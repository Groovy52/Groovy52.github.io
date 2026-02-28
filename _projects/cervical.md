---
layout: page
title: Unreadable Image Classification
description: A Radiomics-based Unread Cervical Imaging Classification Algorithm
importance: 3
category: work
---

## 1. Overview

This study proposes a **radiomics-based pre-screening framework** to automatically identify and exclude unreadable medical images prior to deep learning model training.
Unlike conventional research that focuses on improving network architectures, this work demonstrates that **input data quality is a primary determinant of AI performance.**

By filtering diagnostically unreliable images before training, the framework reduces label noise and stabilizes feature learning, ultimately improving model accuracy and reliability in clinical environments.

---

## 2. Motivation

Real-world clinical datasets inevitably contain images degraded by:
- Motion artifacts
- Low contrast or acquisition errors
- Partial anatomical capture
- Severe noise or distortion

Such samples are often **difficult even for clinicians to interpret**, yet they remain in training datasets and introduce:
- Implicit label noise
- Feature ambiguity
- Unstable decision boundaries

Rather than increasing model complexity, this study explores a data-centric question:
**"Can AI performance improve simply by learning from diagnostically reliable data?"**

This perspective aligns with the shift toward **Data-Centric AI**, where dataset design becomes as critical as model architecture.

---

## 3. Proposed Approach

Figure 2 illustrates the overall workflow of the proposed method for automatically classifying unreadable cervical images.
The algorithm evaluates whether an image is diagnostically usable by quantifying brightness, blur, and anatomical positioning.
All metric values are normalized between **0 (0%) and 1 (100%)** using the full distribution of readable-image data.

#### 3-1. Classification Indicators

Subtle morphological variations in cervical epithelial cells significantly influence diagnostic interpretation.
Therefore, pixel-level image quality differences can directly affect readability.
As shown in **Fig. 2**, three criteria are defined to classify unreadable images:

**1) Brightness**

The first indicator is the **average brightness value** of the image.
- Pixel intensities are summed and divided by the total pixel count to compute the mean brightness.
- Histograms are used to simultaneously visualize readable vs. unreadable datasets and compare their distributions.
- The acceptable brightness range is determined statistically using **t-test analysis and histogram-based thresholding.**
- Images with extreme brightness deviations that hinder visual interpretation are classified as unreadable.

**2) Blur**

The second indicator is the **degree of blur**, defined as the sharpness of the image.
- Blur is measured using the **Laplacian operator**, a second-order derivative widely used for edge detection.
- Lower values indicate blurred images; higher values indicate clearer images.
- Based on statistical analysis of readable images, the acceptable threshold is set within the **densely distributed upper 13% range of blur values.**
- This relative definition reflects the dataset-dependent nature of blur measurement.

**3) Region Adequacy (Cervical Position)**

The third indicator evaluates whether the cervical os region is properly captured.
- A template image with the cervical region centered is defined.
- Similarity between the template and an input image is computed using **Euclidean distance**

Examples of unreadable cases identified using these criteria are presented in Figure 3, including:
- excessively bright or dark images,
- blurred images,
- images where the cervical region is improperly captured.

#### 3-2. Classification Procedure

The full decision process is depicted in **Figure 4** and proceeds as follows:

**1) Input Stage**
A cervical image is provided to the algorithm.

**2) Threshold-Based Evaluation**
The image is tested against predefined thresholds for brightness, blur, and Euclidean-distance-based region adequacy.
Any image outside these ranges is classified as unreadable and excluded from model input.

**3) Cause-Oriented Categorization**
Each of the three indicators has a binary outcome (acceptable / unacceptable), producing:

2×2×2=8 unreadable-case combinations

The algorithm assigns each rejected image to one of these eight categories and outputs a message describing the specific cause.
Images satisfying all criteria are retained and recommended for expert diagnosis.

**Normalized Indicator Interpretation**

Within the normalized classification standard:
- Brightness: Values closer to 1 indicate higher intensity.
- Blur: Larger values indicate clearer images.
- Region (Euclidean Distance): Smaller values indicate better alignment of the cervical region.

---

## 4. Implementation Details

#### 4-1. Data Acquisition

The dataset was collected specifically for this study.
Due to hospital privacy regulations, it cannot be publicly released.
Access may be granted upon institutional agreement.

**LiDAR Sensor Configuration**

A total of 10,985 point cloud scenes were collected at the Veterans Health Service Medical Center between 2022 and 2023 using flash-type LiDAR sensors (NSL-1110AV, NANOSYSTEMS Corp., Gyeongsan, South Korea) operating at 5 frames per second (FPS).

LiDAR systems are broadly categorized into scanning and flash types. Unlike scanning LiDAR, which sequentially emits laser beams, flash LiDAR captures the entire scene simultaneously, providing robust and stable sensing for fixed indoor installations with simpler hardware and improved durability. 

Although flash LiDAR offers lower spatial resolution and a narrower field of view than scanning LiDAR, it is well suited for hospital environments, where reliable operation and privacy-preserving depth sensing are more important than long-range perception.

Each captured point cloud had a spatial resolution of 320 × 240 voxels with a sensing depth of up to 12 m.

**Sensor Deployment in a Hospital Environment**

