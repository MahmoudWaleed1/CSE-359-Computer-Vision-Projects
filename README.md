# CSE 359 - Computer Vision Projects

This repository contains coursework for **CSE 359: Computer Vision** (Alexandria University, Faculty of Engineering).
The projects cover feature extraction, geometric vision, image stylization, video augmentation, and image stitching.

**Instructor:** Dr. Marwan Torki

## Repository Structure

- `Assignment-1_cartoonification-and-lane-detection/`
- `Assignment-2_Augmented-Reality_and_Image-Mosaics/`

## Assignment 1: Cartoonification and Road Lane Detection

This assignment introduces foundational computer vision techniques through two tasks:

1. **Image Cartoonification** using filtering, edge detection, and stylization.
2. **Road Lane Detection** using preprocessing and lane extraction pipelines.

Notebooks:

- `Assignment-1_cartoonification-and-lane-detection/Cartoonification/cartoon.ipynb`
- `Assignment-1_cartoonification-and-lane-detection/RoadLane-Detection/Road_Lane-Detection.ipynb`

## Assignment 2: Augmented Reality and Image Mosaics

This assignment implements two projective geometry pipelines:

1. **Planar Augmented Reality** on a book cover in video.
2. **Panorama/Image Mosaic Stitching** from overlapping images.

Notebooks:

- `Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented Reality/ar.ipynb`
- `Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented Reality/ar_part2.ipynb`
- `Assignment-2_Augmented-Reality_and_Image-Mosaics/Image Mosaics/Image-Mosaics.ipynb`

Key methods used:

- SIFT keypoints and descriptors
- KNN feature matching with Lowe ratio test
- Homography estimation (manual DLT + OpenCV/RANSAC)
- Perspective warping and masking for AR overlay
- Forward/inverse warping strategies for mosaics

Main materials and outputs:

- Inputs: `cv_cover.jpg`, `book.mov`, `ar_source.mov`, `pano_image1.jpg`, `pano_image2.jpg`, `shanghai-21.png`, `shanghai-22.png`, `shanghai-23.png`
- Output video: `Assignment-2_Augmented-Reality_and_Image-Mosaics/Augmented Reality/output.avi`
- Full report: `Assignment-2_Augmented-Reality_and_Image-Mosaics/Assignment-2_Report.md`

## Status

- Assignment 1: Completed
- Assignment 2: Completed
- Assignment 3: Not added yet
- Assignment 4: Not added yet
