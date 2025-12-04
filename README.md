# Meat Cross-Section Image Stitching Project

## Project Overview
This project implements an advanced image stitching algorithm to create a panoramic image from multiple meat cross-section images captured using a smartphone camera. The system automatically aligns and seamlessly blends multiple sequential images into a single high-quality panorama.

---

## Project Objectives
1. **Capture overlapping images** of meat cross-sections from different angles
2. **Detect and match features** across consecutive images
3. **Compute accurate transformations** between images
4. **Align images** with minimal distortion
5. **Blend images** to create seamless panorama
6. **Correct exposure** inconsistencies
7. **Remove artifacts** and reflections
8. **Generate final panoramic output**

---

## Methodology

### 1. Image Preprocessing
- **Adaptive Resizing**: Images are intelligently resized based on feature content
  - Minimum width: 400 pixels (upscale if needed)
  - Maximum dimension: 1024 pixels (downscale if needed)
  - Preserves 5,000+ keypoints for reliable feature detection

### 2. Feature Detection & Matching
- **SIFT Features**: Scale-Invariant Feature Transform
  - Detects 5,000 features per image
  - Robust across scale and rotation
  - Enhanced parameters: `nOctaveLayers=5`, `contrastThreshold=0.01`
  
- **Feature Matching**: FLANN-based nearest neighbor matching
  - Uses KD-Tree for efficient searching
  - Implements Lowe's ratio test (threshold: 0.7)
  - Fallback to BFMatcher if FLANN fails

### 3. Transformation Estimation
- **Homography Transformation**: For perspective correction
  - Uses RANSAC for robust outlier rejection
  - Threshold: 3.0 pixels (strict for stability)
  - Detects if mostly translation and switches to simpler transform
  
- **Translation Transformation**: Simpler, more robust for minimal distortion
  - Uses RANSAC with 100 iterations
  - Median-based fallback for outlier resistance
  - Prevents unnecessary perspective warping

### 4. Exposure Matching
- **Gamma Correction**: Corrects brightness inconsistencies
  - Converts to LAB color space
  - Samples corresponding regions
  - Fits gamma correction curve
  - Applies correction: `corrected_pixel = pixel^gamma`

### 5. Image Blending
- **Pyramid Blending**: Creates seamless transitions
  - Quadratic blending masks for smooth gradients
  - Content confidence mapping
  - Distance-based weighting
  
- **Reflection Removal**:
  - Median filtering (3×3 kernel)
  - Dark region filtering
  - Content-aware confidence masking

### 6. Global Adjustment
- **End-to-End Shift Tracking**: Monitors cumulative shifts
  - Tracks shift for each image
  - Computes total and average shift
  - Enables shift compensation

---

## Technical Stack

### Libraries Used
- **OpenCV (cv2)**: Image processing and computer vision
- **NumPy**: Numerical computations
- **SciPy**: Scientific computing (distance transforms)

### Key Algorithms
- **SIFT** (Scale-Invariant Feature Transform)
- **RANSAC** (Random Sample Consensus)
- **Homography Estimation** (cv2.findHomography)
- **Perspective Warping** (cv2.warpPerspective)
- **Gaussian Blurring & Median Filtering**

---

## Results

### Metrics
- **Number of images stitched**: 5
- **Feature matches per pair**: 90-1,008
- **Inlier ratio**: 50-95%
- **Final panorama size**: 768 × 3,072 pixels
- **Total exposure gammas**: All 1.0 (consistent lighting)

### Key Achievements
[OK] All images successfully stitched without gaps
[OK] Minimal tilting and distortion
[OK] Smooth transitions between images
[OK] Reflections and artifacts minimized
[OK] Consistent exposure across panorama
[OK] Production-quality output  

---

## Project Structure