A total of 19 LiDAR sensors were installed in fixed positions across four representative hospital zones: Radiology Department, Laboratory Medicine, Inpatient Ward A, and Inpatient Ward B

These locations were deliberately selected to ensure:
- spatial diversity of indoor geometries
- varied human–robot interaction scenarios
- realistic collision-risk environments for AMR deployment.

This multi-zone configuration enabled the dataset to capture heterogeneous clinical workflows and dynamic object interactions, producing a representative benchmark for hospital navigation systems.

#### 4-2. Data Preprocessing and Augmentation Process

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/sald-net_fig5.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Fig 5. Preprocessing steps for raw point cloud data. (a) Raw point cloud. (b) Voxel-based downsampling. (c) RANSAC-based filtering; red points indicate filtered wall points. (d) Final denoised point cloud after statistical outlier removal. Background points are shown in black; object points and bounding boxes are color-coded by object class
</div>

To stabilize noisy hospital point clouds, a four-stage pipeline was designed:
- **Voxel-based downsampling for density normalization
Voxel-based downsampling was applied with a voxel size of 0.25 m:**
    - projects points into voxel grids,
    - replaces points within each voxel by their centroid,
    - reduces point density while maintaining global geometry.
      
- **RANSAC filtering to remove structural planes
To separate foreground objects from structural background, a RANSAC-based plane fitting algorithm was applied with:**
    - distance threshold: 0.2 m
    - minimum sampled points: 3
    - maximum iterations: 500
    
- **Statistical outlier removal for sensor noise reduction
To suppress measurement noise, statistical filtering was performed using:**
    - number of neighbors: 20
    - standard deviation ratio: 1.5

**Data Augmentation**
The preprocessed dataset of 10,985 scenes is split into 6,591 training, 2,197 validation, and 2,197 test samples. To mitigate class imbalance among robots, people, beds, and wheelchairs, GT sampling is adopted, a widely used data augmentation method originally introduced in SECOND.

Specifically, annotated objects from a database are inserted into other training scenes. This augmentation increases the number of robots, beds, and wheelchairs, as summarized in Table 1.

To avoid object overlaps, the z-coordinate of each inserted object is set to the lowest height among existing objects.
For horizontal positioning, inserted objects are placed beyond the largest existing x or y coordinates in the scene, depending on the spatial distribution of existing objects.

If existing objects are located along the positive x-axis, new objects are placed further along the y-axis, and vice versa.
Finally, scenes are normalized by centering the 3D coordinate system at (0, 0, 0), ensuring consistent spatial scaling across the dataset.

#### 4-3. Software

**Framework**
- The proposed network and all comparative models were implemented using PyTorch 1.9.1.
- Baseline methods in Table 2 were reproduced using the OpenPCDet toolbox, a widely adopted open-source framework for 3D object detection.
- Except for adapting configurations to the hospital-specific dataset, all models retained their default settings provided in the official repository to ensure fair comparison.

**Training**
- Optimization was performed using the Adam optimizer.
- Batch size: 4
- Training epochs: 100
- A cosine annealing learning rate schedule was adopted:
  - Initial learning rate: 0.001
  - Increased gradually to a peak of 0.01 during the first 40% of training steps
  - Decreased smoothly to a minimal value over the remaining 60% of training.

- Online augmentation techniques included:
  - Random flipping
  - Random rotation in the range of −45° to 45°
  - Scaling with factors between 0.95 and 1.05

**Hardware**
- Training was conducted on an NVIDIA GeForce RTX 3090 GPU.
- The environment utilized CUDA 11.1 for GPU acceleration.
  
---

## 5. Results

SALD-Net significantly outperformed the baseline Part-A2 detector:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/sald-net_fig6.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Qualitative comparison of 3D object detection results from baseline methods and SALD-Net on the test set. Background points are shown in black, and object points are color-coded by class. Top down BEV images provide overall scene context, while zoomed-in 3D RoIs highlight mispredicted objects. Detection errors—misclassified, missed, or over-detected—are indicated by arrows. Ground-truth and predicted objects are shown in red and aqua-blue bounding boxes, respectively
</div>


- **3D mAP: 89.08%** 
- Overall improvement: **+19.56%** 
- Wheelchair detection: +22.85%p 
The model successfully separated objects that previous detectors failed to distinguish in cluttered hospital scenes


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/sald-net_Table2.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Table 2. Qualitative Performance comparison of 3D detection on our test dataset. The evaluation metrics are BEV AP(%), 3D AP(%) with an IoU threshold of 0.5 for robot, person, bed, and wheelchair classes, and inference speed measured in FPS
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/sald-net_Table3.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Table 3.  Ablation study results on the test set. Evaluation of the impact of data augmentation and self-attention mechanisms in different net
work modules. AUG: Applying data augmentation for the training set, BAM: backbone-integrated self-attention mechanism, RAM: RoI feature-based self-attention mechanism
</div>

---

## 6. Technical Takeaways

This work demonstrates that:

- Indoor medical environments require **domain-specific perception modeling**
- Global relational reasoning is critical for dense human-object interaction scenes
- Dataset realism is as important as model architecture for safety-critical robotics

---

## 7. Future Work

Future extensions include:

- Multi-sensor fusion for improved spatial robustness
- Deployment-oriented optimization for real-time AMR navigation
- Transfer of the preprocessing pipeline to radar-based perception systems

