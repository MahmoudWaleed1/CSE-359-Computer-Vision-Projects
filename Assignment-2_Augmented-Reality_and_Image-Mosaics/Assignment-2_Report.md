# Assignment 2 Report

## Augmented Reality and Image Mosaics

## README Section

### 1. Purpose

This assignment implements two core computer vision pipelines:

1. Augmented Reality (AR) on a planar surface (a book cover in video).
2. Image mosaics (panorama stitching) from overlapping images.

The notebooks show both conceptual and practical implementations of feature matching, homography estimation, geometric warping, and image composition.

### 2. What The Code Does

The project is organized around three notebooks:

1. [ar.ipynb](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/ar.ipynb):
   Loads the cover image from [cv_cover.jpg](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/assignment_2_materials/cv_cover.jpg) and a target video frame from [book.mov](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/assignment_2_materials/book.mov). Detects SIFT keypoints and descriptors. Matches features with brute-force KNN matching and ratio test. Computes homography manually using SVD and compares to OpenCV. Projects points and book corners into the video frame.

2. [ar_part2.ipynb](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/ar_part2.ipynb):
   Completes the AR pipeline on full video. Crops AR source frames from [ar_source.mov](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/assignment_2_materials/ar_source.mov) to the cover aspect ratio. Computes robust homographies frame-by-frame using RANSAC. Warps AR frames to the book plane and overlays them. Writes final augmented output to [output.avi](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/output.avi).

3. [Image-Mosaics.ipynb](Assignment-2_Augmented-Reality_and_Image-Mosaics/Image%20Mosaics/Image-Mosaics.ipynb):
   Detects and matches image features. Computes homography manually and with OpenCV. Implements forward warping, weighted splatting, and inverse warping. Builds a two-image mosaic and a three-image robust pairwise mosaic.

### 3. Dependencies

Install Python packages:

```bash
pip install opencv-python numpy matplotlib pandas
```

Notes:

1. SIFT requires a modern OpenCV build (opencv-python >= 4.x).
2. The notebooks use matplotlib for visualization and OpenCV for geometric operations.

### 4. Input Data Requirements

Augmented Reality uses:

1. [cv_cover.jpg](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/assignment_2_materials/cv_cover.jpg)
2. [book.mov](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/assignment_2_materials/book.mov)
3. [ar_source.mov](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/assignment_2_materials/ar_source.mov)

Image Mosaics uses:

1. [pano_image1.jpg](Assignment-2_Augmented-Reality_and_Image-Mosaics/Image%20Mosaics/pano_image1.jpg)
2. [pano_image2.jpg](Assignment-2_Augmented-Reality_and_Image-Mosaics/Image%20Mosaics/pano_image2.jpg)
3. [shanghai-21.png](Assignment-2_Augmented-Reality_and_Image-Mosaics/Image%20Mosaics/shanghai-21.png)
4. [shanghai-22.png](Assignment-2_Augmented-Reality_and_Image-Mosaics/Image%20Mosaics/shanghai-22.png)
5. [shanghai-23.png](Assignment-2_Augmented-Reality_and_Image-Mosaics/Image%20Mosaics/shanghai-23.png)

### 5. How To Run

1. Launch Jupyter:

```bash
jupyter notebook
```

2. Open and run notebooks in order:
   [ar.ipynb](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/ar.ipynb), [ar_part2.ipynb](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/ar_part2.ipynb), [Image-Mosaics.ipynb](Assignment-2_Augmented-Reality_and_Image-Mosaics/Image%20Mosaics/Image-Mosaics.ipynb).

3. Run cells sequentially from top to bottom.

4. Expected output artifacts:
   [output.avi](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/output.avi) from AR pipeline, and plotted mosaics plus intermediate visual diagnostics in notebook outputs.

### 6. Practical Usage Notes

1. For AR, reliable overlay depends on enough inlier matches in each frame.
2. For mosaics, better overlap and texture-rich images improve homography quality.
3. The code includes both educational manual implementations and OpenCV reference functions for validation.

---

## Detailed Technical Report

### 1. Augmented Reality Pipeline

#### 1.1 Objective

Detect a planar object (book cover) in each video frame and replace that planar region with a warped AR source frame.

#### 1.2 Feature Detection And Description

The pipeline uses SIFT:

1. Convert images to grayscale.
2. Detect keypoints.
3. Compute descriptors.

This creates scale- and rotation-robust local features suitable for matching between cover image and video frames.

#### 1.3 Feature Matching

Brute-force descriptor matching with KNN (k=2) is applied, then filtered with Lowe ratio test:

$$
\text{accept } m \text{ if } d(m_1) < 0.75 \cdot d(m_2)
$$

where $m_1$ and $m_2$ are the nearest and second-nearest matches.

This step rejects many ambiguous matches and improves homography stability.

#### 1.4 Homography Estimation (Manual DLT)

For a point pair $(x, y) \leftrightarrow (x', y')$, the project builds two linear equations. Stacking all correspondences yields:

$$
A h = 0
$$

Then SVD is used:

$$
A = U \Sigma V^T
$$

The homography vector is the last row of $V^T$ reshaped to $3 \times 3$, normalized so $H_{33}=1$.

Core idea used in code:

```python
_, _, V = np.linalg.svd(A)
H = np.reshape(V[-1], (3, 3))
H = H / H[2, 2]
```

The notebook also checks consistency against cv2.findHomography.


#### 1. AR Frame Preparation (Aspect-Ratio Aware Crop)

Before warping, each AR frame is center-cropped to the same aspect ratio as the cover image, then resized to exact cover dimensions.

Reason:

1. Prevent shape distortion when mapping AR content onto the planar target.
2. Preserve visual content alignment and reduce stretching artifacts.

#### 1. Warping And Overlay

1. Cover corners are projected into the current frame with perspectiveTransform.
2. AR frame is warped by warpPerspective using the computed homography.
3. A convex polygon mask of projected corners is filled.
4. Background and foreground are combined via bitwise operations.

This creates a clean replacement of the book region with warped AR content.

#### 1.7 End-To-End Video Procedure

For each frame in [book.mov](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/assignment_2_materials/book.mov):

1. Read frame.
2. Read AR frame (loop AR source if needed).
3. Compute SIFT features and robust homography.
4. If homography is valid and inliers are enough:
   crop/resize AR frame, warp to book plane, then overlay on frame.
5. Otherwise keep original frame.
6. Write output frame to [output.avi](Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented%20Reality/output.avi).

This results in temporally consistent augmentation with graceful fallback when tracking quality drops.

---

### 2. Image Mosaic Pipeline

#### 2.1 Objective

Build a larger panoramic canvas by geometrically aligning overlapping images via homography.

#### 2.2 Keypoint Detection And Matching

1. Convert input images to grayscale.
2. Detect SIFT keypoints and descriptors.
3. Match descriptors using BFMatcher KNN.
4. Apply ratio test and keep best matches.

Good matches are the foundation for robust geometric alignment.

#### 2.3 Homography Computation

The notebook implements manual DLT and compares it with cv2.findHomography(RANSAC).

Using both versions helps verify correctness of custom implementation and observe practical robustness differences.

#### 2.4 Homography Verification

Matched points from image 1 are transformed by $H$ and plotted against image 2 to visually check correspondence quality.

This diagnostic step is important for debugging failed mosaics before blending.

#### 2.5 Warping Strategies Implemented

1. Naive forward warp:
   Maps each source pixel to destination. Fast to understand but can produce holes due to discretization.

2. Forward warp with split/splat:
   Distributes each source pixel to 4 neighboring destination pixels using bilinear-style weights. Reduces holes and aliasing compared with naive forward mapping.

3. Inverse warp with bilinear sampling:
   For each destination pixel, map back to source using $H^{-1}$. Sample source intensities with bilinear interpolation. Produces denser, smoother warps and is generally preferred for final composition.

#### 2.6 Canvas Construction And Translation

To avoid clipping after warping:

1. Project corners of warped image.
2. Combine with base image corners.
3. Compute min/max bounds.
4. Build translation matrix to shift everything into positive coordinates.

The transformed homography is:

$$
H_{final} = T H
$$

where $T$ is a 2D translation in homogeneous coordinates.

#### 2.7 Blending/Composition Policy

The implemented composition keeps base image pixels in overlap regions and fills uncovered regions with warped image pixels.

This is simple and stable, though not seam-optimized.

#### 2.8 Three-Image Mosaic Procedure

The robust pairwise method is:

1. Build mosaic from image 2 and image 3.
2. Extract features on the intermediate mosaic.
3. Match intermediate mosaic with image 1.
4. Estimate second homography.
5. Compose final mosaic.

This incremental strategy handles multiple images while reusing the same two-image pipeline logic.

---

## Conclusion

This assignment demonstrates the full workflow of planar projective geometry in computer vision:

1. local feature extraction,
2. descriptor matching,
3. robust homography estimation,
4. geometric warping,
5. image/video composition.

The notebooks combine educational manual implementations with robust OpenCV methods, making the work suitable for both conceptual understanding and practical deployment.
