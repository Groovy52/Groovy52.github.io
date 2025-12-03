---
layout: post
title: "SALD-Net: Self-Attention-integrated LiDAR-based 3D Object Detection in Crowded Hospital Environments"
date: 2025-09-22
categories: [Publications, 3D Perception, LiDAR]
---

<style>
/* ----------------------------- */
/*         FONT SETTINGS         */
/* ----------------------------- */
body, p, li, h1, h2, h3, h4, h5, h6 {
  font-family: "Noto Sans KR", "Helvetica Neue", Arial, sans-serif !important;
  line-height: 1.7;
  font-weight: 400;
}
code, pre, kbd {
  font-family: "JetBrains Mono", Menlo, Consolas, Monaco, monospace !important;
}

/* ----------------------------- */
/*     SECTION / PARAGRAPH SPACING  */
/* ----------------------------- */

/* 큰 제목 간격 */
h1 {
  margin-top: 65px !important;
  margin-bottom: 30px !important;
}

/* 중간 제목 간격 */
h2 {
  margin-top: 55px !important;
  margin-bottom: 25px !important;
}

/* 소제목 간격 */
h3 {
  margin-top: 40px !important;
  margin-bottom: 20px !important;
}

/* 본문 단락 간격 확대 */
p {
  margin-top: 16px !important;
  margin-bottom: 16px !important;
}

/* 리스트 항목 간격 */
li {
  margin-bottom: 10px !important;
}

/* 이미지 포함 블록 간격 추가 */
p img {
  margin-top: 25px !important;
  margin-bottom: 25px !important;
}

/* 섹션 전체 간격을 더 여유 있게 */
section {
  padding-top: 30px !important;
  padding-bottom: 30px !important;
}
</style>

Accurate 3D object detection from point clouds is essential for autonomous systems, especially in complex indoor environments such as hospitals.  
In this post, I introduce **SALD-Net**, my published work proposing a self-attention-integrated LiDAR-based 3D object detection network tailored for crowded, cluttered, and privacy-sensitive hospital settings.

The paper is published in:

📄 **Signal, Image and Video Processing (SIVP), 2025**  
🔗 *Paper:* https://link.springer.com/article/10.1007/s11760-025-04727-y  
🔗 *Code:* https://github.com/Groovy52/SALD-Net  

---

# 1. Introduction

Accurate 3D object detection from point clouds is crucial for autonomous systems, including autonomous driving, drone navigation, and automated agriculture. In clinical settings, autonomous mobile robots have recently been introduced to mitigate workforce shortages and support healthcare delivery by performing tasks such as medication transport and patient guidance. However, performing **reliable 3D object detection in crowded and cluttered hospital environments** presents significant challenges due to the following issues.

### **Unique Characteristics of Hospital Environments**

1. Hospitals serve a large population of elderly or mobility-impaired patients who often require assistance.  
   As a result, **caregivers or clinical staff frequently accompany patients in close proximity**, leading to **frequent person–wheelchair or person–bed overlapping** in point cloud representations.

2. Hospital rooms and corridors are **narrow and densely populated**, while medical equipment, beds, and carts move continuously throughout the environment.  
   This results in **severe occlusion** and **partial observations** that significantly degrade object visibility.

3. Hospitals enforce stringent **privacy regulations**, making the use of RGB-based detection infeasible.  
   Consequently, robust **LiDAR-only, depth-based perception** is required.

4. Indoor Flash LiDAR sensors often suffer from **noise and low spatial resolution**,  
   which produces **blurred object boundaries** and makes **instance separation** particularly difficult.

Given these characteristics, it is evident that **existing autonomous-driving datasets such as KITTI and Waymo differ substantially from real hospital environments**, both in object categories and spatial interaction patterns.  
Applying conventional models or datasets directly to hospitals therefore leads to **significant performance degradation**.

To address these limitations, we constructed a **hospital-specific LiDAR dataset comprising 10,985 scenes** and developed **SALD-Net**, a self-attention–integrated 3D object detection framework tailored to crowded and cluttered clinical environments.

# 2. Problem Statement
## 1. Domain Gap: Lack of Hospital-Specific Datasets

<p align="center">
  <img src="/publications/sald-net/images/kitti.png" width="85%">
</p>
<p align="center"><em>Figure 1. object classes and scene structure of KITTI</em></p>

Public datasets such as KITTI and Waymo do not contain hospital-relevant objects such as beds, wheelchairs, medical staff, or mobility-assisted patient scenarios.  
As a result, existing models experience severe **domain shift** when applied to **hospital scenes (Figure 3)**.

## 2. Flash LiDAR Noise and Low Resolution

