# Model Architecture: Data Flow & Processing

## Overview
This model implements a **Generative Adversarial Network (GAN)** architecture that incorporates both text and image features to generate synthetic images. The model utilizes **text embedding** and **latent encoding** in combination with a **U-Net** architecture for both **downsampling (contracting path)** and **upsampling (expanding path)**. The discriminator evaluates the generated images by considering both the image data and the conditional features.

## 3.2 Data Flow & Processing

### 1. **Text and Latent Encoding**
The processing begins with the **text embedding** (2010-dimensional) that is passed through a **three-layer fully connected neural network**. This neural network generates a **256-dimensional feature vector** from the text input.

- **Text embedding processing**:
  - Input: 2010-dimensional text embedding.
  - Output: 256-dimensional feature vector.

The **source encoding**, which is handled through a separate fully connected layer, also generates a **256-dimensional vector**.

- **Source encoding processing**:
  - Input: Source data.
  - Output: 256-dimensional feature vector.

The generated **text** and **source feature vectors** are reshaped and interpolated to a **4 × 4 spatial size** for fusion with the image features.

### 2. **U-Net Downsampling (Contracting Path)**
The **input image** is processed through six **convolutional layers** (UNetDown blocks). As the data passes through the layers, the spatial dimensions progressively reduce while the depth increases. The output feature maps from each downsampling step are saved for later use in the **upsampling** phase, using **skip connections**.

- **Downsampling Path**:
  - Input: Image data.
  - Process: Convolutional layers progressively reduce spatial dimensions and increase feature depth.
  - Output: Feature maps for skip connections.

### 3. **Bottleneck: Fusing Text & Latent Features**
At the **bottleneck**, the **deepest feature map** (from the last downsampling layer) is resized to **4 × 4 spatial size**. This feature map is then concatenated with the **text** and **source features**, increasing the number of channels in the network.

- **Concatenation**:
  - Input: Deepest feature map, text feature vector, source feature vector.
  - Process: Concatenation increases the number of channels.
  - Output: Combined feature map.

A **1×1 convolution** is applied to reduce the channel dimensions before the data is passed through the **bottleneck convolutional layer**.

### 4. **U-Net Upsampling (Expanding Path)**
The **upsampling path** mirrors the downsampling path but includes **skip connections** to retain fine-grained details. During this phase:

- **Feature maps** from earlier downsampling layers are resized and concatenated with the data at each upsampling step.
- **1×1 convolutions** adjust channel dimensions before applying **transpose convolutions** to achieve upsampling.

- **Upsampling Path**:
  - Input: Combined feature map from the bottleneck.
  - Process: Transpose convolutions and skip connections for upsampling.
  - Output: Upampled feature map.

### 5. **Final Image Generation**
After upsampling, the last feature map is passed through a **final convolutional layer** (FinalLayer) to generate the output image. The output image maintains the same number of channels as the input image, preserving its **RGB format**.

- **Final Output**:
  - Output: Image in RGB format (same number of channels as input).

---

## 5. Discriminator Architecture

The **discriminator** is responsible for processing both the generated image data and its conditional information (such as the text description) to classify the image as **real or fake**. The discriminator uses a **progressive downsampling** pattern, with the feature channels increasing as the spatial dimensions reduce.

### **Discriminator Layers**
The discriminator consists of a series of **five convolutional layers**:

- **Layer 1 to 4**: **2D-convolutional layers** with **LeakyReLU activation function**.
- **Layer 5**: A **final 2D-convolutional layer** to produce a **single scalar value** for real/fake classification.

- **Feature Progression**:
  - **Condition Integration**: Early fusion strategy by concatenating the conditional data (e.g., text description) with image features.
  - **Output**: A single scalar value indicating real/fake classification.

### **Feature Channel Expansion**:
- The channels progress as follows:
  - Initial: 4 channels (RGB + condition) at **64×64 resolution**.
  - Progression: **64 → 128 → 256 → 512** channels.

### **Spatial Reduction**:
- The spatial resolution reduces as follows:
  - Initial: **64×64 → 32×32 → 16×16 → 8×8 → 4×4 → 1×1**.

---

### **Architecture Diagram**
*If you have an architecture diagram for the model, you can insert it here for better visualization.*

---

## Summary
This architecture utilizes both text and image features to generate synthetic images through a **U-Net-based network**. The generator progressively downsamples and upsamples feature maps while incorporating text and latent information. The discriminator uses a series of convolutional layers to classify images based on the combination of image data and its conditional inputs.

