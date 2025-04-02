
# Contour Based Semantic Segmentation for RBC Morphology Analysis
🚀 *Pixel-wise Red Blood Cell (RBC) Segmentation using UNet for Morphological Anemia Analysis*

---

## 📌 Project Overview

This project focuses on building a **deep learning-based pipeline** to segment red blood cells (RBCs) from blood smear images. The primary goal is to isolate individual RBCs using **semantic segmentation**, enabling downstream analysis such as cell classification and morphological diagnosis of anemia.

The model is designed using a **UNet architecture**, optimized for **pixel-wise mask prediction** on binary images. This phase is the foundation for further feature extraction and classification of anemia types.

---

## 🧬 Objective

To detect and isolate RBCs from binary blood smear images to support classification into:

- 🔴 **Normocytes** – Normal-sized, round RBCs with central pallor  
- 🔵 **Microcytes** – Small RBCs, often present in iron deficiency anemia  
- 🟠 **Macrocytes** – Large RBCs, associated with B12/folate deficiency  
- 🟣 **Elliptocytes** – Oval-shaped RBCs, common in hereditary elliptocytosis  
- 🎯 **Target Cells** – RBCs with a bullseye-like appearance, seen in thalassemia/liver disease

---

## 📂 Dataset

- **Source**: AneRBC-II Dataset (Open-access)  
- **Total Images**: 36,000  
  - 18,000 from **Anemic** patients  
  - 18,000 from **Healthy** individuals  
- Each category contains:
  - `Original_images/`: Raw microscope images  
  - `Binary_segmented/`: Black RBCs on white background (used for training)  
  - `RGB_segmented/`: Color-isolated RBCs  
- **Preprocessing**:
  - Resized all images to **224×224** to match model input requirements

---

## 🔬 Phase 1: Semantic Segmentation using UNet

### 🧠 Why UNet?

UNet is a **fully convolutional network** designed for biomedical image segmentation. It excels at **pixel-level labeling** using a **U-shaped encoder–decoder architecture** with **skip connections**. This allows the model to learn **contextual features (what)** and **spatial localization (where)** simultaneously.

---

### 🏗 UNet Architecture (Used in This Project)

- **Input shape**: `(224, 224, 1)` – grayscale binary images
- **Output**: Single-channel binary mask (pixel values in range [0, 1])
- **Total Conv2D Layers**: 14
- **Pooling/Upsampling Layers**: 3 each
- **Skip Connections**: Between each encoder and corresponding decoder layer

#### 🔻 Encoder (Contracting Path):
1. Conv2D → ReLU → Conv2D → ReLU  
2. MaxPooling2D (downsampling)
3. Repeated 3 times, doubling filters at each stage (64 → 128 → 256)

#### 🎯 Bottleneck:
- 2 Conv2D layers with 512 filters

#### 🔼 Decoder (Expanding Path):
1. UpSampling2D → Concatenate skip connection  
2. Conv2D → ReLU → Conv2D → ReLU  
3. Repeated 3 times, reducing filters each time (256 → 128 → 64)

#### 🧾 Output Layer:
- Final Conv2D (1×1 kernel) with Sigmoid activation to predict per-pixel probability

---

## ⚙️ Training Configuration

- **Loss Function**:  
  - `Binary Crossentropy` → for pixel-level classification  
  - `Dice Loss` → to maximize mask overlap (especially important due to class imbalance)

- **Optimizer**:  
  - `Adam` (Adaptive Moment Estimation)  
  - Chosen for its speed and adaptive learning rate capabilities

- **Batch Size**: 32  
- **Epochs**: 10  
- **Validation Split**: 20%

---

## 📊 Results & Performance

| Metric             | Value     |
|--------------------|-----------|
| Training Accuracy  | 99.80%    |
| Validation Accuracy| 89.64%    |
| Mean IoU (Val)     | ~0.30     |

### 🧪 Visual Output:
- Masks showed clear, crisp RBC boundaries  
- RBCs remained separated in most cases  
- Minimal merging, even in dense cell clusters  
- Average ~16–20 cells segmented per image

---

## ❌ Dropped Methods

### 🔻 Mean Shift Segmentation
- Initially applied to CLAHE-enhanced RGB images using `cv2.pyrMeanShiftFiltering()`
- **Dropped due to**:
  - High computational cost
  - Tendency to merge overlapping RBCs
  - Inefficiency on large-scale datasets

### 🔻 CLAHE (Binary)
- Applied CLAHE only to RGB images for visual enhancement  
- **Skipped for binary inputs** due to already strong contrast

---

## 🔜 Future Plans ?

With successful segmentation completed, the next phase can be:

- ✂️ **Cropping** each segmented RBC from original images  
- 📐 **Feature Extraction**:
  - Shape: area, eccentricity, circularity  
  - Texture: entropy, contrast  
  - Color (if using RGB): mean hue, saturation  
- 🧠 **Classification** of RBCs using a CNN or MLP classifier trained on these features

---
