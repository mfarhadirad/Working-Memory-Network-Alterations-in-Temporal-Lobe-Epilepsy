# Working Memory Network Alterations in Temporal Lobe Epilepsy: An fMRI Investigation
## Project Overview
This educational project demonstrates a complete fMRI analysis pipeline to investigate working memory processing in Temporal Lobe Epilepsy (TLE) using SPM12. The analysis implements preprocessing, first-level general linear modeling, group-level statistics, and region-of-interest analysis on a subset of the UNAM Temporal Lobe Epilepsy Dataset.
Note: This analysis utilized 7 subjects (3 controls, 4 TLE patients) for educational demonstration purposes. Sample size limitations preclude definitive clinical conclusions but adequately illustrate methodological principles.
## Dataset Description
The analysis utilizes the UNAM Temporal Lobe Epilepsy Dataset, which includes:
•	Participants: 4 TLE patients and 3 healthy controls (educational subset)
•	Task: Sternberg working memory paradigm (Coding, Retention, Test phases)
•	Imaging: 3T fMRI with T1-weighted anatomical scans
•	Data Structure: BIDS-organized with complete event timing files
## Research Objectives
1.	Methodological: Demonstrate a complete SPM12 fMRI analysis pipeline
2.	Analytical: Implement group-level comparison between TLE patients and controls
3.	Technical: Conduct region-of-interest analysis of temporal lobe structures
4.	Educational: Illustrate statistical inference and interpretation in neuroimaging
## Analytical Pipeline
###Preprocessing
  All preprocessing was performed using SPM12 with the following sequence:
  1.	Realignment
    o	Motion correction using six-parameter rigid body transformation
    o	Exclusion criterion: >3mm translation or >3° rotation
    o	Output: Motion parameters for nuisance regression
  2.	Slice Timing Correction
    o	Temporal interpolation to the middle slice reference
    o	Accounted for varying slice numbers (primarily 33 slices) across subjects
    o	TR: 2.0s, Slice order: interleaved	
  3.	Coregistration
    o	Mutual information optimization between functional and anatomical images
    o	Manual origin setting to the anterior commissure when required
    o	Quality control: Visual inspection of temporal lobe alignment
  4.	Segmentation & Normalization
    o	Unified segmentation to MNI space
    o	Tissue classification: GM, WM, CSF
    o	Voxel size: 2×2×2 mm³

    ### Important Note:
      In some studies, we observe an anomaly in the coregistration output. Although the functional data appear normal, the realigned anatomical image seems to be flipped.
      This error will affect later stages like normalization and first level analysis. For this, we need to compare the raw anatomical image and a functional image using the checkreg button in the SPM GUI.
      What we see is that the centers of the images are far away from each other. The problem is not in the data or the script; instead, it is because of the initial alignment with the anatomical image.
      To fix this, you need to Manually set origins for each subject. Specifically, we need to set the origin of all images exactly on the anterior commissure, a bundle of white matter that connects both hemispheres of 
      the brain.

  6.	Spatial Smoothing
    o	Kernel: [4 4 6] mm FWHM anisotropic Gaussian
    o	Rationale: Matched to native voxel dimensions (1.87×1.87×3 mm) for consistent 2-voxel effective smoothing
<img width="619" height="892" alt="image" src="https://github.com/user-attachments/assets/f403b21d-37ae-4814-bd14-cad3408e0499" />
<img width="576" height="830" alt="image" src="https://github.com/user-attachments/assets/dce6cfd6-dc08-4560-8b42-91f482f2d291" />



## First-Level Analysis
 ### Model Specification:
    •	Conditions: Coding, Retention, Test (onsets/durations from Sternberg_events.tsv)
    •	High-pass filter: 128s cutoff
    •	Nuisance regressors: 6 motion parameters + derivatives
 ### Contrasts:
    •	Coding > Baseline: [1 0 0 0 0 0 0 0 0]
    •	Retention > Baseline: [0 1 0 0 0 0 0 0 0]
    •	Test > Baseline: [0 0 1 0 0 0 0 0 0]
    •	Working Memory Network: [1 1 1 0 0 0 0 0 0]
    •	Encoding vs Retrieval: [1 0 -1 0 0 0 0 0 0]