Indoor Flash LiDAR produces frequent outliers, blurred boundaries, and sparse point distributions—causing unstable bounding box estimation. Sparsity of point is illustrated in **Figure 2**.

## 3. Class Imbalance

Hospitals show highly skewed category frequencies, with staff and patients appearing far more often than wheelchairs, beds, or robots.


## 4. Occlusion and Overlap in Dynamic Hospital Scenes

<p align="center">
  <img src="/publications/sald-net/images/lidar_challenges.png" width="65%">
</p>
<p align="center"><em>Figure 2. Illustration of challenges in 3D object detection in hospital environments. Background points are shown in black; object points and bounding boxes are color-coded by object class. (a) Occlusion: A person is partially occluded by a bed. (b) Overlap: Adjacent objects exhibit overlapping regions. (c) Combined: Both occlusion and overlap occur simultaneously. (d) Sparsity: Non-uniform sensor density leads
to incomplete 3D representations. </em></p>

**Crowded corridors** (Indoor structure of hospital as illustrated in **Figure 3**), staff assisting patients, and narrow clinical spaces make **overlapping** and **partial occlusion** unavoidable, making instance separation extremely difficult.

# 3. Methods

## 1. Hospital-Specific LiDAR Dataset Construction

<p align="center">
  <img src="/publications/sald-net/images/complex_hospital.png" width="85%">
</p>
<p align="center"><em>Figure 3. Sensor deployment zones for data collection.</em></p>

We deployed 19 Flash LiDAR sensors across four hospital zones.

## 2. Preprocessing Pipeline for Indoor Flash LiDAR

<p align="center">
  <img src="/publications/sald-net/images/preprocessing_steps.png" width="80%">
</p>
<p align="center"><em>Figure 4. Preprocessing steps for raw point cloud data. (a) Raw point cloud. (b) Voxel-based downsampling. (c) RANSAC-based filtering; red points indicate filtered wall points. (d) Final denoised point cloud after statistical outlier removal. Background points are shown in black; object points and bounding boxes are color-coded by object class.</em></p>

A total of 10,985 point cloud scenes were collected at 5 fps using 19 fixed Flash LiDAR sensors. To reduce noise and computational complexity, a three-stage preprocessing pipeline was applied using Open3D:

(1) Voxel downsampling with a voxel size of 0.25 to adjust point density.

(2) RANSAC plane removal (max distance 0.2 m, 3-point model, 500 iterations) to separate foreground objects from walls/floors.

(3) Statistical Outlier Removal (SOR) with 20 neighbors and 1.5 std threshold.

Finally, all point clouds were axis-normalized to (0,0,0) for consistent training.

## 3. GT Sampling for Class Imbalance Mitigation

<p align="center">
  <img src="/publications/sald-net/images/gt_sampling_result.png" width="90%">
</p>
<p align="center"><em>Figure 5. Illustration of GT sampling process and number of classes. Blue points indicate original GTs, and red points are GT annotations from the database for robot (R), person (P), bed (B), and wheelchair (W). The first column shows the original samples, the second column shows the GT database, the third column shows the augmentation process (a, b), and the final column shows the augmented samples. Background points are shown in white.</em></p>

The dataset was divided into training/validation/test sets at a 6:2:2 ratio. Severe class imbalance was observed among the four classes (person, robot, bed, wheelchair), with the person class dominating. To mitigate this, a GT sampling strategy was applied: annotated GT instances of minority classes were extracted from a database and inserted into other scenes while preventing spatial overlap. 

During augmentation, bounding box ranges of each scene were computed, and new GT instances were placed according to the scene’s geometry (e.g., positive x- or y-direction) to avoid collisions. Bounding boxes were updated with each addition. After augmentation, all point clouds were re-normalized to (0,0,0) to maintain consistent alignment across samples. This GT sampling process produced a more balanced yet realistic dataset for robust network training.

## 4. Graph-based Self-Attention
SALD-Net adopts a two-stage 3D detection architecture:

(1) initial 3D box proposal via PointNet++ with BAM

(2) refinement via URG RoI pooling + RAM

To handle overlapping and occlusion, two graph-based self-attention modules (BAM, RAM) were introduced.

<p align="center">
  <img src="/publications/sald-net/images/network_architecture.png" width="80%">
</p>
<p align="center"><em>Figure 6. Architecture of the proposed SALD-Net.</em></p>

{% raw %}

### 4.1. Backbone-integrated Attention Module (BAM)

<p align="center">
  <img src="/publications/sald-net/images/BAM.png" width="80%">
</p>
<p align="center"><em>Figure 7. Architecture of the proposed BAM module.</em></p>

