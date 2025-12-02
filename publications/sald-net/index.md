---
layout: post
title: "SALD-Net: Self-Attention-integrated LiDAR-based 3D Object Detection in Crowded Hospital Environments"
date: 2025-09-22
categories: [Publications, 3D Perception, LiDAR]
---

Accurate 3D object detection from point clouds is essential for autonomous systems, especially in complex indoor environments such as hospitals.  
In this post, I introduce **SALD-Net**, my published work proposing a self-attention-integrated LiDAR-based 3D object detection network tailored for crowded, cluttered, and privacy-sensitive hospital settings.

The paper is published in:

📄 **Signal, Image and Video Processing (SIVP), 2025**  
🔗 *Paper:* https://link.springer.com/article/10.1007/s11760-025-04727-y  
🔗 *Code:* https://github.com/Groovy52/SALD-Net  

---

# 1. Introduction

<div style="text-align:center;">
  <img src="./figures/saldnet_cover.png" width="80%" alt="SALD-Net overview (placeholder)">
  <p style="color:gray;">*Conceptual overview of SALD-Net in a congested hospital corridor (placeholder).* </p>
</div>

Hospitals represent one of the most difficult indoor environments for 3D perception:

- Patients requiring mobility assistance are often accompanied by caregivers.
- Wheelchairs, beds, stretchers, and medical carts constantly move through narrow corridors.
- People and equipment frequently overlap or occlude one another.
- RGB cameras cannot be used due to strict privacy regulations.

Existing public datasets such as KITTI or Waymo contain outdoor scenes and lack the object categories and motion patterns present in clinical environments. Applying such models directly leads to **severe domain shift** and poor detection performance.

SALD-Net is designed to resolve these challenges using a **hospital-specific LiDAR dataset** and **global–local self-attention mechanisms**.

---

# 2. Motivation

<div style="text-align:center;">
  <img src="./figures/saldnet_challenges.png" width="80%" alt="Hospital detection challenges placeholder">
  <p style="color:gray;">*Occlusion, overlapping, and sparse point clouds in hospital scenes (placeholder).* </p>
</div>

Hospital perception is challenging due to:

### ▸ 2.1 Limited hospital-domain datasets  
Existing datasets do not include wheelchairs, beds, stretchers, or caregiver–patient interactions.

### ▸ 2.2 Frequent occlusion & overlapping  
A caregiver positioned behind a wheelchair patient creates **blended point clouds** that are hard to separate.

### ▸ 2.3 Privacy-preserving requirements  
RGB images cannot be used.  
Only spatial information from LiDAR is allowed.

### ▸ 2.4 Low-resolution & noisy indoor LiDAR  
Flash LiDAR produces sparse, noisy data.  
Boundaries between adjacent objects are often ambiguous.

These motivated us to design a dataset and detection model specifically optimized for hospital dynamics.

---

# 3. Hospital LiDAR Dataset (10,985 scenes)

<div style="text-align:center;">
  <img src="./figures/saldnet_dataset.png" width="80%" alt="Dataset visualization placeholder">
  <p style="color:gray;">*Example BEV/3D point cloud scenes from the constructed dataset (placeholder).* </p>
</div>

To address the lack of clinical-domain data, we collected **10,985 scenes** using **19 Flash LiDAR sensors** across four hospital zones.

Dataset characteristics:
- Captures **wheelchair–patient–caregiver** interactions
- 5 FPS operation  
- Train/Val/Test = **6 : 2 : 2** with **patient-level split**  
- **No RGB** data recorded  
- Dense clutter and natural movement patterns

This dataset reflects real-world hospital complexity and serves as the basis for SALD-Net.

---

# 4. Method

<div style="text-align:center;">
  <img src="./figures/saldnet_architecture.png" width="85%" alt="SALD-Net architecture placeholder">
  <p style="color:gray;">*Overall architecture of SALD-Net (placeholder).* </p>
</div>

SALD-Net is a two-stage 3D detection framework based on OpenPCDet and enhanced with self-attention.

---

## 4.1 Backbone Attention Module (BAM)

PointNet++ captures local geometric features but struggles in overlapping scenarios.  
**BAM injects self-attention into the backbone**, enabling the model to:

- Capture **global dependencies**
- Separate blended clusters (e.g., caregiver + wheelchair)
- Improve robustness in cluttered regions

---

## 4.2 Unified Regional & Grid (URG) RoI Pooling

To better handle diverse object scales and shapes in hospitals:

- URG pooling combines **grid-level** and **regional** feature extraction.
- Ensures stable feature representation under occlusion.

---

## 4.3 RoI Attention Module (RAM)

RAM applies learnable self-attention on RoI features:

- Understands context around partially occluded objects  
- Improves box refinement accuracy  
- Helps differentiate human–equipment pairs

---

## 4.4 Augmentation via GT-Sampling

To mitigate class imbalance and overlapping conditions:

- Rare classes (beds, wheelchairs) are oversampled  
- Synthetic overlapping scenarios are generated  
- Greatly enhances generalization under dense hospital interactions

---

# 5. Preprocessing Pipeline

To handle indoor LiDAR noise:

- **Voxel downsampling**  
- **RANSAC floor-plane removal**  
- **Statistical outlier filtering**  

Together, these steps stabilize point cloud quality before inference.

---

# 6. Experiments

| Model        | 3D mAP | BEV mAP |
|--------------|--------|---------|
| PointPillars | 66.66  | 71.15   |
| SECOND       | 72.58  | 75.89   |
| Part-A2      | 78.68  | 83.53   |
| **SALD-Net** | **89.08** | **90.03** |

<div style="text-align:center;">
  <img src="./figures/saldnet_results.png" width="80%" alt="Qualitative results placeholder">
  <p style="color:gray;">*Qualitative results showing improved detection in overlapping scenes (placeholder).* </p>
</div>

**Key improvements:**  
- +16.5%p 3D mAP over SECOND  
- Robust detection under caregiver–patient overlapping  
- Stable refinement under partial occlusion

---

# 7. Discussion

SALD-Net demonstrates that self-attention combined with domain-specific augmentation is highly effective in indoor hospital environments.  
The method is:

- **Privacy-preserving**
- **Robust to occlusion**
- **Deployable in real hospital robots**

The approach provides a promising direction for safe, reliable indoor service robotics in healthcare.

---


# Citation

```bibtex
@article{saldnet2025kim,
  title={SALD-Net: Self-attention-integrated LiDAR-based 3D object detection network in a crowded hospital environment},
  author={Kim, Goeun and Yang, Su and Han, Ji Yong and Kim, Jun-Min and Yi, Won-Jin},
  journal={Signal, Image and Video Processing},
  year={2025}
}
