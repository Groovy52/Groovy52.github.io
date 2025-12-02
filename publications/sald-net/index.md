---
layout: default
title: "SALD-Net: Self-Attention-integrated LiDAR-based 3D Object Detection in Crowded Hospital Environments"
description: "A LiDAR-only 3D object detection framework designed for cluttered, crowded, and privacy-restricted hospital environments."
---

# **SALD-Net: Self-Attention-integrated LiDAR-based 3D Object Detection in Crowded Hospital Environments**

<div style="text-align:center;">
  <img src="../images/saldnet_cover.png" width="75%" alt="SALD-Net Overview (Placeholder)">
  <p style="color:gray;">*Figure 1. SALD-Net operating in a crowded hospital corridor (placeholder image).* </p>
</div>

---

## **1. Introduction**

Accurate 3D object detection from point clouds is essential for autonomous systems such as self-driving cars, drones, and service robots.  
In clinical settings, autonomous mobile robots are increasingly deployed for tasks such as medication transport and patient guidance.  
However, reliable 3D perception in **crowded and cluttered hospital environments** remains challenging due to:

- **Frequent occlusion and overlapping** between patients, caregivers, wheelchairs, and beds  
- **Tight indoor corridors** with limited maneuvering space  
- **Sparse and noisy Flash LiDAR point clouds**  
- **Strict privacy restrictions**, which make RGB imaging unsuitable  

Public datasets like KITTI and Waymo do not contain hospital-relevant objects or mobility-assisted patient scenarios, resulting in severe **domain shift**.  
To address these challenges, we present **SALD-Net**, a self-attention-integrated 3D object detection framework designed specifically for hospital environments.

---

## **2. Motivation**

Hospital scenes differ significantly from typical autonomous-driving scenarios:

### **Crowded, dynamic scenes**
Patients—especially elderly or mobility-impaired individuals—often move with caregivers closely positioned behind or beside them.  
Such proximity produces **blended point clouds** where human and wheelchair geometries overlap.

### **Occlusion from medical equipment**
Beds, carts, and stretchers often obscure parts of a person’s body, causing partial observations and fragmented point clusters.

### **Privacy-preserving requirements**
RGB cameras risk exposing identifiable information.  
LiDAR-only perception is therefore preferred, but comes with **lower spatial resolution** and **higher noise**, making robust 3D detection more difficult.

These characteristics motivated the development of both a **hospital-specific dataset** and a **novel detection architecture** capable of modeling complex spatial dependencies.

---

## **3. Hospital-Specific LiDAR Dataset**

<div style="text-align:center;">
  <img src="../images/saldnet_dataset.png" width="75%" alt="Dataset Visualization (Placeholder)">
  <p style="color:gray;">*Figure 2. Example BEV and 3D point cloud scenes from the hospital dataset (placeholder image).* </p>
</div>

To overcome the lack of clinical-domain data, we constructed a **10,985-scene LiDAR-only dataset** in collaboration with a major hospital.  
The dataset includes:

- **19 Flash LiDAR sensors** deployed across **4 hospital zones**  
- Natural interactions among **patients, caregivers, medical staff, wheelchairs, beds, and carts**  
- **5 FPS** data collection in real operational conditions  
- **Patient-level train/val/test split** to prevent data leakage  
- **No RGB recordings** for privacy preservation  

This dataset captures the unique spatial characteristics of hospital environments and serves as the foundation for SALD-Net.

---

## **4. Method: SALD-Net Architecture**

<div style="text-align:center;">
  <img src="../images/saldnet_architecture.png" width="80%" alt="Architecture (Placeholder)">
  <p style="color:gray;">*Figure 3. High-level architecture of SALD-Net (placeholder image).* </p>
</div>

SALD-Net is a two-stage 3D object detection framework built upon the PointNet++ backbone and enhanced with **self-attention mechanisms** designed to address occlusion and overlapping.

### **4.1 Backbone Attention Module (BAM)**  
The backbone often struggles in dense hospital scenes because local point features are insufficient for separating overlapping objects.  
BAM integrates a **self-attention module** directly into the backbone to capture **global geometric dependencies**, enabling the network to differentiate closely spaced objects.

### **4.2 Unified Regional and Grid (URG) RoI Pooling**  
Hospital objects vary widely in shape, scale, and orientation.  
URG pooling combines regional pooling and grid-based pooling to generate more stable RoI features.

### **4.3 RoI Attention Module (RAM)**  
To refine partially occluded objects, RAM applies a learnable self-attention mechanism over RoI features.  
This helps the model understand **contextual relations** among adjacent human–equipment pairs, improving localization accuracy.

### **4.4 Simple Augmentation (GT-Sampling)**  
To address class imbalance and simulate realistic overlapping scenarios:

- Rare object classes (e.g., beds, wheelchairs) are oversampled  
- Synthetic proximity-based au

