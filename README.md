# Road Defect Detection System

This project contains four Jupyter notebooks for detecting different types of road defects from video files. Each notebook processes a video, extracts frames, analyzes image quality, applies adaptive filtering, and detects specific road defects.

## Prerequisites

### System Requirements
- Python 3.8 or higher
- Jupyter Notebook or JupyterLab
- Windows/Linux/Mac with OpenCV support

### Installation

1. **Create and activate virtual environment:**
   ```bash
   python -m venv .venv
   .venv\Scripts\activate  # On Windows
   # or source .venv/bin/activate  # On Mac/Linux
   ```

2. **Install required packages:**
   ```bash
   pip install opencv-python numpy pandas matplotlib pillow
   ```

3. **Launch Jupyter:**
   ```bash
   jupyter notebook
   ```

## Project Structure

```
new imageprocessing/
├── videos/                              # Video input files
│   ├── pedestrianCrossingMarkRemoval_01.mp4
│   ├── edgeCracking_06.mp4
│   ├── ravelling.mp4
│   └── roadPothholes.mp4
│
├── video1.ipynb                         # Pedestrian crossing detection
├── video2.ipynb                         # Edge cracking detection
├── video3.ipynb                         # Ravelling detection
├── video4.ipynb                         # Pothole detection
│
├── extracted_All_framesVideo1/          # Extracted frames from each video
├── extracted_All_framesVideo2/
├── extracted_All_framesVideo3/
├── extracted_All_framesVideo4/
│
├── filteredVideo1/                      # Adaptively filtered frames
├── filteredVideo2/
├── filteredVideo3/
├── filteredVideo4/
│
├── detectedCrossingVideo1/              # Detection output with marked boxes
├── detectedEdgeCrackingVideo2/
├── detectedRavellingVideo3/
├── detectedPotholeVideo4/
│
└── README.md                            # This file
```

## Video Notebooks Overview

### Video1: Pedestrian Crossing Detection
**Input:** `pedestrianCrossingMarkRemoval_01.mp4`  
**Purpose:** Detects pedestrian crossing stripes and white lane markings  
**Output folder:** `detectedCrossingVideo1/`  
**Detection method:** White paint stripe detection using morphological operations

**How to run:**
1. Open `video1.ipynb` in Jupyter
2. Run cells in order (1-10)
3. Check output in `detectedCrossingVideo1/` folder

---

### Video2: Edge Cracking Detection
**Input:** `edgeCracking_06.mp4`  
**Purpose:** Detects road edge cracks and margin cracking patterns  
**Output folder:** `detectedEdgeCrackingVideo2/`  
**Detection method:** Multi-directional edge detection with curvature analysis

**Key features:**
- Detects both linear and curved crack patterns
- Analyzes vertex count for curvature detection
- Uses multi-directional morphological kernels

**How to run:**
1. Open `video2.ipynb` in Jupyter
2. Run cells in order (1-10)
3. Check output in `detectedEdgeCrackingVideo2/` folder

---

### Video3: Ravelling Detection
**Input:** `ravelling.mp4`  
**Purpose:** Detects surface ravelling (aggregate loss, texture degradation)  
**Output folder:** `detectedRavellingVideo3/`  
**Detection method:** Light spot detection and multi-scale texture analysis

**Key features:**
- Detects light/bright spots (exposed aggregate)
- Multi-scale texture variance analysis
- Identifies rough surface regions

**How to run:**
1. Open `video3.ipynb` in Jupyter
2. Run cells in order (1-10)
3. Check output in `detectedRavellingVideo3/` folder

---

### Video4: Pothole Detection
**Input:** `roadPothholes.mp4`  
**Purpose:** Detects road potholes and surface depressions  
**Output folder:** `detectedPotholeVideo4/`  
**Detection method:** Adaptive dark region detection

**Key features:**
- Detects darkest connected regions (potholes appear darker)
- Adaptive thresholding based on pixel histogram
- Multi-frame analysis for consistency

**How to run:**
1. Open `video4.ipynb` in Jupyter
2. Run cells in order (1-12)
3. Check output in `detectedPotholeVideo4/` folder

---

## Step-by-Step Execution Guide

### For Each Notebook, Follow This Sequence:

**Cell 1-2:** Import libraries and set paths
- Sets video file path
- Creates output folder structure

**Cell 3:** Open video file
- Verifies video can be opened
- Displays video properties (FPS, duration, total frames)

**Cell 4:** Extract frames
- Saves every 5th frame as JPG
- Creates `extracted_All_framesVideoX/` folder with numbered images

**Cell 5:** Degradation analysis
- Analyzes 30 random frames
- Detects: noise, illumination issues, blur
- Displays histograms
- Creates pandas DataFrame with degradation flags

**Cell 6:** Adaptive filtering
- Applies denoising to noisy frames
- Applies CLAHE contrast enhancement to illumination-affected frames
- Applies sharpening to blurry frames
- Saves filtered images to `filteredVideoX/` folder

