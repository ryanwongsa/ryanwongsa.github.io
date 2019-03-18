---
title: "New Methods for Improved MRI of Lung Diseases"
published: true
---

# 1. Description

## 1.1 Background

"Lung diseases are the second most common cause of death and lead to substantial degradation of quality of life in suffers. Improved tools for diagnosing and monitoring lung disease will provide better patient characterisation and enable more appropriate treatment options to be selected. Recent work by the supervisory team has shown that new magnetic resonance imaging (MRI) methods can provide information on the structure and function of the lung and that these methods are sensitive to disease and to the impact of treatment. However, despite these developments, current lung MRI is subject to poor image quality, which limits our ability to extract detailed information."

## 1.2 Research Aims

"This project will address some of the key current limitations of lung MRI. Breathing motion degrades image quality by causing changes in the location of important structures and introduces unwanted signals in the images. Heart pulsation leads to additional unwanted signal fluctuations. These signals will be modelled and removed from time series of MR images in order to improve the detection of detailed structures. The enhanced information content within the image time series will then be modelled to extract functional information, including lung ventilation and perfusion. These methods will be applied to existing MRI data available in conditions including lung cancer and cystic fibrosis."

# 2. Reading Notes

## 2.1 Respiratory motion correction

### 2.1.1 Respiratory motion correction techniques:

- **Tracking methods (tracks a single or few targets)**
    
- **Motion Estimation techniques (dense motion fields)** - Some approaches include modelling patient-specific breathing motion to a simpler respiratory surrogate signal before the treatment, auto adaptive motion modelling framework

### 2.1.2 Stages of motion models

1. **Model calibration** - Aquring the imaging data (e.g. surrogate signals)
2. **Model formation** - Using the imaging data create a model
3. **Application phase** - During treatment apply the corresponding model for motion estimation.

<br>

## 2.2. Other Areas of MRI Motion Correction (Brain MRI Motion Correction)

Suppress motion artefacts from brain MR imaging using data-driven deep learning approaches. Previous approaches used a CNN which was trained on simulated motion corrupted images to identify and suppress artefacts due to the motion.

## 2.3 Terminology

**thorax** - part of the body of a mammal between the neck and the abdomen

**MR-thermometry** - process of measuring temperature

**HIFU** - High-intensity focused ultrasound

**transducer** - a device that converts variations in a physical quantity, such as pressure or brightness, into an electrical signal, or vice versa.

**ablate** - remove (body tissue) surgically

**MR linear accelerators** - Used to precisely locate tumours, tailor the shape of X-ray beams in real time and accurately deliver doses of radiation to moving tumours

**Manifold alignment** - techniques establish correspondences between multiple related datasets, which are not directly comparable in high/dimensional space, by aligning the low-dimensional manifold structure.

**sagittal** - relating to or denoting the suture on top of the skull which runs between the parietal bones in a front to back direction.


# 3. Coded Attempt

An attempt at recreating a "breadth held" images using the a time sequence of slices of MR Images of a patient which has breathing motion. Testing the use of U-Net deep neural network artitecture to accept an input of a sequence of MRI slices with a patient breathing and recreate a "breath held" image as its output.

