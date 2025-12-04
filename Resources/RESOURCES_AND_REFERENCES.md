# Resources & References

## Academic Papers & Publications

### 1. SIFT Feature Detection
- **Title**: Distinctive Image Features from Scale-Invariant Keypoints
- **Author**: David G. Lowe
- **Year**: 2004
- **Journal**: International Journal of Computer Vision
- **Key Concepts**:
  - Scale-space theory
  - Keypoint localization
  - Orientation assignment
  - Descriptor matching
- **Reference**: Used for feature detection in this project

### 2. Image Stitching & Panorama Creation
- **Title**: Automatically Stitching Images with Phototour
- **Author**: Brown & Lowe
- **Year**: 2007
- **Key Contributions**:
  - Incremental alignment
  - Exposure compensation
  - Multi-band blending
- **Application**: Core algorithm design

### 3. RANSAC Algorithm
- **Title**: Random Sample Consensus: A Paradigm for Model Fitting
- **Author**: Fischler & Bolles
- **Year**: 1981
- **Conference**: CACM
- **Usage**: Robust homography estimation
- **Advantage**: Outlier rejection capability

### 4. Homography Estimation
- **Title**: Multiple View Geometry in Computer Vision
- **Author**: Hartley & Zisserman
- **Year**: 2004
- **Book**: Cambridge University Press
- **Topics Covered**:
  - Planar homography
  - Perspective transforms
  - Robust estimation methods

### 5. Image Blending & Compositing
- **Title**: The Laplacian Pyramid as a Compact Image Code
- **Author**: Burt & Adelson
- **Year**: 1983
- **Key Idea**: Multi-level blending
- **Implementation**: Used in overlap regions

---

## Algorithm References

### Feature Matching Strategy
```
Reference: FLANN - Fast Approximate Nearest Neighbors
- Author: Marius Muja
- Library: VLFeat (MATLAB), OpenCV (Python)
- Complexity: O(log n) for KD-Tree
- Advantage: Speed over BFMatcher
```

### Gamma Correction for Exposure
```
Formula: I'(x,y) = (I(x,y)/255)^γ × 255

Reference Paper:
- Title: Exposure Compensation for Image Stitching
- Concept: Correcting camera exposure differences
- Method: LAB color space conversion
- Advantage: Perceptually uniform corrections
```

### Pyramid Blending
```
Levels: 3 (Gaussian + Laplacian)
Blend Mask: Quadratic function (smoother than linear)
Formula: output = left_img × (1-mask) + right_img × mask

Where mask = x²  (quadratic for smooth transition)
```

---

## Implementation References Used

### MATLAB Code Implementations (Provided References)
1. **matchExposures.m**
   - Gamma fitting algorithm
   - Exposure matching between image pairs
   - Global optimization with loop closure
   - Implementation: LAB space conversion

2. **merge.m**
   - Distance-weighted blending
   - Warp function integration
   - Multi-image merging
   - Cylindrical projection support

3. **RANSAC.m**
   - Robust transformation estimation
   - Inlier/outlier classification
   - Translation-only model
   - Confidence threshold: 99.9%

4. **getSIFTFeatures.m** & **getMatches.m**
   - VLFeat integration
   - SIFT descriptor extraction
   - Lowe's ratio test implementation
   - Threshold: 0.75

5. **imorder.m**
   - Image sequencing algorithm
   - Forward/backward matching
   - Loop detection
   - Order verification

---

## OpenCV Documentation

### Key Functions Used
```python
# Feature Detection
cv2.SIFT_create()
sift.detectAndCompute(image, mask)

# Feature Matching
cv2.FlannBasedMatcher()
matcher.knnMatch(descriptors1, descriptors2, k=2)

# Transformation
cv2.findHomography(srcPoints, dstPoints, cv2.RANSAC)

# Image Warping
cv2.warpPerspective(image, H, output_size)

# Filtering & Processing
cv2.medianBlur(image, kernel_size)
cv2.GaussianBlur(image, kernel_size, sigma)

# Color Space Conversion
cv2.cvtColor(image, cv2.COLOR_BGR2LAB)
```

---

## Online Learning Resources

### Computer Vision Fundamentals
1. **OpenCV Official Documentation**
   - URL: https://docs.opencv.org/
   - Covers: APIs, tutorials, algorithm explanations

2. **Stanford CS231N: CNNs for Visual Recognition**
   - Lectures on image processing basics
   - Feature extraction concepts

3. **YouTube Channels**
   - First Principles of Computer Vision
   - OpenCV tutorials by sentdex

### Image Stitching Resources
1. **OpenCV Image Stitching Tutorial**
   - Cv2.Stitcher class documentation
   - Multi-image blending examples

