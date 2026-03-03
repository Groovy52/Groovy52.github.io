---
layout: page
title: SALD-Net
description: Self-attention-integrated LiDAR-based 3D object detection network in a crowded hospital environment
img: assets/img/sald-net_fig2.png
importance: 1
category: research
---

# 1. Overview

Autonomous mobile robots (AMRs) are increasingly deployed in hospitals to mitigate workforce shortages and support healthcare delivery by performing tasks such as medication transport and patient guidance.

However, accurate **3D object detection in hospital environments** is significantly more difficult than in outdoor autonomous driving scenarios due to dense human-object interactions, privacy constraints, and sensor limitations. :contentReference[oaicite:0]{index=0}

This project proposes **SALD-Net**, a self-attention-based 3D detection framework designed specifically for hospital LiDAR environments. 

---
# 2. Why This Problem Matters
## 2-1. Why Hospital Detection Is Challenging

Hospital environments exhibit unique domain characteristics:

- Frequent **overlapping interactions** between patients, staff, beds, and wheelchairs
- Narrow and cluttered corridors causing severe **occlusion and partial observations**
- Lack of domain-specific datasets compared to public benchmark datasets such as KITTI and Waymo which focus on outdoor driving scenes
beds and wheelchairs
- Strict privacy regulations preventing RGB-based perception → depth-only sensing required 

As a result, models trained on datasets such as KITTI or Waymo suffer from strong domain shift

---

# 3. Challenges

The project addressed four key problems:

## 3-1. Domain Gap
The lack of domain-specific datasets and the presence of occluded or specialized objects (e.g., beds, wheelchairs) pose significant challenges

## 3-2. Sensor Noise & Low Resolution
Flash LiDAR introduced outliers and unclear object boundaries

## 3-3. Class Imbalance 
Wheelchairs and beds appeared far less frequently than people

## 3-4. Occlusion & Overlapping Objects
Dense multi-object motion made instance separation extremely difficult

---

# 4. Approach

## 4.1 Hospital-Specific Dataset Construction

- Installed **19 Flash LiDAR sensors** across four hospital zones
- Collected **10,985 real-world point cloud scenes** at 5 FPS
- Designed acquisition scenarios based on real collision-risk workflows

All data were captured without RGB to preserve patient privacy


## 4.2 Preprocessing Pipeline for Indoor LiDAR

To stabilize noisy hospital point clouds, a four-stage pipeline was designed:

- Voxel-based downsampling for density normalization 
- RANSAC filtering to remove structural planes 
- Statistical outlier removal for sensor noise reduction 
- Coordinate normalization for consistent learning input 

## 4.3 Class-Imbalance Mitigation

A GT-sampling augmentation strategy increased rare-class instances
while maintaining realistic hospital distributions


## 4.4 Self-Attention-Based Detection Architecture

SALD-Net introduces two attention modules:

- **BAM (Backbone Attention Module)**  
  Enhances global geometric dependency beyond PointNet++ local features. 

- **RAM (RoI Attention Module)**  
  Refines proposals using contextual relationships between neighboring objects. 

This enables robust detection under occlusion and overlapping scenarios


---

# 5. Implementation Details

- Framework: PyTorch + OpenPCDet 
- Training: 100 epochs with Adam optimizer 
- Hardware: RTX 3090 environment
- Dataset split: 6:2:2 (train/val/test) 

---

# 6. Results

SALD-Net significantly outperformed the baseline Part-A2 detector:

- **3D mAP: 89.08%** 
- Overall improvement: **+19.56%** 
- Wheelchair detection: +22.85%p 
The model successfully separated objects that previous detectors failed to distinguish in cluttered hospital scenes


---

# 7. Technical Takeaways

This work demonstrates that:

- Indoor medical environments require **domain-specific perception modeling**
- Global relational reasoning is critical for dense human-object interaction scenes
- Dataset realism is as important as model architecture for safety-critical robotics

---

# 8. Future Work

Future extensions include:

- Multi-sensor fusion for improved spatial robustness
- Deployment-oriented optimization for real-time AMR navigation
- Transfer of the preprocessing pipeline to radar-based perception systems
