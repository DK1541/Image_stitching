# Usage Guide - Running the Final Stitching Code

## Quick Start (5 Minutes)

### 1. Prerequisites
```powershell
pip install opencv-python numpy scipy
```

### 2. Prepare Images
- Place 5+ overlapping JPEG/PNG images in: `C:\Users\dhirender.pandey\meat cross-section stitching\nature (code, image, output)\nature_images\`
- Ensure 25-35% overlap between consecutive images

### 3. Run Code
```powershell
cd "C:\Users\dhirender.pandey\meat cross-section stitching\nature (code, image, output)\nature_code"
python "Final_Code/sequential_stitch3_FINAL.py"
```

### 4. Find Output
```
C:\Users\dhirender.pandey\meat cross-section stitching\nature (code, image, output)\nature_images output\panorama_sequential.jpg
```

---

## Detailed Installation

### Step 1: Install Python 3.7+

**Check if installed**:
```powershell
python --version
```

**Install from** [python.org](https://www.python.org/downloads)

### Step 2: Install Required Libraries

**Option A - Individual Installation**:
```powershell
pip install opencv-python
pip install numpy
pip install scipy
```

**Option B - Batch Installation**:
```powershell
pip install opencv-python numpy scipy
```

**Verify Installation**:
```powershell
python -c "import cv2; import numpy; import scipy; print('✓ All libraries installed')"
```

### Step 3: Prepare Input Images

**Folder Structure**:
```
nature_images/
├── image1.jpg
├── image2.jpg
├── image3.jpg
├── image4.jpg
└── image5.jpg
```

**Image Requirements**:
- Format: JPG, JPEG, or PNG
- Minimum width: 400 pixels (will be resized if larger)
- Overlap: 25-35% between consecutive images
- Count: Minimum 2 images (5+ recommended)

**Optimal Specifications**:
- Resolution: 1024×768 or similar aspect ratio
- Format: JPEG (best compression)
- Quality: High-quality source images
- Content: Clear, well-lit subjects

### Step 4: Run the Script

**Navigate to code folder**:
```powershell
cd "C:\Users\dhirender.pandey\meat cross-section stitching\nature (code, image, output)\nature_code"
```

**Run script**:
```powershell
python "Final_Code/sequential_stitch3_FINAL.py"
```

### Step 5: Access Output

**Output location**:
```
C:\Users\dhirender.pandey\meat cross-section stitching\nature (code, image, output)\nature_images output\panorama_sequential.jpg
```

**Typical output**:
- Dimensions: 768×3,072 pixels (for 5 images)
- File size: 2.5-3.2 MB
- Format: JPEG (8-bit, 3-channel, BGR)

---

## Configuration and Customization

### Basic Parameters (Modify in Code)

**Location**: Lines 17-20 in `sequential_stitch3_FINAL.py`

```python
MIN_WIDTH = 400           # Minimum width threshold
MAX_DIM = 1024            # Maximum dimension
MIN_KEYPOINTS = 500       # Feature threshold
```

**Effect**:
- ↑ MIN_WIDTH = Process larger images, slower
- ↓ MIN_WIDTH = Faster processing, may lose features
- ↑ MAX_DIM = Higher quality, slower
- ↓ MAX_DIM = Faster, lower quality

### Advanced Parameters

**SIFT Feature Detection** (Lines 70-76):
```python
sift = cv2.SIFT_create(
    nfeatures=5000,           # Max features (↑ = more features)
    nOctaveLayers=5,          # Scale levels (↑ = multi-scale)
    contrastThreshold=0.01,   # Sensitivity (↓ = more features)
    edgeThreshold=15,         # Edge threshold
    sigma=1.6                 # Gaussian sigma
)
```

**FLANN Matching** (Lines 108-110):
```python
index_params = dict(algorithm=FLANN_INDEX_KDTREE, trees=5)
search_params = dict(checks=50)
```

**RANSAC Settings** (Line 155):
```python
H, mask = cv2.findHomography(src_pts, dst_pts, cv2.RANSAC, 
                             ransacReprojThreshold=3.0)  # ↑ = more points kept
```

**Blending** (Lines 220):
```python
blend_mask = np.power(x, 2)  # 2 = quadratic (change for different curves)
```

---

## Troubleshooting Guide

### Issue 1: "Module not found" Error

**Error Message**:
```
ModuleNotFoundError: No module named 'cv2'
```

**Solution**:
```powershell
pip install --upgrade opencv-python
```

### Issue 2: Images Not Found

**Error Message**:
```
Need at least 2 images
```

**Solution**:
1. Verify folder path: `nature_images/`
2. Check file extensions (.jpg, .jpeg, .png)
3. Ensure files are readable
4. Try with test images first

### Issue 3: Slow Processing

**Symptoms**: Takes > 15 seconds

**Solutions**:
```python
# Option 1: Reduce MAX_DIM
MAX_DIM = 800  # (default: 1024)

