---
layout: page
title: SALD-Net
description: Self-Attention-based 3D Object Detection for Hospital AMR Navigation
img: assets/img/sald-net_fig2.png
importance: 1
category: research
---

# 1. Overview

Autonomous mobile robots (AMRs) are increasingly deployed in hospitals for tasks such as
medication delivery, patient guidance, and nighttime monitoring.

However, accurate **3D object detection in hospital environments** is significantly more difficult
than in outdoor autonomous driving scenarios due to dense human-object interactions,
privacy constraints, and sensor limitations. :contentReference[oaicite:0]{index=0}

This project proposes **SALD-Net**, a self-attention-based 3D detection framework designed
specifically for hospital LiDAR environments. :contentReference[oaicite:1]{index=1}


---
# 2. Why This Problem Matters
## 2. Why Hospital Detection Is Challenging

Hospital environments exhibit unique domain characteristics:

- Frequent **overlapping interactions** between patients, caregivers, beds, and wheelchairs :contentReference[oaicite:2]{index=2}
- Narrow and cluttered corridors causing severe **occlusion and partial observations** :contentReference[oaicite:3]{index=3}
- Strict privacy regulations preventing RGB-based perception → depth-only sensing required :contentReference[oaicite:4]{index=4}
- Flash LiDAR produces low-resolution, noisy point clouds that blur object boundaries :contentReference[oaicite:5]{index=5}

As a result, models trained on datasets such as KITTI or Waymo suffer from strong domain shift. :contentReference[oaicite:6]{index=6}


---

# 3. Challenges

The project addressed four key problems:

**T1 — Domain Gap**  
No dataset existed representing hospital interaction patterns. :contentReference[oaicite:7]{index=7}

**T2 — Sensor Noise & Low Resolution**  
Flash LiDAR introduced outliers and unclear object boundaries. :contentReference[oaicite:8]{index=8}

**T3 — Class Imbalance**  
Wheelchairs and beds appeared far less frequently than people. :contentReference[oaicite:9]{index=9}

**T4 — Occlusion & Overlapping Objects**  
Dense multi-object motion made instance separation extremely difficult. :contentReference[oaicite:10]{index=10}


---

# 4. Approach

## 4.1 Hospital-Specific Dataset Construction

- Installed **19 Flash LiDAR sensors** across four hospital zones. :contentReference[oaicite:11]{index=11}
- Collected **10,985 real-world point cloud scenes** at 5 FPS. :contentReference[oaicite:12]{index=12}
- Designed acquisition scenarios based on real collision-risk workflows. :contentReference[oaicite:13]{index=13}

All data were captured without RGB to preserve patient privacy. :contentReference[oaicite:14]{index=14}


## 4.2 Preprocessing Pipeline for Indoor LiDAR

To stabilize noisy hospital point clouds, a four-stage pipeline was designed:

- Voxel-based downsampling for density normalization :contentReference[oaicite:15]{index=15}
- RANSAC filtering to remove structural planes :contentReference[oaicite:16]{index=16}
- Statistical outlier removal for sensor noise reduction :contentReference[oaicite:17]{index=17}
- Coordinate normalization for consistent learning input :contentReference[oaicite:18]{index=18}


## 4.3 Class-Imbalance Mitigation

A GT-sampling augmentation strategy increased rare-class instances
while maintaining realistic hospital distributions. :contentReference[oaicite:19]{index=19}


## 4.4 Self-Attention-Based Detection Architecture

SALD-Net introduces two attention modules:

- **BAM (Backbone Attention Module)**  
  Enhances global geometric dependency beyond PointNet++ local features. :contentReference[oaicite:20]{index=20}

- **RAM (RoI Attention Module)**  
  Refines proposals using contextual relationships between neighboring objects. :contentReference[oaicite:21]{index=21}

This enables robust detection under occlusion and overlapping scenarios. :contentReference[oaicite:22]{index=22}


---

# 5. Implementation Details

- Framework: PyTorch + OpenPCDet :contentReference[oaicite:23]{index=23}
- Training: 100 epochs with Adam optimizer :contentReference[oaicite:24]{index=24}
- Hardware: RTX 3090 environment :contentReference[oaicite:25]{index=25}
- Dataset split: 6:2:2 (train/val/test) :contentReference[oaicite:26]{index=26}


---

# 6. Results

SALD-Net significantly outperformed the baseline Part-A2 detector:

- **3D mAP: 89.08%** :contentReference[oaicite:27]{index=27}
- Overall improvement: **+19.56%** :contentReference[oaicite:28]{index=28}
- Bed detection: +8.78%p :contentReference[oaicite:29]{index=29}
- Wheelchair detection: +22.85%p :contentReference[oaicite:30]{index=30}

The model successfully separated objects that previous detectors failed to distinguish
in cluttered hospital scenes. :contentReference[oaicite:31]{index=31}


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
