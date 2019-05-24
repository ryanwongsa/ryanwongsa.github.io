---
title: "Review of MRI of the lungs"
published: false
---

# Notes on Lung MRI

## 1. Main Lung MRI Challenge

> Magnetic resonance imaging (MRI) of the lung has been a challenge due to limitations such as low proton density in the lung and the fast signal decay due to susceptibility artefacts at air-tissue interfaces

### 1.1. Physical lung properties relevant to MRI

Important tissue properties of lung MRI:

- Low density
- Susceptibility difference between tissue and air
<br>
Healthy lungs have tissue density = 0.1 g/cm^3 which is ~10 fold lower than in other tissues


### 1.2 Challenges in MRI of lung

*Signal-to-noise ratio (SNR) makes structural proton MRI of lung microstructure challenging.*
<br>
**Solution**: signal averaging but increases image acquisition times to be longer than 10 minutes per data set.
<br><br>
*Gradient echo MRI of lung parenchyma becomes highly challenging*
<br>
**Solution**: pulse sequences with short echo times

Low-field MRI 0.5 T or less advantageous for lung MRI but requires signal average to recover signal to noise, so it requires longer imaging times. Not a good solution from a clinical view.

## 2, 1.5 T vs 3 T MRI

(T - Tesla) Difference affects the strength of the MRI magnet which affects the signal that is received from the body.

## 3. Breath-hold MRI

Less than 20 seconds duration, either end-expiration or full inspiration.

**Expiration**

- higher signal intensity from lung parenchyma
- Density of protons in a voxel of parenchyma is increased
- Bulk volume magnetic susceptibility difference is reduced as air is expelled
<br><br>

**Inspiration**

- High contrast to noise between pulmonary vessels or nodules
- Darker background

## 4. Respiratory Motion

Reliable acquisition of images at defined stages during respiration requires respiratory / spirometric triggering and gating.

Synchronisation of the image acquisition to the respiratory cycle requires the acquisition of a signal that is proportional to the respiratory state of the lung. Methods of synchronisation:

- External hardware
- MR signal of lung itself
<br><br>

Pneumatic bellows are provided by MR manufacturers.

Alternatively, navigator echoes can be applied to measure the breathing motion directly via MRI. Either a dedicated 90-180° spin echo excitation or a pencil beam readout is used to measure the MR signal orthogonal to the base of the diaphragm.

A sub-second self-navigator scan can also be integrated into the imaging sequence to provide the motion information. Thus, the motion of the diaphragm is detected and can be used for retrospective gating of scans during the free-breathing cycle.

A navigator signal can also be extracted from free-breathing non-gated two-dimensional (2D) lung images as long as the acquisition time is fast compared with the motion of the diaphragm.


## 5. T1-weighted and T2-weighted scans

> *Time to Echo (TE)* - time between the delivery of the RF pulse and the receipt of the echo signal

> *Repetition Time  (TR)* - the amount of time between successive pulse sequences applied to the same slice

**T1-weighted images** are produced by using short *Time to Echo* (TE) and *Repetition Time* (TR) times.

- T1 (longitudinal relaxation time) is the time constant which determines the rate at which excited protons return to equilibrium.
- A measure of the time taken for spinning protons to realign with the external magnetic field.
<br><br>

**T2-weighted images** are produced by using longer TE and TR times.

- T2 (transverse relaxation time) is the time constant which determines the rate at which excited protons reach equilibrium or go out of phase with each other.
- Measure of the time taken for spinning protons to lose phase coherence among the nuclei spinning perpendicular to the main field

## 6. How MRI works

1. Base magnetic field which is always on aligns all the tiny hydrogren nucleus (protons) in the body which have a magnetic moment (think of earth and magnetic poles / bar magnet).
2. Then radio frequency magnetic pulses are sent from the machine which causes the tiny magnets in the body to move out of alignment temporarily.
3. As they relax back into alignment with the magnetic field, they induce a current within a magnet which is detected as a signal from the hydrogen atoms.
4. Different tissues align / relax at different rates.

