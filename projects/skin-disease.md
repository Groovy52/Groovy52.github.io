---
layout: default
title: OMMs
permalink: /projects/skin-disease/
---

# 1. Overview

This project presents the development of a deep learning-based **Skin Disease Self-Diagnosis System**, designed to classify dermatological conditions (e.g., melanoma, eczema, vitiligo) from clinical images and deploy the model as a web-based service.

The primary objective was to build an end-to-end AI pipeline that integrates:

- Image classification using CNN-based architectures
- Performance optimization through iterative experimentation
- Deployment via Flask-based API
- Real-time inference through a web interface

Starting from a baseline accuracy of **57.8%**, the model was iteratively refined through five experimental stages, ultimately achieving:

- **Accuracy: 73.4%**
- **Recall: +16.9% improvement**
- **F1-score: +17.6% improvement**
- **API latency reduced from 8.3s to 4.1s**

The final system demonstrated both predictive robustness and service-level feasibility.

---

# 2. Motivation

With the rapid growth of digital healthcare services, there is increasing demand for accessible and preliminary medical screening tools.

However, practical challenges emerged early in the project:

- Low-quality images and noisy backgrounds
- Severe class imbalance
- Model instability and overfitting
- API integration errors
- Slow inference speed for web deployment

Additionally, medical AI systems must prioritize **recall and false-negative minimization**, especially in high-risk conditions such as melanoma.

Rather than focusing solely on accuracy, this project emphasized:

- Balanced classification performance
- Model generalization
- Deployment feasibility
- Service-oriented optimization

---

# 3. Proposed Approach

The development strategy followed a structured, iterative refinement process across five experimental versions (Ver1–Ver5).

### Core Design Strategy

1. Establish a CNN-based baseline model.
2. Improve data quality and class balance.
3. Introduce transfer learning (ResNet50).
4. Apply fine-tuning and learning rate scheduling.
5. Optimize augmentation and reduce overfitting.
6. Convert the trained model to a lightweight deployment format (TFLite).

### Key Architectural Evolution

- **Baseline CNN → Transfer Learning (ResNet50)**
- Fine-tuning selected layers
- Learning rate scheduling
- Dropout regularization
- EarlyStopping and ReduceLROnPlateau

The final architecture leveraged **ResNet50 with optimized augmentation and extended training (Epoch 300)** while carefully controlling overfitting.

---

# 4. Implementation Details

## 4-1. Data Acquisition

- Dermatological image dataset (multiple skin conditions)
- Multi-class classification problem
- Real-world image variability (lighting, background noise, facial regions)

Key dataset characteristics:

- Class imbalance across disease categories
- Variable image quality
- Background artifacts (face, hair, clothing)

---

## 4-2. Data Preprocessing and Augmentation Process

To improve robustness and generalization, the following preprocessing steps were applied:

### Preprocessing

- Background noise reduction
- Removal of irrelevant facial regions
- Image resizing and normalization
- Class rebalancing strategies

### Augmentation Strategy

- Controlled rotation
- Horizontal/vertical flipping
- Brightness adjustment
- Smoothing techniques

Special emphasis was placed on:

- Reducing false negatives (medical safety priority)
- Improving macro recall and F1-score
- Avoiding excessive augmentation that induces overfitting

---

## 4-3. Software

### Framework

- Python
- TensorFlow / Keras
- Flask (API backend)
- TFLite (deployment optimization)

### Training

- Loss: Categorical Cross-Entropy
- Optimizer: Adam
- Learning Rate Scheduling
- EarlyStopping
- ReduceLROnPlateau
- Epochs: up to 300
- Iterative experimental validation (Ver1–Ver5)

### Hardware

- GPU-based training environment
- Deployment environment optimized for real-time inference

---

# 5. Results

## Performance Improvement

| Metric | Baseline | Final Model | Improvement |
|--------|----------|------------|-------------|
| Accuracy | 0.578 | **0.734** | +15.6% |
| Recall | 0.564 | **0.733** | +16.9% |
| F1-score | 0.560 | **0.736** | +17.6% |
| AUC | 0.800 | **0.8618** | +6.18% |

## Deployment Performance

| Metric | Before Optimization | After Optimization |
|--------|---------------------|--------------------|
| API Response Time | 8.3s | **4.1s** |

### Key Observations

- Transfer learning significantly stabilized training.
- Fine-tuning improved minority class recall.
- Overtraining (Ver4, 100 epochs) led to severe overfitting.
- Carefully controlled augmentation (Ver5) produced the best overall performance.
- TFLite conversion reduced inference latency for web deployment.

---

# 6. Technical Takeaways

## 6-1. Data Quality Matters More Than Model Complexity
Early improvements were driven more by preprocessing and rebalancing than by architecture changes.

## 6-2. Medical AI Requires Metric Reinterpretation
Optimizing for recall and F1-score was more aligned with clinical safety than maximizing accuracy alone.

## 6-3. Iterative Experimentation is Critical
Structured version control (Ver1–Ver5) enabled systematic performance gains.

## 6-4. Deployment Constraints Must Be Considered Early
Model compression (H5 → TFLite) and API optimization significantly improved user experience.

## 6-5. Collaboration and Version Tracking Improve Stability
GitHub-based history tracking and structured experiment logging reduced confusion and integration errors.

---

# 7. Future Work

## 7-1. Attention-Based Lesion Localization
Incorporate Grad-CAM or attention modules for explainability.

## 7-2. Clinical-Grade Dataset Expansion
Train on higher-resolution and clinically verified dermatology datasets.

## 7-3. Class Imbalance Handling with Advanced Loss Functions
Explore Focal Loss or class-balanced loss.

## 7-4. Edge Deployment Optimization
Further reduce latency for mobile or embedded device deployment.

## 7-5. Real-World User Testing and Feedback Loop
Implement continuous data collection and model retraining for system improvement.