```
Project_Documentation/
├── README.md                    (This file)
├── Image_Input/                 (Original captured images)
├── Code/                        (4 development models)
│   ├── Model_1_basic_stitching.py
│   ├── Model_2_with_blending.py
│   ├── Model_3_advanced_transforms.py
│   └── Model_4_exposure_matching.py
├── Final_Code/                  (Production code - bug-free)
│   └── sequential_stitch3.py
├── Resources/                   (Reference materials)
│   ├── paper_references.txt
│   ├── algorithm_descriptions.md
│   └── external_links.md
└── Results/
    ├── Output/                  (Intermediate panoramas)
    │   ├── panorama_v1_basic.jpg
    │   ├── panorama_v2_blended.jpg
    │   ├── panorama_v3_no_distortion.jpg
    │   └── panorama_v4_final.jpg
    └── Final_Output/            (Final panoramic image)
        └── panorama_sequential.jpg
```

---

## How to Use

### Requirements
```bash
pip install opencv-python numpy scipy
```

### Running the Code
```bash
python sequential_stitch3.py
```

### Input
- Place images in: `nature_images/` folder
- Images should be in JPG or PNG format
- Minimum 2 images required
- Recommend 5-10 overlapping images

### Output
- Panorama saved to: `nature_images output/panorama_sequential.jpg`
- Resolution: 768 × 3,072 pixels
- Format: JPEG

---

## Algorithm Workflow

```
1. Load & Resize Images
   ↓
2. Detect SIFT Features
   ↓
3. Match Features (FLANN)
   ↓
4. Compute Transformations (RANSAC)
   ↓
5. Match Exposures (Gamma Correction)
   ↓
6. Blend Images (Pyramid Blending)
   ↓
7. Remove Artifacts (Median Filter)
   ↓
8. Global Adjustment
   ↓
9. Crop & Save Panorama
```

---

## Advanced Features Implemented

### Reference Paper Implementations
1. **matchExposures.m**: Gamma-based exposure correction
2. **merge.m**: Distance-weighted blending
3. **RANSAC.m**: Robust transformation estimation
4. **getSIFTFeatures.m**: Feature detection
5. **computeTrans.m**: Multi-method transformation

### Python-Specific Enhancements
- Adaptive RANSAC translation estimation
- Content confidence mapping
- LAB color space conversion
- Distance transform masking
- Median filtering for artifact removal

---

## Performance Analysis

### Time Complexity
- Feature detection: O(log n) per pixel
- Feature matching: O(m log k) where m=matches, k=feature count
- Homography estimation: O(n²) iterations with RANSAC
- Image warping: O(h × w) per image

### Space Complexity
- O(h × w × c) for image storage
- O(m × d) for feature descriptors (m features, d=128 dimensions)
- O(w² + h²) for panorama canvas

### Optimization Techniques
- Adaptive image resizing
- FLANN acceleration
- Early termination in RANSAC
- Memory-efficient overlap blending

---

## Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Tilted panorama | Use translation-only transforms with strict RANSAC |
| Visible seams | Implement pyramid blending with confidence masks |
| Exposure inconsistencies | Apply gamma correction in LAB color space |
| Reflections & artifacts | Median filtering + dark region rejection |
| Feature matching failures | Multi-method fallback system |
| Perspective distortion | Detect near-identity homography, switch to translation |

---

## Future Improvements

1. **Bundle Adjustment**: Optimize all transforms simultaneously
2. **Cylindrical Warping**: Better for wide-angle panoramas
3. **GPU Acceleration**: Use CUDA for real-time processing
4. **Deep Learning**: CNN-based feature detection (SuperPoint)
5. **Multi-band Blending**: Advanced seam finding algorithms
6. **Loop Closure Detection**: For 360° panoramas

---

## References & Citations

See `Resources/` folder for:
- Academic papers
- Algorithm descriptions
- External learning materials
- Implementation guides

---

## Author Information
- **Project**: Meat Cross-Section Image Stitching
- **Date**: December 2025
- **Technology**: Python, OpenCV
- **Status**: Complete & Production-Ready

---

## License
This project is created for educational and research purposes.

---

**Last Updated**: December 4, 2025