# Option 2: Reduce SIFT features
nfeatures=2500  # (default: 5000)

# Option 3: Increase contrastThreshold
contrastThreshold=0.02  # (default: 0.01)
```

### Issue 4: Low Quality Output

**Symptoms**: Visible seams, tilting

**Solutions**:
```python
# Option 1: Increase overlap width
overlap_width = int(abs(shift) * 0.8)  # was 0.7

# Option 2: Increase blend kernel
cv2.GaussianBlur(img, (7, 7), 0)  # was (5, 5)

# Option 3: Stricter RANSAC
ransacReprojThreshold=2.0  # was 3.0
```

### Issue 5: Memory Error

**Error Message**:
```
MemoryError: Unable to allocate
```

**Solution**:
```python
# Reduce image size
MAX_DIM = 512  # (was 1024)

# Or reduce number of features
nfeatures=2000  # (was 5000)
```

### Issue 6: No Matches Found

**Error Message**:
```
Matches found: 0
```

**Causes & Solutions**:
- **Problem**: Insufficient overlap
  - **Fix**: Ensure 25-35% overlap between images
  
- **Problem**: Poor image quality
  - **Fix**: Use sharper, better-lit source images
  
- **Problem**: Very different images
  - **Fix**: Increase SIFT features or lower contrastThreshold

---

## Understanding Console Output

### Example Output

```
Loading images with adaptive resizing...
  Processing image1.jpg...
    Downscaled to 1024x768
    Final size: 1024x768 with 5132 keypoints
  Processing image2.jpg...
    Final size: 1024x768 with 5089 keypoints
Loaded 5 images

Detecting features...
  Image 1: 5132 features
  Image 2: 5089 features
  Image 3: 4956 features
  Image 4: 5201 features
  Image 5: 5078 features

Starting sequential stitching with exposure matching...
Starting with image 1: (768, 1024, 3)

Stitching image 2...
  Matches found: 1008
  ✓ Homography computed
  Shift: 725.3, Overlap: 350
  Exposure gamma: 1.000
  Panorama shape now: (768, 1536, 3)

Stitching image 3...
  Matches found: 890
  ✓ Homography computed
  Shift: 710.2, Overlap: 340
  Exposure gamma: 0.999
  Panorama shape now: (768, 2304, 3)

...

Applying global adjustment...
  Total shift: 2976.5, Average per image: 744.1

Cropping black borders...
  Cropped to: (768, 3072, 3)

✓ Panorama saved to: C:\...\panorama_sequential.jpg
Final panorama size: (768, 3072, 3)
```

### Interpreting Results

**Good Signs** ✓:
- ✓ 5000+ keypoints per image
- ✓ 800+ matches per pair
- ✓ Homography successfully computed
- ✓ Gamma close to 1.0
- ✓ Panorama size increasing
- ✓ Final crop successful

**Warning Signs** ⚠:
- ⚠ < 500 keypoints → Image too small
- ⚠ < 100 matches → Insufficient overlap
- ⚠ Translation fallback → Check homography
- ⚠ Gamma > 1.5 → Exposure mismatch

**Error Signs** ✗:
- ✗ Homography failed → Matches too low
- ✗ No panorama saved → File I/O error
- ✗ Memory error → Reduce image size

---

## Advanced Usage

### Using Different Input Paths

**Modify lines 498-499**:
```python
folder = r"C:\path\to\your\images"
output = r"C:\path\to\output\panorama.jpg"
```

### Batch Processing Multiple Folders

**Create wrapper script** (batch_process.py):
```python
import os
from sequential_stitch3_FINAL import *

folders = [
    r"C:\data\batch1",
    r"C:\data\batch2",
    r"C:\data\batch3",
]

for folder in folders:
    output_name = os.path.basename(folder)
    output = f"output\{output_name}_panorama.jpg"
    
    images, _ = load_images_from_folder(folder)
    keypoints, descriptors = [], []
    
    for img in images:
        kp, des = detect_features(img)
        keypoints.append(kp)
        descriptors.append(des)
    
    pano = stitch_sequential(images, keypoints, descriptors)
    cv2.imwrite(output, pano)
    print(f"✓ Saved: {output}")
```

### Adjusting for Different Image Types

**For wide-angle images**:
```python
# Reduce MAX_DIM
MAX_DIM = 800

# Use translation-only (already default)
prefer_translation = True
```

**For high-resolution images**:
```python
# Increase features
nfeatures = 8000

