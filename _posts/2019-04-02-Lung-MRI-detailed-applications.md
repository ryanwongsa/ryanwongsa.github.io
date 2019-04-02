---
title: "Lung MRI Detailed Applications"
published: true
---

# Summary of Paper: MRI of the Lung–ready...get set... go! 

## Introduction

The challenges of MRI in particular with lung MRI is the low proton density in the lung and fast signal decay caused by air-tissue artifacts.

The benefits of MRI are that it is better than X-ray and matches CT in the detection of *nodular* and *infiltrative* lung diseases without radiation exposure to patients.

> **lung nodules**: a small growth on the lung. noncancerous nodule (*benign*) and cancerous lung nodule (*malignant*)

> **infiltrative**: diffusion or accumulation (in a tissue or cells) of foreign substances or in amounts in excess of the normal

## Clinical Method

The key requirement for hardware and sequence design is that fast sequences must be receive with as much lung signal as possible before the signal is decayed. 

The preference is for *breath-hold imaging* with *high spatial resolution* and *short echo times (TE)*.

> **Spatial Resolution**: number of pixels utilized in construction of a digital image

> **Time to Echo (TE)**: time between the delivery of the radio frequency pulse and the receipt of the echo signal

MRI is enhanced for robust imaging by combining with other imaging techniques (see [here](https://static.healthcare.siemens.com/siemens_hwem-hwem_ssxa_websites-context-root/wcm/idc/groups/public/@global/@imaging/@mri/documents/download/mdaw/mtq4/~edisp/lung_mri_biederer-00012400.pdf) for further reading):
- Parallel imaging (integrated Parallel Acquistion Techniques)
- High temporal resolution MR angiography (time-resolved angiography with stochastic trajectories)
- Rotating phase encoding
- Navigator technology (prospective acquistion correction)

MRI Scanners such as the Siemens MAGNETOM come with protocols which are optimised for imaging lung diseases. The protocols are able to cover different aspects of lung pathology ranging from general purpose to specific sequence combinations such as:
- imaging *thoracic masses* and high resolution *angiography*
- functional imaging with dynamic first pass lung *perfusion* imaging

> **Mediastinal tumors** (*same as thoracic masses?*) : benign or cancerous growths that form in the area of the chest that separates the lungs.

> **Angiography**: a medical imaging technique used to visualize the inside, or lumen, of blood vessels and organs of the body, with particular interest in the arteries, veins, and the heart chambers.


|                             | Description  |  Room Times  | 
|-----------------------------|---|---|
| **Basic Protocol**          |   |   ~15 min  |  
| **Contrast-Enhanced Study** |   |   ~20 min  |  
| **Comprehesive Study**      | perfusion imaging, angiography, post-contrast volumetric imaging   |   <30 min  |   
| **Non-Compliant Patients**  | TrueFISP & navigator-triggered acquisition with BLADE T2-TSE  |   |   

*In order for robust imaging against **cardiac pulsation** and **respiratory motion**, the acquisition times are kept short and multi-breathhold-imaging or respiratory trigger is used.*

*WIP*

# References