## 7. Method for the assessment of pulmonary (lung) diseases

- Chest radiography
- Computed tomography (CT)
- MRI

## 8. Advantage of MRI

- excellent soft tissue contrast and functional information
- multiple and repeated measurements
- assess motion of **perfusion** (passage of fluid) of the **thoracic** (chest) organs

## 9. Proposed Protocol expected to be met by MRI of the lung (as of 2012)

Base protocol to answer questions of staging of malignancy, evaluation of pulmonary vessels and perfusion etc (*more reading required*).

Additional procedure protocols are required for emergency conditions such as acute pulmonary embolism (*more reading required*).

**Current duration standards**: 15 minutes for basic protocol or emergency

**Additional Time**: 5-15 minutes for protocol extensions (tumour necrosis detection, pleural reaction/carcinosis, dynamic contrast-enhanced MRI, visualisation of respiratory motion) 

## 10. Challenges of Lung MRI

### 10.1 Increase of lung density

- collapse of lung with atelectasis
- fluid accumulation inside
- alveolar space and/or the lung interstitium
- fluid and cellaccumulation in infiltrates or growth of soft tissue

### 10.2 Decrease of lung density

- overinflation due to air-trapping
- enlargement of air spaces in emphysema

**Optimal lung MRI conditions**: patient can keep a breath-hold for 20 seconds or perfect gating/triggering (*more reading required*)

## 11, Areas of Applications of Lung MRI (*additional reading required*)

- Pathological conditions with increased signal intensity
- Vessels and lung perfusion disorders
- Airways and lung ventilation disorders
- Others ranging from pleural pathology to mediastinal involvement

## 12. Challenges with Quality Image Acquisition

Image quality depends on patient compliance. Therefore requires fast imaging protocols and redundancy to compensate for failed acquisitions.

### 12.1 Stategies for motion compensation

#### 12.1.1 Fast single shot imaging with very short acquisition time

steady state (SSFP) or partial Fourier single shot sequences (HASTE) allow for rapid acquisition of 10 slices with breath-hold for lower than 10 seconds or ven free breathing

#### 12.1.2 Respiratory gating/triggering of fast spin echo techniques

Gated or triggered acquisition increases imaging time but provides better spatial resolution and soft tissue contrast.

- A radial read-out scheme of the k-space improves the robustness against motion artefacts.
- Cardiac triggering increases acquisition time but may be helpful

## 13. Scenarios of MRI First Choice Modality

- Cystic fibrosis
- Acute pulmonary embolism in young or pregnant patients
- Central mass with atelectasis and pleural effusion (alternative or inconjunction)
- Pneumonia in young patients (alternative or inconjunction)

### 13.1 CT First Choice over MRI

- Emphysema/COPD
- Interstitial lung disease

## 14. Types of MRI Sequencing (*More reading required*)

- Spoiled gradient echo (FLASH, SPGR, FFE)
- Balanced steady state free precession (bSSFP—TrueFISP, FIESTA, BFE)
- Single-shot fast spin echo (RARE/HASTE, Turbo FSE)
- RF coils and parallel imaging
- Ultra-short echo time (UTE) imaging
- Oxygen-enhanced 1H MRI

# References

1. [MRI of the lung (1/3): methods](https://link.springer.com/article/10.1007/s13244-012-0176-x)
2. [MRI of the lung (2/3). Why … when … how?](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3481084/)
3. [MRI of the lung (3/3)—current applications and future perspectives](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3481076/)
4. [MRI of the Lung – ready … get set … go!](https://pdfs.semanticscholar.org/22d0/f422cdc7f64732876480cdd59544c5fe44ee.pdf)
5. [Magnetic Resonance Imaging (MRI) of the Brain and Spine: Basics](http://casemed.case.edu/clerkships/neurology/web%20neurorad/mri%20basics.htm)
6. [1.5T versus 3T MRI](https://www.scanmed.com/single-post/2017/04/27/15T-versus-3T-MRI)