<img width="647" height="932" alt="image" src="https://github.com/user-attachments/assets/625b6a72-efc8-4169-a557-3e4d4d13dd50" />





## Group-Level Analysis
  ### Design: Two-sample t-test
    •	Group 1: Healthy controls (n=4)
    •	Group 2: TLE patients (n=3)
  ### Contrasts:
    •	TLE > Controls: [-1 1]
    •	Controls > TLE: [1 -1]
  ### Statistical Inference:
    •	Threshold: p < 0.01 uncorrected
    •	Extent threshold: 10 voxels
    •	Inference: Cluster-level, peak-level, set-level

<img width="496" height="698" alt="image" src="https://github.com/user-attachments/assets/f48a7673-67c2-4026-ac99-6814fd75eed0" />
<img width="482" height="694" alt="image" src="https://github.com/user-attachments/assets/9b8125fc-382e-43b6-b279-b7f8f8d00a56" />



## Region of Interest Analysis
  ### ROI Definition:
    •	Tool: WFU PickAtlas
    •	Regions: Brodmann areas 20, 21, 22 (inferior, middle, superior temporal gyri)
    •	Mask dilation: 3mm for adequate coverage

<img width="595" height="858" alt="image" src="https://github.com/user-attachments/assets/ede4ccd1-2786-4fea-a377-79bc55e27dcb" />


    
## Statistical Analysis:
    •	Data extraction: MarsBar toolbox
    •	Comparison: Two-sample t-test on mean contrast estimates
    •	Analysis: [h, p, ci, stats] = ttest2(TLE_estimates, Control_estimates)
 <img width="975" height="663" alt="image" src="https://github.com/user-attachments/assets/aa924125-5bd6-4bf8-a11c-cee696e0ae9a" />


## Results
  ### Group-Level Findings
    NOTE: Due to limited sample size (N=7), no statistically significant group differences survived multiple comparisons correction at FWE p < 0.05. At an exploratory threshold (p < 0.01 uncorrected), 
    Several clusters showed differential activation patterns:
    •	Frontal regions: Potential compensatory activation in TLE patients
    •	Temporal regions: Reduced engagement in the TLE group
    •	Parietal areas: Altered working memory network connectivity

## ROI Analysis
    Temporal lobe ROI analysis revealed no statistically significant group differences (p > 0.05), consistent with the limited statistical power of the educational sample.


## Methodological Considerations
  ### Limitations
    1.	Sample Size: Inadequate for definitive clinical conclusions
    2.	Statistical Power: Limited ability to detect subtle group differences
    3.	Multiple Comparisons: Exploratory thresholds increase Type I error risk
    4.	Clinical Heterogeneity: TLE patient characteristics not accounted for
## Technical Strengths
    1.	Comprehensive Pipeline: Complete preprocessing to inference
    2.	Quality Control: Rigorous motion correction and alignment verification
    3.	Anatomically-Informed Analysis: Temporal lobe ROI focus
    4.	Reproducible Methods: BIDS-compliant data organization
## Conclusion
    This project successfully demonstrates a complete fMRI analysis pipeline for investigating working memory in temporal lobe epilepsy. While the limited sample size precludes definitive clinical insights,
    the methodological implementation provides a robust framework for larger-scale studies. The analysis highlights the importance of adequate statistical power, appropriate multiple comparisons correction,
    and anatomically-informed region of interest analysis in clinical neuroimaging research.


## Implementation Notes
  •	Software: SPM12, MATLAB R2023a
  •	Toolboxe: WFU PickAtlas
  •	Computing: Standard workstation implementation
  •	Code Availability: Full analysis scripts available upon request


  <img width="558" height="858" alt="image" src="https://github.com/user-attachments/assets/602f01c5-c56e-41a8-9ce5-0c6f53efc007" />



________________________________________
Wish you bests

# 👨‍💻 Developer
## Mohammad Farhadi Rad

### ☢️Department of Medical Imaging and Radiation Sciences, School of Allied Medical Sciences, Ahvaz Jundishapur University of Medical Sciences, Ahvaz, Iran. 

MSc Radiobiology | BSc Radiology Technology | AI Enthusiast