# More RANSAC iterations
ransac_iterations = 200
```

**For low-resolution images**:
```python
# Increase MIN_WIDTH
MIN_WIDTH = 600

# More aggressive downscaling
MAX_DIM = 512
```

---

## Performance Tips

### Speed Optimization
1. **Reduce image size**: ↓ MAX_DIM (faster)
2. **Reduce features**: ↓ nfeatures (faster, less accurate)
3. **Use FLANN**: ✓ Already enabled (10× faster)
4. **Reduce iterations**: ↓ ransac_iterations (faster)

### Quality Optimization
1. **Increase features**: ↑ nfeatures (more accurate)
2. **Stricter threshold**: ↓ ransacReprojThreshold (cleaner)
3. **Better overlap**: Ensure 25-35% overlap
4. **Higher contrast**: Use well-lit images

### Memory Optimization
1. **Reduce MAX_DIM**: Smaller images = less memory
2. **Process fewer images**: Reduce file count
3. **Lower nfeatures**: Fewer descriptors = less RAM
4. **Clear cache**: Manually clear variables

---

## Exporting Results

### For Web
```python
# Reduce quality for smaller file size
cv2.imwrite(output, pano, [cv2.IMWRITE_JPEG_QUALITY, 80])
# Result: ~1-2 MB file
```

### For Print
```python
# Maximum quality for printing
cv2.imwrite(output, pano, [cv2.IMWRITE_JPEG_QUALITY, 95])
# Result: ~2.5-3.2 MB file

# Can print up to 300 DPI with this quality
# At 96 DPI: 32 × 8 inches
# At 300 DPI: 10 × 2.6 inches
```

### For PowerPoint/Presentation
```python
# Balanced quality and file size
cv2.imwrite(output, pano, [cv2.IMWRITE_JPEG_QUALITY, 90])
# Result: ~2-2.5 MB file

# Suitable for projection and screen display
```

---

## Testing and Validation

### Test with Sample Images
1. Use simple, well-overlapped images
2. Start with 2-3 images
3. Gradually increase complexity
4. Verify output before scaling up

### Quality Checks
1. **Visual**: Inspect panorama for seams
2. **Metrics**: Check console output
3. **Dimensions**: Verify expected width
4. **File**: Ensure JPEG saved successfully

### Debugging Strategy
1. Check console output messages
2. Verify input images are valid
3. Try with different overlap
4. Reduce image count if failing
5. Adjust parameters gradually

---

## FAQ (Frequently Asked Questions)

**Q: How long should it take to stitch 5 images?**  
A: Typically 8-10 seconds for 1024×768 images

**Q: What's the minimum image overlap?**  
A: 25% is minimum; 30-35% recommended

**Q: Can I use different sized images?**  
A: Yes, they'll be automatically resized to same height

**Q: What's the output file size?**  
A: ~2.5-3.2 MB for 5 images (JPEG quality 95)

**Q: Can I stitch more than 5 images?**  
A: Yes, algorithm supports unlimited images

**Q: What if features don't match?**  
A: Check overlap (too small), image quality (too low), or increase features

**Q: Can I use color images?**  
A: Yes, algorithm works with any 3-channel image

**Q: Can I use grayscale images?**  
A: Convert to 3-channel first with `cv2.cvtColor(gray, cv2.COLOR_GRAY2BGR)`

**Q: What if panorama is tilted?**  
A: Verify translation-only RANSAC is active (it's default)

**Q: Can I modify the code?**  
A: Yes! It's well-commented and modular

**Q: Where's the API documentation?**  
A: See `Final_Code/FINAL_CODE_DOCUMENTATION.md`

---

## Support Resources

### Documentation Files
- **Complete Reference**: `Final_Code/FINAL_CODE_DOCUMENTATION.md`
- **Algorithm Details**: `Resources/RESOURCES_AND_REFERENCES.md`
- **Results Analysis**: `Results/Final_Output/FINAL_PANORAMA.md`

### Code Comments
All functions in `sequential_stitch3_FINAL.py` have:
- Purpose statement
- Algorithm explanation
- Parameter descriptions
- Return value documentation

### Example Usage
See `main()` function in code (lines 498-521)

---

## Next Steps

1. **Install** Python and dependencies
2. **Prepare** your images with 25-35% overlap
3. **Run** the script
4. **Review** the output panorama
5. **Experiment** with parameters
6. **Integrate** into your workflow

---

## Contact & Feedback

For issues or questions:
1. Check this guide first
2. Review code comments
3. Study the documentation
4. Examine error messages
5. Adjust parameters

---

**Status**: Production Ready  
**Version**: 3.5 (Final)  
**Last Updated**: December 4, 2025

Happy stitching!