2. **Medium Articles**
   - "Image Stitching with OpenCV"
   - "Understanding Homography"

### SIFT & Feature Detection
1. **VLFeat Library Documentation**
   - SIFT parameter tuning guide
   - MATLAB implementation examples

2. **Reddit Communities**
   - r/computervision
   - r/OpenCV

---

## Software & Libraries

### Core Dependencies
```
- OpenCV (cv2) >= 4.5.0
- NumPy >= 1.19.0
- SciPy >= 1.5.0
- Python >= 3.7
```

### Installation
```bash
pip install opencv-python numpy scipy
```

### Alternative Libraries Considered
1. **Scikit-image**: Simpler but slower
2. **PIL/Pillow**: Basic image operations only
3. **Imutils**: Convenience wrapper (recommended)

---

## Mathematical Formulas & Concepts

### 1. Homography Matrix
```
H is a 3×3 matrix:
[h11 h12 h13]
[h21 h22 h23]
[h31 h32 h33]

Maps point (x, y) to (x', y'):
[x']   [h11 h12 h13] [x]
[y'] = [h21 h22 h23] [y]
[w']   [h31 h32 h33] [1]

x' = x'/w', y' = y'/w'
```

### 2. Translation Transform
```
T is pure translation (2D):
[1  0  tx]
[0  1  ty]
[0  0  1 ]

Only x and y shifts, no rotation or scaling
```

### 3. Gamma Correction
```
Output = (Input/255)^γ × 255

γ < 1: Brightens image
γ = 1: No change
γ > 1: Darkens image
```

### 4. Blending Mask (Quadratic)
```
mask(x) = x²  where x ∈ [0, 1]

Properties:
- Smooth derivative
- Non-linear blend
- Favors left image early, right image later
```

### 5. RANSAC Iterations
```
N = log(1 - confidence) / log(1 - inlier_ratio^n_models)

Example:
- Confidence: 99.9%
- Inlier ratio: 10%
- N_models: 1
- Result: ~7 iterations
```

---

## Troubleshooting Resources

### Common Issues & Solutions

#### Issue: Tilted Panorama
**Solution Reference**: homography_transformation.pdf
- Detect near-identity homography
- Switch to translation-only transform
- Use stricter RANSAC threshold

#### Issue: Visible Seams
**Solution Reference**: merge.m & pyramid_blending.md
- Implement pyramid blending
- Use distance-based weighting
- Apply median filtering

#### Issue: Exposure Mismatch
**Solution Reference**: matchExposures.m
- Convert to LAB color space
- Fit gamma correction
- Apply color correction

#### Issue: Feature Matching Failure
**Solution Reference**: RANSAC.m & getMatches.m
- Increase feature count (SIFT parameters)
- Use lower ratio test threshold
- Implement multi-method fallback

---

## Project-Specific Files

### Code Development References
1. **Model 1**: Basic stitching
   - Simple concatenation
   - Minimal feature matching
   
2. **Model 2**: Added blending
   - Sigmoid blending
   - Exposure averaging
   
3. **Model 3**: Advanced transforms
   - Homography + RANSAC
   - Translation fallback
   
4. **Model 4**: Exposure matching
   - Gamma correction
   - Distance weighting

---

## Citation Format

### For Academic Use
```bibtex
@misc{meat_cross_section_stitching_2025,
    author = {Dhirender Pandey},
    title = {Meat Cross-Section Image Stitching using SIFT and RANSAC},
    year = {2025},
    howpublished = {\url{project_url}}
}
```

### MLA Format
```
"Meat Cross-Section Image Stitching Project." 2025. 
Implemented using Python, OpenCV, and SIFT features.
```

---

## Related Projects & Tools

### Similar Open-Source Projects
1. **Hugin**: GUI image stitcher
2. **OpenCV Stitcher**: Built-in class (cv2.Stitcher)
3. **Microsoft Image Composite Editor**: Advanced GUI tool
4. **AutoStitch**: Research implementation

### Useful Tools
- **GIMP**: Manual stitching (comparison)
- **Photoshop**: Professional blending (reference)
- **ImageMagick**: Command-line processing

---

## Further Reading

### Books Recommended
1. "Computer Vision: Algorithms and Applications" - Richard Szeliski
2. "Multiple View Geometry in Computer Vision" - Hartley & Zisserman
3. "Image Processing with Python" - Sanderson

### Journals & Conferences
- IEEE CVPR (Computer Vision and Pattern Recognition)
- ECCV (European Conference on Computer Vision)
- IEEE Transactions on Image Processing
- International Journal of Computer Vision

---

**Resource Collection Date**: December 4, 2025
**Last Updated**: December 4, 2025
