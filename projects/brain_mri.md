---
layout: default
title: brain_tumor_radiogenomics
permalink: /projects/brain_mri/
---

# 1. Overview

This project addresses the Brain Tumor Radiogenomic Classification task from the Kaggle competition, aiming to predict **MGMT promoter methylation status (0/1)** in glioblastoma patients using multi-parametric MRI (mpMRI) scans.

MGMT methylation is a clinically important molecular biomarker associated with:
- Chemotherapy response (Temozolomide)
- Treatment planning
- Prognosis prediction

However, conventional assessment requires invasive biopsy procedures.

This study proposes a **Depth-aware 2.5D CNN–RNN architecture (CRNN)** that models MRI volumes as sequential data, enabling effective learning of both intra-slice features and inter-slice contextual relationships.

By incorporating depth-aware modeling, the approach improves classification robustness and predictive stability compared to slice-wise CNN baselines.

---

# 2. Motivation

Radiogenomics aims to bridge imaging phenotypes and genetic information.  
However, predicting MGMT status from MRI presents several challenges:

- MRI volumes consist of multiple heterogeneous modalities (FLAIR, T1w, T1CE, T2w)
- Slice-wise CNN models ignore 3D structural continuity
- Full 3D CNNs require high computational cost and large datasets
- The Kaggle dataset is relatively small and imbalanced

Rather than using heavy 3D architectures, this project explores a more efficient question:

**"Can depth-aware sequential modeling capture sufficient 3D structural information without full 3D convolution?"**

This perspective balances:
- Computational efficiency
- Data limitation robustness
- Structural feature learning

---

# 3. Proposed Approach

The proposed framework interprets MRI volumes as ordered slice sequences and applies a hybrid CNN–RNN architecture.

## 3-1. Data Representation Strategy

Each MRI study contains multiple axial slices across four modalities.

Instead of:
- Treating each slice independently (2D CNN), or
- Applying full 3D convolution (3D CNN),

the model:

- Extracts spatial features per slice using a 2D CNN backbone
- Models inter-slice dependencies using an RNN layer
- Aggregates sequential information into a final classification output

This enables **2.5D representation learning**, capturing contextual continuity along the depth axis.

---

## 3-2. Network Architecture (CRNN)

The architecture consists of:

**1) CNN Feature Extractor**
- Pretrained 2D CNN backbone (e.g., ResNet-based)
- Shared weights across slices
- Outputs slice-level feature embeddings

**2) RNN Sequence Modeler**
- Bidirectional LSTM/GRU layer
- Learns depth-wise contextual dependencies
- Encodes tumor structural progression across slices

**3) Classification Head**
- Fully connected layers
- Sigmoid activation for binary MGMT prediction

This design significantly reduces parameters compared to 3D CNN while preserving volumetric awareness.

---

# 4. Implementation Details

## 4-1. Dataset

The dataset is derived from the Kaggle Brain Tumor Radiogenomic Classification challenge.

- Multi-parametric MRI (FLAIR, T1w, T1CE, T2w)
- Binary labels: MGMT methylated (1) vs unmethylated (0)
- Axial slice format
- Preprocessed and standardized per study

Due to class imbalance and limited sample size:
- Stratified splitting was applied
- Data augmentation was selectively used

---

## 4-2. Preprocessing Pipeline

- Slice sorting to preserve anatomical order
- Intensity normalization per volume
- Resize and cropping to standardized resolution
- Removal of non-brain regions when applicable

The goal was to maintain anatomical continuity while reducing noise.

---

## 4-3. Training Configuration

**Optimizer**
- Adam optimizer

**Batch size**
- Study-level batching

**Loss Function**
- Binary Cross-Entropy

**Evaluation Metric**
- Area Under the ROC Curve (AUC)

**Hardware**
- GPU-based training environment

---

# 5. Results

Performance comparison:

- Slice-wise CNN baseline: AUC ≈ 0.63
- Proposed CRNN (2.5D): AUC ≈ 0.75

Key observations:

- Depth modeling improved stability of predictions
- Reduced overfitting compared to pure 2D models
- Efficient alternative to heavy 3D CNN architectures

The +12% improvement in AUC demonstrates that incorporating structural continuity across slices significantly enhances radiogenomic prediction.

---

# 6. Technical Takeaways

## 6-1. Sequential Modeling as Efficient 3D Alternative
CRNN provides a computationally efficient substitute for 3D CNNs in volumetric medical imaging.

## 6-2. Importance of Depth Context
Ignoring slice continuity results in structural information loss.
Depth-aware modeling improves feature consistency.

## 6-3. Data-Efficient Architecture Design
When dataset size is limited, architecture design strategy matters more than increasing model complexity.

---

# 7. Future Work

## 7-1. Attention-Based Slice Weighting
Incorporate attention mechanisms to identify diagnostically dominant slices.

## 7-2. Multi-Modal Fusion Strategies
Explore modality-specific encoders before sequence aggregation.

## 7-3. Self-Supervised Pretraining
Apply contrastive or masked-volume pretraining for improved generalization.

## 7-4. Clinical Validation
Extend evaluation beyond competition dataset to real-world multi-center cohorts.
