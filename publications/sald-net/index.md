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
## **1. Domain Gap: Lack of Hospital-Specific Datasets**

<p align="center">
  <img src="/publications/sald-net/images/kitti.png" width="85%">
</p>
<p align="center"><em>Figure 1. object classes and scene structure of KITTI</em></p>

Public datasets such as KITTI and Waymo do not contain hospital-relevant objects such as beds, wheelchairs, medical staff, or mobility-assisted patient scenarios.  
As a result, existing models experience severe **domain shift** when applied to hospital scenes.

---

## **2. Flash LiDAR Noise and Low Resolution**

Indoor Flash LiDAR produces frequent outliers, blurred boundaries, and sparse point distributions—causing unstable bounding box estimation. Sparsity of point is illustrated in Figure 2.

---

## **3. Class Imbalance**

Hospitals show highly skewed category frequencies, with staff and patients appearing far more often than wheelchairs, beds, or robots.

---

### **4. Occlusion and Overlap in Dynamic Hospital Scenes**

<p align="center">
  <img src="/publications/sald-net/images/lidar_challenges.png" width="65%">
</p>
<p align="center"><em>Figure 2. Occlusion, overlap, and sparsity examples from hospital LiDAR data.</em></p>

**Crowded corridors** (Indoor structure of hospital as illustrated in Figure 1), staff assisting patients, and narrow clinical spaces make **overlapping** and **partial occlusion** unavoidable, making instance separation extremely difficult.

# 3. Methods

## **1. Hospital-Specific LiDAR Dataset Construction**

<p align="center">
  <img src="/publications/sald-net/images/complex_hospital.png" width="65%">
</p>
<p align="center"><em>Figure 3. Sensor deployment zones for data collection.</em></p>

We deployed 19 Flash LiDAR sensors across four hospital zones.

---

## **2. Preprocessing Pipeline for Indoor Flash LiDAR**

<p align="center">
  <img src="/publications/sald-net/images/preprocessing_steps.png" width="80%">
</p>
<p align="center"><em>Figure 4. Preprocessing steps for raw point cloud data. (a) Raw point cloud. (b) Voxel-based downsampling. (c) RANSAC-based filtering; red points indicate filtered wall points. (d) Final denoised point cloud after statistical outlier removal. Background points are shown in black; object points and bounding boxes are color-coded by object class.</em></p>

A total of 10,985 point cloud scenes were collected at 5 fps using 19 fixed Flash LiDAR sensors (NSL-1110AV, 320×240 resolution, 12 m depth range). To reduce noise and computational complexity, a three-stage preprocessing pipeline was applied using Open3D:
(1) Voxel downsampling with a voxel size of 0.25 to adjust point density.
(2) RANSAC plane removal (max distance 0.2 m, 3-point model, 500 iterations) to separate foreground objects from walls/floors.
(3) Statistical Outlier Removal (SOR) with 20 neighbors and 1.5 std threshold.
Finally, all point clouds were axis-normalized to (0,0,0) for consistent training.

---

## **3. GT Sampling for Class Imbalance Mitigation**

<p align="center">
  <img src="/publications/sald-net/images/gt_sampling_result.png" width="80%">
</p>
<p align="center"><em>Figure 5. Illustration of GT sampling process and number of classes. Blue points indicate original GTs, and red points are GT annotations from the database for robot (R), person (P), bed (B), and wheelchair (W). The first column shows the original samples, the second column shows the GT database, the third column shows the augmentation process (a, b), and the final column shows the augmented samples. Background points are shown in white.</em></p>

The dataset was divided into training/validation/test sets at a 6:2:2 ratio. Severe class imbalance was observed among the four classes (person, robot, bed, wheelchair), with the person class dominating. To mitigate this, a GT sampling strategy was applied: annotated GT instances of minority classes were extracted from a database and inserted into other scenes while preventing spatial overlap. This increased instance counts from 4,081 → 6,336 (robot), 4,434 → 6,689 (bed), and 711 → 5,937 (wheelchair), while the person class remained unchanged to preserve realistic hospital distributions.

During augmentation, bounding box ranges of each scene were computed, and new GT instances were placed according to the scene’s geometry (e.g., positive x- or y-direction) to avoid collisions. Bounding boxes were updated with each addition. After augmentation, all point clouds were re-normalized to (0,0,0) to maintain consistent alignment across samples. This GT sampling process produced a more balanced yet realistic dataset for robust network training.

---

## **4. Graph-based Self-Attention**
SALD-Net adopts a two-stage 3D detection architecture:
(1) initial 3D box proposal via PointNet++,
(2) refinement via URG RoI pooling + RAM.
To handle overlapping and occlusion, two graph-based self-attention modules (BAM, RAM) were introduced.

<p align="center">
  <img src="/publications/sald-net/images/network_architecture" width="80%">
</p>
<p align="center"><em>Figure 6. Architecture of the proposed SALD-Net.</em></p>

### **Backbone-integrated Attention Module (BAM)**

<p align="center">
  <img src="/publications/sald-net/images/BAM.png" width="80%">
</p>
<p align="center"><em>Figure 8. Architecture of the proposed BAM module.</em></p>

Representative point groups are constructed as:

𝑋
=
{
𝑥
1
,
𝑥
2
,
…
,
𝑥
𝑛
}
X={x
1
	​

,x
2
	​

,…,x
n
	​

}

