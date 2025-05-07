# Chicken Pose Estimation with YOLOv8

This project focuses on real-time pose estimation for chickens using the Ultralytics YOLOv8 Pose model. The workflow involved custom skeleton design, labeling using CVAT, training the YOLOv8n-pose model, and running live predictions.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Dataset Preparation](#dataset-preparation)
- [Model Training](#model-training)
- [Results](#results)
- [Live Predictions](#live-predictions)
- [Visuals](#visuals)

---

## Project Overview

- **Goal**: Detect keypoints (pose) on chickens using a custom skeleton.
- **Labeling Tool**: [CVAT](https://cvat.org/)
- **Model**: YOLOv8n-pose from [Ultralytics](https://github.com/ultralytics/ultralytics)
- **Device**: Apple Silicon (MPS backend)

---

## Dataset Preparation

1. **Custom Skeleton Design**  
   A chicken skeleton was created with 8 keypoints:
   - Head Top, Beak, Body Center, Tail, Right Foot (RF), Left Foot (LF), Right Claw (RC), Left Claw (LC).

   ![Skeleton Definition](images/Cvat_Skeleton.png)

   ### 🔍 Keypoint Analysis and Potential Inferences

   Each keypoint provides geometric and behavioral insight:

   - **Head Top ↔ Beak ↔ Body Center**: Forms the head-neck-torso alignment; useful for detecting feeding, drinking, and pecking behaviors.
   - **Body Center ↔ Tail**: Defines the body axis and balance; tail drop may indicate resting or discomfort.
   - **Left/Right Foot & Left/Right Claw**: Crucial for walking, standing, or balance assessment. Differences in limb angles can indicate limp or injury.

    ### 📌 Table 2: Description of Chicken Poses

   | Pose           | Description                                                                 |
   |----------------|-----------------------------------------------------------------------------|
   | Standing       | Body upright, both feet flat on the ground, neck extended, wings tucked.   |
   | Drinking       | Beak directed downward, often toward a water source, neck curved.          |
   | Pecking        | Quick downward head movement, head near ground.                            |
   | Walking        | One foot slightly lifted, limbs alternating in motion.                     |
   | Spreading Wings| Wings extended outward, body often upright and still.                      |
   | Resting        | Body low to ground, legs folded, head possibly tucked into feathers.       |
   | Preening       | Beak touching feathers, often near chest or wing area, curved neck.        |

2. **Labeling in CVAT**  
   286 frames from a chicken video were labeled.

   ![CVAT Labeling](images/Cvat_labelling.png)

3. **Dataset Export**  
   Exported using **Ultralytics YOLO Pose 1.0** format.

4. **config.yaml**:
   ```yaml
   path: /Users/barbarosyesilova/Documents/Chicken_Prj/data
   train: images/Train
   val:   images/Val

   nc:    1
   names:
     0: Chicken

   kpt_shape: [8, 3]
   ```

---

## Model Training

- **Command**:
  ```bash
  yolo pose train model=yolov8n-pose.pt data=config.yaml epochs=70 imgsz=640 device=mps name=train_chicken
  ```

- **Training Configuration**:
  - Epochs: 70
  - Image Size: 640x640
  - Device: MPS (Apple Silicon)

- **Training Loss & Metrics**:

  ![Training Results](images/results.png)

- **Sample Training Batches**:
  - Batch 0  
    ![Batch 0](images/train_batch0.jpeg)
  - Batch 1  
    ![Batch 1](images/train_batch1.jpeg)
  - Batch 2  
    ![Batch 2](images/train_batch2.jpeg)

---

## Results

- High precision and recall achieved.
- mAP50-95 performance shows consistent improvement.

- **Validation Batch Example**:  
  ![Validation Labels](images/val_batch0_labels.jpeg)

---

## Live Predictions

- **Command**:
  ```bash
  yolo task=detect mode=predict model=/opt/homebrew/runs/pose/train_chicken3/weights/best.pt source=0 show=true device=mps
  ```

- Real-time chicken pose prediction using laptop camera:

  ![Realtime Test](images/Realtime_Test_model.png)

---

## Visuals

- **More Training Batch Samples**:
  - Batch 1080  
    ![Batch 1080](images/train_batch1080.jpeg)
  - Batch 1081  
    ![Batch 1081](images/train_batch1081.jpeg)
  - Batch 1082  
    ![Batch 1082](images/train_batch1082.jpeg)