The Backbone-integrated self-attention mechanism (BAM) is applied after the third and fourth set abstraction layers for computational efficiency. Given an input point cloud, representative points are sampled and grouped into node features:

$$
X = \{ x_{1}, x_{2}, \ldots, x_{n} \}
$$

Each node feature $x_{i}$ represents a local point-wise grouped feature, and $n$ is the number of groups $(i = 1, \ldots, n)$. To capture global dependencies, all node pairs $(x_{i}, x_{j})$ are connected via an edge set:

$$
E = \{ r_{ij} \mid i, j = 1, \ldots, n \}
$$

where each edge $r_{ij}$ represents the interaction between the $i^{\text{th}}$ and $j^{\text{th}}$ nodes. These interaction terms are computed using a self-attention mechanism.

In BAM, each node feature $x_{j}$ is projected into a key vector $K_{j}$ and a value vector $V_{j}$, while a query vector $Q_{i}$ is derived from $x_{i}$. The attention weight is defined as:

$$
W_{ij} = \text{softmax}(Q_{i} \cdot K_{j}^{\top})
$$

The interaction term is:

$$
r_{ij} = W_{ij} \cdot V_{j}
$$

The global context-aware feature is:

$$
a_{i} = \sum_{j=1}^{n} r_{ij}
$$

This feature is aggregated via multi-head attention, concatenated with the local node feature $x_{i}$, and upsampled through feature propagation. Focal loss is applied to address class imbalance.

{% endraw %}

### 4.2. URG (unified regional and grid) RoI Pooling head

{% raw %}

### 4.2.1. Regional RoI pooling

Each 3D box proposal is defined as  
$$
b_i = (x_i, y_i, z_i,\, h_i, w_i, l_i, \theta_i),
$$  
where $(x_i, y_i, z_i)$ denote its center coordinates, $(h_i, w_i, l_i)$ are the dimensions, and $\theta_i$ is the orientation.  
To include boundary context, the box is slightly expanded as  
$$
b'_i = (x_i, y_i, z_i,\, h_i + \eta,\, w_i + \eta,\, l_i + \eta,\, \theta_i),
$$  
with a fixed expansion factor $\eta = 0.2$.

### 4.2.2. Hierarchical grid RoI pooling

To address point sparsity and capture multi-scale geometry, a five-level 3D grid is constructed inside each expanded RoI $b'_i$.  
The grid sizes and radii are defined as:  

$$
G = [6, 4, 4, 4, 1], \qquad
R = [0.2, 0.4, 0.6, 1.2, 1.6].
$$

Points near each grid vertex are grouped at multiple pyramid levels, enabling extraction of both interior and exterior structure, which improves recognition under occlusion.

### 4.2.3. RoI Feature-based Attention Module (RAM)

<p align="center">
  <img src="/publications/sald-net/images/RAM.png" width="80%">
</p>
<p align="center"><em>Figure 9. Architecture of the proposed RAM module.</em></p>

The RAM module refines the pooled features by integrating spatial and semantic relationships.  
It takes as input:

$$
F_{xyz} \in \mathbb{R}^{N \times 3}, \qquad
F_f \in \mathbb{R}^{N \times C},
$$

where $N$ is the number of neighboring points.

Position embedding:

$$
p_e = \text{FC}(F_{xyz})
$$

Semantic key and value projections:

$$
k_e = \text{FC}(F_f), \qquad
v_e = \text{MLP}(F_f)
$$

Four learnable coefficients modulate spatial–semantic interactions:

$$
v_{\text{coef}},\; q_{\text{coef}},\; k_{\text{coef}},\; qk_{\text{coef}}
$$

Updated embeddings:

$$
v'_e = v_e + v_{\text{coef}} \cdot p_e
$$

$$
p'_e = q_{\text{coef}} \cdot p_e
$$

$$
k'_e = k_{\text{coef}} \cdot k_e
$$

$$
pk'_e = qk_{\text{coef}} \cdot (p_e \odot k_e)
$$

Attention map:

$$
A = p'_e + k'_e + pk'_e
$$

Final aggregated feature:

$$
F_{\text{agg}}
= \sum_{i=1}^{N}
\text{Softmax}(A_i)\, v'_e(i)
$$

This mechanism enhances 3D bounding box refinement by emphasizing critical spatial and semantic cues, especially under partial occlusion.

{% endraw %}

# 4. Result

<p align="center">
  <img src="/publications/sald-net/images/result_table.png" width="80%">
</p>
<p align="center"><em>Figure 9. Architecture of the proposed RAM module.</em></p>

<p align="center">
  <img src="/publications/sald-net/images/RAM.png" width="80%">
</p>
<p align="center"><em>Figure 9. Architecture of the proposed RAM module.</em></p>