Dataset obtained from: [https://zenodo.org/record/55345](https://zenodo.org/record/55345)

*4 Patients (length of 40 images in a time sequence for each slice)*

#### 1. Imports

```python
import torch
from glob import glob
import torchvision
from torch.utils.data import Dataset, DataLoader
from torchvision import datasets, models, transforms, utils
from PIL import Image
from torch import optim
from tqdm import tqdm

import matplotlib.pyplot as plt
import matplotlib.animation as animation
from IPython.display import HTML
import numpy as np
np.set_printoptions(precision=2, suppress=True)

import nibabel as nib
import os
import re

import warnings
warnings.filterwarnings("ignore")
```

#### 2. Dataset Loader


```python
class MedicalDataset(Dataset):
    def __init__(self, train_dir, target_dir, transform=None):
        self.numbers = re.compile(r'(\d+)')
        self.train_dir = train_dir
        self.target_dir = target_dir
        self.train_images = glob(os.path.join(self.train_dir, '*.nii.gz'))
        self.train_images = sorted(self.train_images, key=self.numericalSort)

        self.target_images = glob(os.path.join(self.target_dir, '*.nii.gz'))
        self.target_images = sorted(self.target_images, key=self.numericalSort)
        self.transform = transform

    def numericalSort(self, value):
        parts = self.numbers.split(value)
        parts[1::2] = map(int, parts[1::2])
        return parts
    
    def __len__(self):
        return len(self.target_images)

    def __getitem__(self, idx):
        target_loc = self.target_images[idx]
        target_id = target_loc.split('/')[-1].split('.')[0].split('\\')[-1]
        
        corr_train_images_name = target_id.split("_")
        corr_train_images_name = corr_train_images_name[0]+'_'+corr_train_images_name[1]
        patient_specs = target_loc.split('/')[-1].split('.')[0].split('\\')[:-1]
        
        train_patient_dir = self.train_dir.replace('**', patient_specs[1], 1)
        train_patient_dir = train_patient_dir.replace('**', patient_specs[2], 2)
        
        
        train_images = glob(os.path.join(train_patient_dir,corr_train_images_name+'_*.nii.gz'))
        train_images = sorted(train_images, key=self.numericalSort)
        
        target_image = nib.load(target_loc).get_fdata()
        target_max = target_image.max()
        target_image = Image.fromarray(np.uint8(target_image/target_max*255))
        if self.transform!=None:
            target_image = self.transform(target_image)
        
        train_images_arr = []
        train_ids = []
        for train_image in train_images:
            
            image = nib.load(train_image).get_fdata()
            image = Image.fromarray(np.uint8(image/target_max*255))
            if self.transform!=None:
                image = self.transform(image)

            train_id = train_image.split('/')[-1].split('.')[0].split('\\')[1]
            train_images_arr.append(image)
            train_ids.append(train_id)

        
            
        return target_image, target_id,train_images_arr, train_ids, patient_specs[1]
```

#### 3. U-Net Model


```python
# Source: https://github.com/milesial/Pytorch-UNet/blob/master/unet/unet_parts.py
import torch
import torch.nn as nn
import torch.nn.functional as F

class double_conv(nn.Module):
    def __init__(self, in_ch, out_ch):
        super(double_conv, self).__init__()
        self.conv = nn.Sequential(
            nn.Conv2d(in_ch, out_ch, 3, padding=1),
            nn.BatchNorm2d(out_ch),
            nn.ReLU(inplace=True),
            nn.Conv2d(out_ch, out_ch, 3, padding=1),
            nn.BatchNorm2d(out_ch),
            nn.ReLU(inplace=True)
        )

    def forward(self, x):
        x = self.conv(x)
        return x


class inconv(nn.Module):
    def __init__(self, in_ch, out_ch):
        super(inconv, self).__init__()
        self.conv = double_conv(in_ch, out_ch)

    def forward(self, x):
        x = self.conv(x)
        return x


class down(nn.Module):
    def __init__(self, in_ch, out_ch):
        super(down, self).__init__()
        self.mpconv = nn.Sequential(
            nn.MaxPool2d(2),
            double_conv(in_ch, out_ch)
        )

    def forward(self, x):
        x = self.mpconv(x)
        return x


class up(nn.Module):
    def __init__(self, in_ch, out_ch, bilinear=True):
        super(up, self).__init__()

        if bilinear:
            self.up = nn.Upsample(scale_factor=2, mode='bilinear', align_corners=True)
        else:
            self.up = nn.ConvTranspose2d(in_ch//2, in_ch//2, 2, stride=2)

        self.conv = double_conv(in_ch, out_ch)

    def forward(self, x1, x2):
        x1 = self.up(x1)
        
        # input is CHW
        diffY = x2.size()[2] - x1.size()[2]
        diffX = x2.size()[3] - x1.size()[3]

        x1 = F.pad(x1, (diffX // 2, diffX - diffX//2,
                        diffY // 2, diffY - diffY//2))

        x = torch.cat([x2, x1], dim=1)
        x = self.conv(x)
        return x


class outconv(nn.Module):
    def __init__(self, in_ch, out_ch):
        super(outconv, self).__init__()
        self.conv = nn.Conv2d(in_ch, out_ch, 1)

    def forward(self, x):
        x = self.conv(x)
        return x
    
class UNet(nn.Module):
    def __init__(self, n_channels):
        super(UNet, self).__init__()
        self.inc = inconv(n_channels, 64)
        self.down1 = down(64, 128)
        self.down2 = down(128, 256)
        self.down3 = down(256, 512)
        self.down4 = down(512, 512)
        self.up1 = up(1024, 256)
        self.up2 = up(512, 128)
        self.up3 = up(256, 64)
        self.up4 = up(128, 64)
        self.outc = outconv(64, 1)

    def forward(self, x):
        x1 = self.inc(x)
        x2 = self.down1(x1)
        x3 = self.down2(x2)
        x4 = self.down3(x3)
        x5 = self.down4(x4)
        x = self.up1(x5, x4)
        x = self.up2(x, x3)
        x = self.up3(x, x2)
        x = self.up4(x, x1)
        x = self.outc(x)
        return F.tanh(x)
```

#### 4. Dataset Path


```python
dataset_dir = "../dataset/medical/"
patient_dir = "**/**/"
train_images = "dyn/images/sag/"
target_images = "bhs/images/sag/"

train_dir = dataset_dir+patient_dir+train_images
target_dir = dataset_dir+patient_dir+target_images
```

#### 5. Hyperparameters


```python
img_size = 215 # actual size (215, 288)
mean = [0.5]
std=[0.5]
num_workers = 0
batch_size = 1
learning_rate = 0.01
image_time_range = 40
```

#### 6. Loading Model


```python
data_transform = transforms.Compose([
        transforms.Resize(img_size),
        transforms.ToTensor(),
        transforms.Normalize(mean=mean,
                             std=std)
    ])

medical_dataset = MedicalDataset(train_dir, target_dir,data_transform)
dataloader = DataLoader(medical_dataset, batch_size=batch_size,
                        shuffle=False, num_workers=num_workers)

unet = UNet(image_time_range)
unet = unet.cuda()
# optimizer = optim.Adam(unet.parameters(),lr=learning_rate)
criterion = torch.nn.MSELoss()
```

#### 7. Training Model

**Training dataset** - VolunteerA,VolunteerB,VolunteerC

**Test dataset** - VolunteerD


```python
test_loss = -1
training_loss_arr = []
test_loss_arr = []
```


```python
def train_model(model, optim, crit, dl, epoches):
    for j in tqdm(range(epoches)):
        for i, (target_img, target_id, train_imgs, train_ids, patient_id) in enumerate(dl):

            if "volunteerD" in patient_id:
                model.eval()
                input_image = torch.stack(train_imgs).view(-1,image_time_range,target_img.size(2),target_img.size(3)).cuda()
                output_image = model(input_image)
                target_img = target_img.cuda()
                test_loss = crit(/assets/images/medical-images/output_image,target_img)
                continue
            model.train()
            optim.zero_grad()
            input_image = torch.stack(train_imgs).view(-1,image_time_range,target_img.size(2),target_img.size(3)).cuda()
            output_image = model(input_image)
            target_img = target_img.cuda()
            loss = crit(/assets/images/medical-images/output_image,target_img)
            loss.backward()
            optim.step()
    #     print(j,loss.item(),test_loss.item())
        training_loss_arr.append(loss.item())
        test_loss_arr.append(test_loss.item())
```


```python
optimizer = optim.Adam(unet.parameters(),lr=0.01)
train_model(unet, optimizer, criterion, dataloader, 20)
```

    100%|██████████| 10/10 [04:53<00:00, 29.16s/it]
    


```python
optimizer = optim.Adam(unet.parameters(),lr=0.001)
train_model(unet, optimizer, criterion, dataloader, 20)
```

    100%|██████████| 10/10 [04:50<00:00, 29.08s/it]
    


```python
optimizer = optim.Adam(unet.parameters(),lr=0.0001)
train_model(unet, optimizer, criterion, dataloader, 20)
```

    100%|██████████| 10/10 [04:56<00:00, 29.55s/it]
    

#### 8. Evaluation


```python
plt.scatter(range(len(training_loss_arr)),training_loss_arr, label="Training loss")
plt.scatter(range(len(test_loss_arr)),test_loss_arr, label="Test loss")
plt.ylim(0,0.02) # adding to remove outlier in beginning
plt.legend()
```


![png](/assets/images/medical-images/output_20_1.png){:height="100%" width="100%"}


#### 8.1 Results on unseen Test set Volunteer D


```python
for i, (target_img, target_id, train_imgs, train_ids, patient_id) in enumerate(dataloader):
    if "volunteerD" not in patient_id:
        continue
    unet.eval()
    input_image = torch.stack(train_imgs).view(-1,image_time_range,target_img.size(2),target_img.size(3)).cuda()
    output_image = unet(input_image)
    target_img = target_img.cuda()
    
    f, axs = plt.subplots(4,10,figsize=(40,10))
    f.suptitle('Input Sequence:'+patient_id[0])
    counter_i=0
    counter_j=0
    for t_img in train_imgs:
        diff_res2 = abs((t_img[0][0].cpu().detach().numpy()+1.0)/2.0-(target_img[0][0].cpu().detach().numpy()+1.0)/2.0)
        im2= Image.fromarray(diff_res2*255.0)
        axs[counter_i][counter_j].imshow(im2, cmap='gray')
        axs[counter_i][counter_j].axis('off')
        counter_j+=1
        if counter_j%10==0:
            counter_j=0
            counter_i+=1
    f.show()
    
    f2, axs2 = plt.subplots(1,3,figsize=(20,10))
    
    target_display = Image.fromarray(np.uint8((target_img[0][0].cpu().detach().numpy()+1.0)/2.0*255.0))
    axs2[0].imshow(target_display, cmap='gray')
    axs2[0].set_title("Target")
    axs2[0].axis('off')
    
    res_img = Image.fromarray(np.uint8((/assets/images/medical-images/output_image[0][0].cpu().detach().numpy()+1.0)/2.0*255.0))
    axs2[1].imshow(res_img, cmap='gray')
    axs2[1].set_title("Predicted")
    axs2[1].axis('off')
    
    
    diff_res = abs((/assets/images/medical-images/output_image[0][0].cpu().detach().numpy()+1.0)/2.0-(target_img[0][0].cpu().detach().numpy()+1.0)/2.0)
    diff_res = Image.fromarray(np.uint8(diff_res*255))
    axs2[2].imshow(diff_res, cmap='gray')
    axs2[2].set_title("Difference")
    axs2[2].axis('off')
    plt.show()
```

#### Sample of results from unseen dataset from Volunteer D

![png](/assets/images/medical-images/output_22_0.png){:height="100%" width="100%"}



![png](/assets/images/medical-images/output_22_1.png){:height="100%" width="100%"}



![png](/assets/images/medical-images/output_22_2.png){:height="100%" width="100%"}



![png](/assets/images/medical-images/output_22_3.png){:height="100%" width="100%"}



![png](/assets/images/medical-images/output_22_4.png){:height="100%" width="100%"}



![png](/assets/images/medical-images/output_22_5.png){:height="100%" width="100%"}

<center>...</center>

#### 9. Mini Analysis / Conclusion

- With a limited dataset and unadjusted model, the model is able to recreate a single image for the 40 images but has signficant blurring effect and sometimes create unwanted artificants. 
- Model still requires a lot fine tuning, adjustments and a larger / adjusted dataset.
- An improved approach to peform real-time image motion correction will require a LSTM / RNN based approach to feed in one image at a time and keep a memory of a 'simplified' history of the past images.
- Alternatively the LSTM based approach may have applications of creating a motion estimation model to predict patterns in breathing motion and possibly extending on existing research.

<br>
## References

1. [Autoadaptive motion modelling for MR-based respiratory motion estimation](https://www.medicalimageanalysisjournal.com/article/S1361-8415(16)30082-2/fulltext)

2. [MoCoNet: Motion Correction in 3D MPRAGE images using a Convolutional Neural Network approach](https://arxiv.org/ftp/arxiv/papers/1807/1807.10831.pdf)

## Useful Future Reading

3. [Respiratory motion models: A review](https://www.medicalimageanalysisjournal.com/article/S1361-8415(12)00134-X/abstract) (Unable to access) 
4. [On using an adaptive neural network to predict lung tumor motion during respiration for radiotherapy applications](https://www.researchgate.net/publication/7300769_On_using_an_adaptive_neural_network_to_predict_lung_tumor_motion_during_respiration_for_radiotherapy_applications) (Unable to access) 

