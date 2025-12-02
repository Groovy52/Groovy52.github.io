<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&display=swap" rel="stylesheet">

<style>
body {
  font-family: "Inter", sans-serif;
  line-height: 1.6;
}
</style>

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

## Problem Statement

Hospital environments present several unique challenges for LiDAR-based 3D object detection that differ significantly from conventional outdoor autonomous-driving domains.

### **1. Domain Gap: Lack of Hospital-Specific Datasets**

<p align="center">
  <img src="/publications/sald-net/images/kitti_classes.png" width="70%">
</p>
<p align="center"><em>Figure 1. Comparison of object classes between KITTI and hospital environments.</em></p>

Public datasets such as KITTI and Waymo do not contain hospital-relevant objects such as beds, wheelchairs, medical staff, or mobility-assisted patient scenarios.  
As a result, existing models experience severe **domain shift** when applied to hospital scenes.

---

### **2. Flash LiDAR Noise and Low Resolution**

<p align="center">
  <img src="/publications/sald-net/images/lidar_noise.png" width="70%">
</p>
<p align="center"><em>Figure 2. Noise, sparsity, and low-resolution challenges of indoor Flash LiDAR.</em></p>

Indoor Flash LiDAR produces frequent outliers, blurred boundaries, and sparse point distributions—causing unstable bounding box estimation.

---

### **3. Class Imbalance**

<p align="center">
  <img src="/publications/sald-net/images/class_imbalance.png" width="70%">
</p>
<p align="center"><em>Figure 3. Example of class imbalance in hospital datasets.</em></p>

Hospitals show highly skewed category frequencies, with staff and patients appearing far more often than wheelchairs, beds, or robots.

---

### **4. Occlusion and Overlap in Dynamic Hospital Scenes**

<p align="center">
  <img src="/publications/sald-net/images/occlusion_overlap.png" width="75%">
</p>
<p align="center"><em>Figure 4. Occlusion, overlap, and sparsity examples from hospital LiDAR data.</em></p>

Crowded corridors, staff assisting patients, and narrow clinical spaces make **overlapping** and **partial occlusion** unavoidable, making instance separation extremely difficult.

## Methods

### **1. Hospital-Specific LiDAR Dataset Construction**

<p align="center">
  <img src="/publications/sald-net/images/dataset_map.png" width="65%">
</p>
<p align="center"><em>Figure 5. Sensor deployment zones for data collection.</em></p>

We deployed 19 Flash LiDAR sensors across four hospital zones…

---

### **2. Preprocessing Pipeline for Indoor Flash LiDAR**

<p align="center">
  <img src="/publications/sald-net/images/preprocessing_steps.png" width="80%">
</p>
<p align="center"><em>Figure 6. Preprocessing pipeline: downsampling → RANSAC → SOR.</em></p>

A three-stage preprocessing pipeline was applied…

---

### **3. GT Sampling for Class Imbalance Mitigation**

<p align="center">
  <img src="/publications/sald-net/images/gt_sampling.png" width="80%">
</p>
<p align="center"><em>Figure 7. Illustration of GT sampling for balancing rare classes.</em></p>

GT instances from a database were inserted…

---

### **4. Backbone-integrated Attention Module (BAM)**

<p align="center">
  <img src="/publications/sald-net/images/BAM_architecture.png" width="80%">
</p>
<p align="center"><em>Figure 8. Architecture of the proposed BAM module.</em></p>

BAM enhances global geometric reasoning…

---

### **5. RoI Feature-based Attention Module (RAM)**

<p align="center">
  <img src="/publications/sald-net/images/RAM_architecture.png" width="80%">
</p>
<p align="center"><em>Figure 9. Architecture of the proposed RAM module.</em></p>

RAM refines RoIs by integrating positional and semantic relationships…

















