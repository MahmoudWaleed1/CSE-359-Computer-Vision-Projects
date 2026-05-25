# CSE 359 - Computer Vision Projects

This repository contains coursework for **CSE 359: Computer Vision** (Alexandria University, Faculty of Engineering).
The projects cover feature extraction, geometric vision, image stylization, video augmentation, and image stitching.

**Instructor:** Dr. Marwan Torki

## Repository Structure

- `Assignment-1_cartoonification-and-lane-detection/`
- `Assignment-2_Augmented-Reality_and_Image-Mosaics/`
- `Assignment-3_Stereo-Vision/`
- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/`

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


## Assignment 3: Stereo Vision

This assignment focuses on depth estimation from stereo images through block matching and disparity computation.

Highlights (from the code):

- Block matching with configurable window size and maximum disparity search.
- Cost functions implemented: SAD and SSD.
- Disparity maps computed per pixel and visualized with grayscale colormap and colorbar.
- Dynamic-programming-based row matching in Part 2 to build a disparity map per scanline.

Notebooks:

- `Assignment-3_Stereo-Vision/Part-1/Block-Matching.ipynb`
- `Assignment-3_Stereo-Vision/Part-2/stereo_db.ipynb`

Report:

- `Assignment-3_Stereo-Vision/Assignment-3_Report.md`

## Assignment 4: Pet Expression Classification Using Deep CNN Architectures and Transfer Learning

This assignment explores deep CNN baselines and transfer learning for pet expression classification.

Highlights (from the code):

- Baseline CNNs trained and evaluated: VGG, ResNet, MobileNet, InceptionV3, and DenseNet121.
- Data loading via `ImageFolder`, with train/val/test splits and per-model transforms.
- InceptionV3 uses 299x299 preprocessing; DenseNet121 uses 224x224 with resize and center crop.
- Transfer learning with ImageNet-pretrained DenseNet121; classifier head fine-tuned.
- Training setup includes CrossEntropyLoss with class weights, Adam optimizer, and cosine annealing LR scheduler.
- Evaluation includes accuracy, classification report, and confusion matrix visualization.

Notebooks:

- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/CNN_BaselineModels_VGG_ResNet_MobileNet/BaselineModels_VGG_ResNet_MobileNet.ipynb`
- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/CNN_BaselineModels_InceptionV3_DenseNet121/BaselineModels_InceptionV3_DenseNet121.ipynb`
- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/TransferLearning_ImageNet_FineTuning/transferLearning.ipynb`

Trained weights:

- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/CNN_BaselineModels_VGG_ResNet_MobileNet/best_ResNet18.pth`
- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/CNN_BaselineModels_VGG_ResNet_MobileNet/best_MobileNet.pth`
- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/CNN_BaselineModels_InceptionV3_DenseNet121/best_InceptionV3.pth`
- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/CNN_BaselineModels_InceptionV3_DenseNet121/best_DenseNet121.pth`
- `Assignment-4_PetExpressionClassificationUsingDeepCNNArchitecturesAndTransferLearning/TransferLearning_ImageNet_FineTuning/best_model.pth`