Each node feature produces query, key, and value embeddings:

𝐾
𝑗
=
𝑊
𝐾
𝑥
𝑗
,
𝑉
𝑗
=
𝑊
𝑉
𝑥
𝑗
,
𝑄
𝑖
=
𝑊
𝑄
𝑥
𝑖
K
j
	​

=W
K
	​

x
j
	​

,V
j
	​

=W
V
	​

x
j
	​

,Q
i
	​

=W
Q
	​

x
i
	​

Attention weight
𝑊
𝑖
𝑗
=
softmax
(
𝑄
𝑖
⋅
𝐾
𝑗
)
W
ij
	​

=softmax(Q
i
	​

⋅K
j
	​

)
Interaction term
𝑟
𝑖
𝑗
=
𝑊
𝑖
𝑗
⋅
𝑉
𝑗
r
ij
	​

=W
ij
	​

⋅V
j
	​

Global context feature
𝑎
𝑖
=
∑
𝑗
=
1
𝑛
𝑟
𝑖
𝑗
a
i
	​

=
j=1
∑
n
	​

r
ij
	​


The final feature forwarded to propagation is:

[
𝑥
𝑖
 
∥
 
𝑎
𝑖
]
[x
i
	​

∥a
i
	​

]

Foreground–background imbalance is addressed using focal loss.


### **URG (unified regional and grid) RoI Pooling head**

### Regional RoI pooling

A slightly expanded RoI box is defined as:

𝑏
𝑖
′
=
(
𝑥
𝑖
,
 
𝑦
𝑖
,
 
𝑧
𝑖
,
 
ℎ
𝑖
+
𝜂
,
 
𝑤
𝑖
+
𝜂
,
 
𝑙
𝑖
+
𝜂
,
 
𝜃
𝑖
)
b
i
′
	​

=(x
i
	​

,y
i
	​

,z
i
	​

,h
i
	​

+η,w
i
	​

+η,l
i
	​

+η,θ
i
	​

)

with expansion factor:

𝜂
=
0.2
η=0.2

### Hierarchical grid pooling

Five pyramid levels are constructed:

𝐺
=
[
6
,
 
4
,
 
4
,
 
4
,
 
1
]
G=[6,4,4,4,1]

with corresponding radii:

𝑅
=
[
0.2
,
 
0.4
,
 
0.6
,
 
1.2
,
 
1.6
]
R=[0.2,0.4,0.6,1.2,1.6]

These grids capture multi-scale local and contextual geometric features.

### RoI Feature-based Attention Module (RAM)

<p align="center">
  <img src="/publications/sald-net/images/RAM.png" width="80%">
</p>
<p align="center"><em>Figure 9. Architecture of the proposed RAM module.</em></p>

RAM refines RoI features using positional coordinates and semantic features.

Position embedding
𝑝
𝑒
=
FC
(
𝐹
𝑥
𝑦
𝑧
)
pe=FC(F
xyz
	​

)
Semantic key and value
𝑘
𝑒
=
FC
(
𝐹
𝑓
)
,
𝑣
𝑒
=
MLP
(
𝐹
𝑓
)
k
e
	​

=FC(F
f
	​

),v
e
	​

=MLP(F
f
	​

)
Learnable coefficients

Four learnable coefficients are generated using:

𝜎
(
Linear
(
Mean
(
⋅
)
)
)
σ(Linear(Mean(⋅)))

They are:

𝑣
coef
,
𝑞
coef
,
𝑘
coef
,
𝑞
𝑘
coef
v
coef
	​

,q
coef
	​

,k
coef
	​

,qk
coef
	​

Updated embeddings
𝑣
𝑒
′
=
𝑣
𝑒
+
𝑣
coef
⋅
𝑝
𝑒
v
e
′
	​

=v
e
	​

+v
coef
	​

⋅pe
𝑝
𝑒
′
=
𝑞
coef
⋅
𝑝
𝑒
p
e
′
	​

=q
coef
	​

⋅pe
𝑘
𝑒
′
=
𝑘
coef
⋅
𝑘
𝑒
k
e
′
	​

=k
coef
	​

⋅k
e
	​

𝑝
𝑘
𝑒
′
=
𝑞
𝑘
coef
⋅
(
𝑝
𝑒
⊙
𝑘
𝑒
)
pk
e
′
	​

=qk
coef
	​

⋅(pe⊙k
e
	​

)
Attention map
𝐴
=
𝑝
𝑒
′
+
𝑘
𝑒
′
+
𝑝
𝑘
𝑒
′
A=p
e
′
	​

+k
e
′
	​

+pk
e
′
	​

Final aggregated feature
𝐹
agg
=
∑
𝑖
=
1
𝑁
Softmax
(
𝐴
𝑖
)
 
𝑣
𝑒
′
(
𝑖
)
F
agg
	​

=
i=1
∑
N
	​

Softmax(A
i
	​

)v
e
′
	​

(i)

RAM improves box refinement by modeling spatial–semantic interactions under severe occlusion.

# **4. Result**

<p align="center">
  <img src="/publications/sald-net/images/result_table.png" width="80%">
</p>
<p align="center"><em>Figure 9. Architecture of the proposed RAM module.</em></p>

<p align="center">
  <img src="/publications/sald-net/images/RAM.png" width="80%">
</p>
<p align="center"><em>Figure 9. Architecture of the proposed RAM module.</em></p>











