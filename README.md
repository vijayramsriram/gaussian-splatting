# 🌕 Apollo 17 3D Reconstruction using COLMAP and Gaussian Splatting

This project aims to digitally reconstruct lunar terrain from the Apollo 17 mission using 3D vision techniques. We utilize COLMAP for photogrammetric modeling and apply **Gaussian Splatting** to recover original views and generate novel perspectives. The reconstructed outputs are evaluated using both image metrics and mesh comparisons.

---


## 🛠️ Workflow

### Step 1: Preparing Input Images

- Collected 15 Apollo HR images in `.png` format.
- Preprocessed for COLMAP: renamed and resized as needed.

### Step 2: Sparse Point Cloud with COLMAP

- Detected and matched features across views.
- Generated a sparse 3D structure.

### Step 3: Exporting COLMAP Files

- Saved `cameras.txt`, `images.txt`, `points3D.txt` and placed them in `data/apollo17`.

### Step 4: Dense Mesh Creation

- Built dense point clouds and textured meshes to support visual comparison.

---

## 🌌 Training Gaussian Splatting Model

### Environment Setup

```bash
conda create -n splatter310 python=3.10
conda activate splatter310
pip install -r requirements.txt
```

### Common Fixes

- `plyfile` error resolved using:
  ```bash
  pip install plyfile==0.8.1
  ```
- PyTorch issue with NumPy >=2.0 fixed with:
  ```bash
  pip install numpy==1.24.4
  ```

### Training

```bash
python train.py -s data/apollo17
```

- Point cloud trained for 30k steps
- Output saved in `output/000fab2b-8/point_cloud/iteration_30000/point_cloud.ply`

---

## 🖼️ View Recovery (Original Image Reconstruction)

```bash
python render.py -m output/000fab2b-8 -s data/apollo17 --skip_train --iteration 30000
```

- Reconstructed the original 15 Apollo images from the trained model.
- Recovered outputs stored in:
  - `renders/` (Reconstructed)
  - `gt/` (Original)

#### Ground Truth  
![GT](https://github.com/user-attachments/assets/3cafce5b-d5b4-48fa-a3e6-0cedf72f0672)

#### Reconstructed  
![Rendered](https://github.com/user-attachments/assets/d3379bdc-178f-4944-8c17-f24e0fa0ffd3)

---

## 📏 PSNR/SSIM Evaluation

To evaluate image fidelity:

```bash
python Assignment4_partA.py
```

- Measures difference between recovered and ground truth images using `scikit-image`.

<img width="1552" alt="metrics" src="https://github.com/user-attachments/assets/ad263588-dceb-4f55-850e-90993012f359" />

---

## 🧭 Creating Novel Viewpoints

- Used COLMAP camera centers for PCA-based pose interpolation.
- Created new views and saved them in `novel_poses.json`.

### Render Novel Images

```bash
python nerfstudio/nerfstudio/scripts/gaussian_splatting/render.py camera-path   --model-path output/000fab2b-8   --camera-path-filename novel_poses.json   --output-path output/000fab2b-8/novel_renders/pca_poses/   --output-format images
```

### ⚠️ Problem

- Rendered outputs turned out fully black even with valid poses.
- Likely caused by incorrect viewing bounds or over-cropped splat extents.

---

## 🧪 Mesh Comparison: COLMAP vs Meshroom

- Generated textured meshes using COLMAP and Meshroom.
- Compared results from:
  - Original 15 images
  - Enhanced with 10 new synthesized views

---

## 📊 Performance Metrics

### Quantitative Analysis

- PSNR and SSIM for:
  - Reconstructed vs original
  - Standard vs augmented mesh outputs

### Visual Quality

- Evaluated mesh surface detail and coverage improvements from novel views.

---

## 🧩 Required Packages

```bash
python==3.10
torch>=2.0
numpy==1.24.4
plyfile==0.8.1
scikit-image
opencv-python
Pillow
```

---

## 📝 References

Based on the following projects and resources:

- [3D Gaussian Splatting (Inria)](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)
- [COLMAP SfM Toolkit](https://colmap.github.io/)
- [Apollo 17 Image Dataset](https://www.hq.nasa.gov/alsj/a17/images17.html)
