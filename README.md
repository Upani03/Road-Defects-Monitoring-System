# 🛣️ Road Detect Monitoring System

A computer vision pipeline built in Python that extracts frames from road survey videos, analyses and removes noise degradations, and automatically detects common road defects including edge cracking, pedestrian crossings, ravelling, and potholes.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Pipeline Stages](#pipeline-stages)
  - [1. Frame Extraction](#1-frame-extraction)
  - [2. Noise Analysis & Filtering](#2-noise-analysis--filtering)
  - [3. Road Defect Detection](#3-road-defect-detection)
- [Output Folders](#output-folders)
- [Noise Types Detected](#noise-types-detected)
- [Defect Types Detected](#defect-types-detected)
- [Results Summary](#results-summary)

---

## Overview

This project implements a full image processing pipeline for road condition monitoring. Given an input video of a road surface, the system:

1. Extracts 30 equally distributed frames from the video
2. Scores each frame by noise severity and selects the most degraded ones for analysis
3. Converts frames to grayscale and plots pixel intensity histograms
4. Classifies the type of noise present in each frame using statistical analysis
5. Applies the most appropriate noise removal filter per frame
6. Generates side-by-side visual comparisons of original vs. filtered frames
7. Runs four independent defect detectors on the cleaned frames
8. Saves all outputs with annotated bounding boxes and a full summary report

---

## Project Structure

```
project/
│
├── videos/
│   └── all_in_one.mp4          # Input road survey video
│
├── extracted_30_frames/        # 30 equally spaced frames from video
├── grayscale_frames/           # Grayscale versions of selected frames
├── histograms/                 # Pixel intensity histogram plots
├── filtered_frames/            # Noise-removed frames (used for defect detection)
├── comparisons/                # Side-by-side original vs. filtered plots
│
├── detectedEdgeCrackingFinal/  # Frames with edge cracks marked in GREEN
├── detectedCrossingFinal/      # Frames with pedestrian crossings marked in BLUE
├── detectedRavellingFinal/     # Frames with ravelling marked in RED
├── detectedPotholeFinal/       # Frames with potholes marked in CYAN
│
├── noise_detection_report.csv  # Per-frame noise classification table
├── final.ipynb                 # Main Jupyter Notebook (full pipeline)
└── README.md
```

---

## Requirements

- Python 3.8+
- Jupyter Notebook or JupyterLab

### Python Libraries

| Library | Purpose |
|---|---|
| `opencv-python` | Image reading, filtering, morphology, contour detection |
| `numpy` | Array operations and statistical calculations |
| `pandas` | Noise report DataFrame and CSV export |
| `matplotlib` | Histogram plots and comparison figures |

---

## Installation

```bash
# 1. Clone or download the project
git clone <your-repo-url>
cd road-detect-monitoring

# 2. Install dependencies
pip install opencv-python numpy pandas matplotlib

# 3. Place your video inside the videos/ folder
mkdir videos
# Copy your video as: videos/all_in_one.mp4

# 4. Launch Jupyter and open the notebook
jupyter notebook final.ipynb
```

---

## Usage

Open `final.ipynb` in Jupyter and **run all cells in order** from top to bottom. Each stage depends on the outputs of the previous one.

```
Cell 1  → Import libraries
Cell 2  → Verify video file
Cell 3  → Extract 30 frames
Cell 4  → Select frames by noise score
Cell 5  → Grayscale conversion + histogram plots
Cell 6  → Noise classification (statistical analysis)
Cell 7  → Apply noise-specific filters
Cell 8  → Generate original vs. filtered comparisons
Cell 9  → Edge cracking detection
Cell 10 → Pedestrian crossing detection
Cell 11 → Ravelling detection
Cell 12 → Pothole detection
Cell 13 → Full pipeline summary report
```

---

## Pipeline Stages

### 1. Frame Extraction

Frames are sampled at equal intervals across the entire video using `numpy.linspace`, ensuring coverage of all road segments regardless of video length.

```
Video (908 frames) → 30 equally spaced frames → extracted_30_frames/
```

Frames are saved as `frame_01.jpg` through `frame_30.jpg`.

### 2. Noise Analysis & Filtering

#### Frame Selection by Noise Score

Each extracted frame is scored using a composite metric:

| Component | Description | Weight |
|---|---|---|
| Laplacian variance | Measures texture and edge noise | 40% |
| Local std-dev | Captures grain and speckle | 30% |
| Salt-and-pepper ratio | Proportion of extreme pixels (≤5 or ≥250) | 20% |
| Speckle index | Std/mean ratio (multiplicative noise) | 10% |

The 30 highest-scoring frames are selected for the full analysis pipeline.

#### Grayscale Conversion & Histograms

Each frame is converted to grayscale using `cv2.COLOR_BGR2GRAY`. A pixel intensity histogram is plotted and saved alongside the grayscale image for visual inspection of the brightness distribution.

#### Noise Classification

Each frame is statistically analysed to classify the noise type present:

| Noise Type | Detection Method |
|---|---|
| **Gaussian** | Residual std-dev after blur > 8 AND skewness < 1.5 |
| **Salt & Pepper** | More than 0.2% pixels at intensity ≤5 or ≥250 |
| **Speckle** | Mean squared ratio of residual to local mean > 0.004 |
| **Uniform** | Kurtosis < −0.5 (platykurtic distribution) |

Results are saved to `noise_detection_report.csv`.

#### Noise Removal Filters

The filter applied to each frame is chosen based on the detected noise type:

| Detected Noise | Filter Applied |
|---|---|
| Salt & Pepper | Median filter (5×5 kernel) |
| Gaussian | Gaussian blur (5×5 kernel) |
| Speckle | Bilateral filter (d=9, σ=75) |
| Uniform | Gaussian blur (3×3 kernel, mild) |
| None detected | Mild Gaussian blur (3×3, default) |

### 3. Road Defect Detection

All four detectors operate on the noise-filtered grayscale frames. Each saves annotated colour images to its own output folder.

#### Edge Cracking (GREEN)
Uses bilateral filtering, Canny edge detection, and multi-directional morphological operations to identify elongated crack contours. Filters out shadows and vegetation using aspect ratio, solidity, and area constraints. Operates on the lower 70% of the frame (road surface only).

#### Pedestrian Crossing (BLUE)
Applies CLAHE contrast enhancement and multi-level brightness thresholding to detect bright horizontal stripe regions characteristic of zebra crossings. Contours are filtered by width-to-height ratio and solidity.

#### Ravelling (RED)
Detects road surface deterioration (aggregate loss, surface roughness) using multi-scale texture variance analysis, top-hat morphology for exposed aggregate, and Sobel/Laplacian edge density. Combines fine, medium, and coarse scale responses.

#### Pothole (CYAN)
Identifies the darkest connected regions adaptively using the 20th percentile of the local histogram. Filters out uniform shadows by requiring internal texture (std-dev > 8) and compact shape (aspect ratio < 4, solidity > 0.2).

---

## Output Folders

| Folder | Contents |
|---|---|
| `extracted_30_frames/` | Raw JPG frames from the video |
| `grayscale_frames/` | Grayscale versions of all 30 selected frames |
| `histograms/` | PNG histogram plots per frame |
| `filtered_frames/` | Noise-cleaned frames used as input to defect detection |
| `comparisons/` | 2×2 grid plots: original, filtered, histogram overlay |
| `detectedEdgeCrackingFinal/` | Annotated frames — cracks in GREEN boxes |
| `detectedCrossingFinal/` | Annotated frames — crossings in BLUE boxes |
| `detectedRavellingFinal/` | Annotated frames — ravelling in RED boxes |
| `detectedPotholeFinal/` | Annotated frames — potholes in CYAN boxes |
| `noise_detection_report.csv` | Per-frame noise type classification with statistics |

---

## Noise Types Detected

| Type | Description |
|---|---|
| **Gaussian noise** | Random intensity variations due to sensor or lighting conditions |
| **Salt & Pepper noise** | Isolated extreme-value pixels (pure black or pure white) |
| **Uniform noise** | Evenly distributed pixel-level disturbance across the intensity range |
| **Speckle noise** | Multiplicative granular noise proportional to local brightness |

---

## Defect Types Detected

| Defect | Marker Colour | Typical Cause |
|---|---|---|
| Edge Cracking | GREEN | Structural fatigue, edge settlement, sub-base failure |
| Pedestrian Crossing | BLUE | Road marking identification for safety audit |
| Ravelling | RED | Surface aggregate loss, weathering, poor binder |
| Pothole | CYAN | Water ingress, crack propagation, heavy traffic load |

---

## Results Summary

Sample results from `videos/all_in_one.mp4` (908 frames, 30 FPS):

| Stage | Result |
|---|---|
| Frames extracted | 30 |
| Frames with Gaussian noise | 30 |
| Frames with Salt & Pepper noise | 8 |
| Frames with Speckle noise | 24 |
| Frames with Uniform noise | 2 |
| Edge crack detections | 179 (29 frames) |
| Pedestrian crossing detections | 206 (27 frames) |
| Ravelling detections | 5 (2 frames) |
| Pothole detections | 327 (23 frames) |

> **Conclusion:** Noise degradations were successfully identified using histogram analysis and statistical observation. After applying filtering techniques, random pixel disturbances were reduced, image smoothness and clarity improved, and histogram distributions became more stable.