**Cell 7:** Defect-specific detection
- Runs detection algorithm on filtered frames
- Marks detected defects with colored bounding boxes
- Saves marked images to `detectedVideoX/` folder
- Displays preview of detections

### Example Execution (Video1):

```
1. jupyter notebook
2. Open video1.ipynb
3. Run all cells sequentially (Ctrl+Enter)
4. Wait for frame extraction (~30-60 seconds)
5. Wait for degradation analysis (~30-60 seconds)
6. Wait for filtering (~30-60 seconds)
7. Wait for detection (~30-60 seconds)
8. View results in detectedCrossingVideo1/ folder
```

---

## Output Interpretation

### Detection Output Files

Each detection output folder contains images with colored bounding boxes:

| Video | Color | Defect Type |
|-------|-------|-------------|
| video1 | Green | Pedestrian crossing stripes |
| video2 | Green | Edge cracks |
| video3 | Red | Ravelling regions |
| video4 | Red | Potholes/dark regions |

### Console Output Example

```
Processed frames: 139
Total potholes detected: 1154
Frames with detections: 139
Output: detectedPotholeVideo4/
```

### Understanding Statistics

- **Processed frames:** Total number of extracted frames analyzed
- **Total detected:** Total number of defects marked across all frames
- **Frames with detections:** Number of frames that contain at least one detection
- **Average per frame:** Detections ÷ frames with detections

---

## Troubleshooting

### Issue: "FileNotFoundError: No images found in 'extracted_All_framesVideoX'"

**Solution:**
- Frame extraction failed; check if video file exists and is readable
- Run the video validation cell to verify file can be opened
- Check `videos/` folder for correct filename spelling

### Issue: "Error: Cannot open video file"

**Solution:**
- Verify video file path is correct
- Check video file format (should be .mp4)
- Try using a different video codec
- Ensure video file is not corrupted

### Issue: No detections found (0 defects detected)

**Solution:**
- Check if defects are visible in the original extracted frames
- Defect characteristics may not match algorithm expectations
- Try adjusting detection thresholds in the detection cell
- Review sample frames in `detectedVideoX/` folder (may show faint markings)

### Issue: Too many false detections

**Solution:**
- Reduce the detection sensitivity by adjusting thresholds
- Increase minimum area constraints
- Modify solidity or aspect ratio filters
- Review Cell 7 (detection cell) and adjust parameters

---

## Customization Guide

### To Adjust Detection Sensitivity

Open the detection cell (Cell 7) in your notebook and modify these parameters:

**For video1 (Pedestrian crossing):**
- Line: `_, white_mask = cv2.threshold(gray_eq, 185, 255, cv2.THRESH_BINARY)`
- Lower threshold (e.g., 180) = more sensitive
- Higher threshold (e.g., 190) = less sensitive

**For video2 (Edge cracking):**
- Line: `edges = cv2.Canny(bilateral, 40, 120)`
- Lower first value = more sensitive to edges

**For video3 (Ravelling):**
- Line: `cv2.threshold(bright_spots, 50, 255, cv2.THRESH_BINARY)`
- Lower threshold = detect darker spots as ravelling

**For video4 (Potholes):**
- Line: `dark_threshold = max(50, min(100, dark_threshold_idx))`
- Increase upper value = detect lighter areas as potholes

---

## Performance Notes

| Video | Frames | Duration | Typical Runtime |
|-------|--------|----------|-----------------|
| video1 | ~300 | ~10 sec | 3-5 minutes |
| video2 | ~200 | ~7 sec | 2-4 minutes |
| video3 | ~140 | ~5 sec | 2-3 minutes |
| video4 | ~140 | ~23 sec | 2-3 minutes |

**Note:** Runtimes depend on system specs and image resolution (640x480)

---

## Output File Formats

- **Extracted frames:** JPG (640×480 resolution)
- **Filtered frames:** JPG (adaptive preprocessing applied)
- **Marked detections:** JPG (original + colored bounding boxes + label count)
- **Analysis data:** Pandas DataFrame (displayed in notebook, not saved)

---

## Quality Assurance Checklist

Before considering detection complete:

- [ ] Video file opens successfully (check console output for FPS, frame count)
- [ ] Frames extracted to correct folder (check extracted_All_framesVideoX/)
- [ ] Degradation analysis shows appropriate flags (noise/illumination/blur)
- [ ] Filtered frames show preprocessing applied (check filteredVideoX/)
- [ ] Detection output folder created (check detectedVideoX/)
- [ ] Sample previews display in notebook
- [ ] Detection count > 0 (or expected for your video content)

---

## Contact & Support

For issues or questions:
1. Review the troubleshooting section above
2. Check cell output messages for specific errors
3. Verify input video files are in `videos/` folder
4. Try running a single cell at a time to isolate issues

---

## Version Info

- **Python:** 3.8+
- **OpenCV:** 4.5+
- **NumPy:** 1.19+
- **Pandas:** 1.2+
- **Matplotlib:** 3.3+
- **Pillow:** 8.0+

Last updated: May 2026
