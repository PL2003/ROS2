## [For training Yolo,Click Here!](https://www.ejtech.io/learn/train-yolo-models)

Here's a well-structured README based on your raw input data, covering the complete pipeline from environment setup and dataset preparation to YOLO training and ROS2 deployment:

---

# YOLO11 Object Detection: Training & ROS2 Deployment Guide

A comprehensive guide for training YOLO object detection models with Ultralytics and deploying them in ROS2 environments.

---

## Table of Contents
1. [Environment Setup](#1-environment-setup)
2. [Dataset Preparation](#2-dataset-preparation)
3. [Training Configuration](#3-training-configuration)
4. [Model Training](#4-model-training)
5. [Inference & Testing](#5-inference--testing)
6. [ROS2 Integration](#6-ros2-integration)
7. [Troubleshooting](#7-troubleshooting)

---

## 1. Environment Setup

### 1.1 Create Conda Environment
```bash
# Create new environment with Python 3.12
conda create --name yolo11-env python=3.12 -y

# Activate the environment
conda activate yolo11-env
```
> **Note:** When active, you'll see `(yolo11-env)` at the left of your command prompt.

### 1.2 Install Ultralytics
```bash
# Install Ultralytics (includes OpenCV, Numpy, PyTorch CPU)
pip install ultralytics
```

### 1.3 Install GPU-Enabled PyTorch (Recommended)
```bash
# Install PyTorch with CUDA 12.4 support
pip install --upgrade torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124
```
> **Note:** Check [PyTorch's official site](https://pytorch.org/get-started/locally/) for the latest CUDA version commands.

### 1.4 Verify GPU Installation
```bash
python -c "import torch; print(torch.cuda.get_device_name(0))"
```

---

## 2. Dataset Preparation

### 2.1 Dataset Size Guidelines
| Dataset Size | Recommended Images | Epochs |
|-------------|-------------------|--------|
| Small | 200+ total (50+ per class) | 60 |
| Medium | 500+ total | 50 |
| Large | 10,000+ total | 40 |

### 2.2 Image Collection Best Practices
- **Variety:** Capture objects from multiple angles, distances, and perspectives
- **Backgrounds:** Include diverse backgrounds and settings to reduce false positives
- **Lighting:** Capture images in different lighting conditions (bright, dim, indoor, outdoor)
- **Avoid Redundancy:** Remove near-identical images (common when extracting video frames)
- **Occlusion:** Include partially obscured objects when relevant to your application

### 2.3 Labeling Guidelines
1. **Include Full Object:** Ensure the entire object is inside the bounding box
2. **Slightly Too Big > Too Small:** Lean towards larger boxes rather than missing object parts
3. **Overlap is OK:** Bounding boxes may intersect
4. **Perspective Matters:** Ask yourself: *"Where would I want the model to predict this box in production?"*
5. **Use Zoom:** Utilize zoom tools for precise bounding boxes
6. **Handle Occlusion:** For partially visible objects, draw boxes around visible parts

### 2.4 Required Folder Structure

```
yolo/
├── data/
│   ├── train/
│   │   ├── images/          # Training images (.jpg, .png, .bmp)
│   │   └── labels/          # Training annotations (.txt)
│   └── validation/
│       ├── images/          # Validation images
│       └── labels/          # Validation annotations
├── data.yaml                # Training configuration
└── runs/                    # Training outputs (auto-generated)
```

### 2.5 Automated Dataset Split
Create the working directory:
```bash
mkdir %USERPROFILE%\Documents\yolo
cd %USERPROFILE%\Documents\yolo
mkdir data
```

Download and run the split script:
```bash
curl --output train_val_split.py https://raw.githubusercontent.com/EdjeElectronics/Train-and-Deploy-YOLO-Models/refs/heads/main/utils/train_val_split.py

python train_val_split.py --datapath="C:\\path\\to\\your\\dataset" --train_pct=.8
```

---

## 3. Training Configuration

### 3.1 Create `data.yaml`
Create a file named `data.yaml` in your `yolo` folder:

```yaml
path: C:\Users\<username>\Documents\yolo\data
train: train\images
val: validation\images

nc: 5  # Number of classes

names: ["class1", "class2", "class3", "class4", "class5"]
```

**Configuration Parameters:**
- `path`: Absolute path to your dataset root
- `nc`: Number of object classes
- `names`: List of class names (must match label map order)

### 3.2 Example: VisDrone Dataset Configuration
```yaml
path: VisDrone
train: images/train
val: images/val
test: images/test

names:
  0: pedestrian
  1: people
  2: bicycle
  3: car
  4: van
  5: truck
  6: tricycle
  7: awning-tricycle
  8: bus
  9: motor
```

---

## 4. Model Training

### 4.1 Model Selection

| Model | Size | mAP | Parameters | FLOPs | Use Case |
|-------|------|-----|------------|-------|----------|
| `yolo11n.pt` | Nano | Lower | 2.6M | 6.5B | Edge devices, speed critical |
| `yolo11s.pt` | Small | Medium | 9.4M | 21.5B | **Recommended starting point** |
| `yolo11m.pt` | Medium | Higher | 20.1M | 68.0B | Balanced performance |
| `yolo11l.pt` | Large | High | 46.5M | 147.7B | High accuracy |
| `yolo11x.pt` | XLarge | Highest | 96.1M | 341.4B | Maximum accuracy |

> **Recommendation:** Start with `yolo11s.pt` for most applications.

### 4.2 Training Command
```bash
yolo detect train data=data.yaml model=yolo11s.pt epochs=60 imgsz=640
```

**Parameters:**
- `data`: Path to configuration YAML file
- `model`: Model architecture to train
- `epochs`: Number of training epochs
- `imgsz`: Input resolution (default: 640x640)

### 4.3 Training Output
After training completes, model weights are saved to:
```
yolo/runs/detect/train/weights/
├── best.pt    # Best performing model (recommended)
└── last.pt    # Final epoch model
```

**Training Metrics:**
- Check `runs/detect/train/results.png` for loss/mAP progression
- If mAP50 < 0.60, review your dataset for labeling errors

---

## 5. Inference & Testing

### 5.1 Download Inference Script
```bash
curl -o yolo_detect.py https://raw.githubusercontent.com/EdjeElectronics/Train-and-Deploy-YOLO-Models/refs/heads/main/yolo_detect.py
```

### 5.2 Run Inference
```bash
# USB Camera
python yolo_detect.py --model=runs/detect/train/weights/best.pt --source=usb0

# Image File
python yolo_detect.py --model=best.pt --source=test_img.jpg

# Video File
python yolo_detect.py --model=best.pt --source=test_vid.mp4 resolution=1280x720

# Image Directory
python yolo_detect.py --model=best.pt --source=img_dir/
```

> Press `q` to stop the inference script.

---

## 6. ROS2 Integration

### 6.1 Dependencies Installation
```bash
# Install ROS Numpy for message conversion
pip install ros_numpy

# Install Ultralytics
pip install ultralytics
```

### 6.2 ROS2 Node: Image Detection & Segmentation

```python
import time
import rospy
from sensor_msgs.msg import Image
from ultralytics import YOLO
import ros_numpy

# Initialize models
detection_model = YOLO("yolo11m.pt")
segmentation_model = YOLO("yolo11m-seg.pt")

# Initialize ROS node
rospy.init_node("ultralytics")
time.sleep(1)

# Publishers
det_image_pub = rospy.Publisher("/ultralytics/detection/image", Image, queue_size=5)
seg_image_pub = rospy.Publisher("/ultralytics/segmentation/image", Image, queue_size=5)

def callback(data):
    """Process image and publish annotated results."""
    array = ros_numpy.numpify(data)
    
    if det_image_pub.get_num_connections():
        det_result = detection_model(array)
        det_annotated = det_result[0].plot(show=False)
        det_image_pub.publish(ros_numpy.msgify(Image, det_annotated, encoding="rgb8"))
    
    if seg_image_pub.get_num_connections():
        seg_result = segmentation_model(array)
        seg_annotated = seg_result[0].plot(show=False)
        seg_image_pub.publish(ros_numpy.msgify(Image, seg_annotated, encoding="rgb8"))

# Subscribe to camera topic
rospy.Subscriber("/camera/color/image_raw", Image, callback)

while True:
    rospy.spin()
```

### 6.3 ROS2 Node: Publish Detected Classes (String)

```python
import time
import ros_numpy
import rospy
from sensor_msgs.msg import Image
from std_msgs.msg import String
from ultralytics import YOLO

# Initialize model
detection_model = YOLO("yolo11m.pt")
rospy.init_node("ultralytics")
time.sleep(1)

# Publisher for detected classes
classes_pub = rospy.Publisher("/ultralytics/detection/classes", String, queue_size=5)

def callback(data):
    """Process image and publish detected class names."""
    array = ros_numpy.numpify(data)
    if classes_pub.get_num_connections():
        det_result = detection_model(array)
        classes = det_result[0].boxes.cls.cpu().numpy().astype(int)
        names = [det_result[0].names[i] for i in classes]
        classes_pub.publish(String(data=str(names)))

rospy.Subscriber("/camera/color/image_raw", Image, callback)

while True:
    rospy.spin()
```

### 6.4 ROS2 Node: Depth Image Processing

```python
import time
import numpy as np
import ros_numpy
import rospy
from sensor_msgs.msg import Image
from std_msgs.msg import String
from ultralytics import YOLO

rospy.init_node("ultralytics")
time.sleep(1)

segmentation_model = YOLO("yolo11m-seg.pt")
classes_pub = rospy.Publisher("/ultralytics/detection/distance", String, queue_size=5)

def callback(data):
    """Process RGB and depth images to calculate object distances."""
    # Get RGB image
    image = rospy.wait_for_message("/camera/color/image_raw", Image)
    image = ros_numpy.numpify(image)
    depth = ros_numpy.numpify(data)
    
    # Run segmentation
    result = segmentation_model(image)
    
    all_objects = []
    for index, cls in enumerate(result[0].boxes.cls):
        class_index = int(cls.cpu().numpy())
        name = result[0].names[class_index]
        mask = result[0].masks.data.cpu().numpy()[index, :, :].astype(int)
        
        # Calculate average distance from depth image
        obj = depth[mask == 1]
        obj = obj[~np.isnan(obj)]
        avg_distance = np.mean(obj) if len(obj) else np.inf
        
        all_objects.append(f"{name}: {avg_distance:.2f}m")
    
    classes_pub.publish(String(data=str(all_objects)))

rospy.Subscriber("/camera/depth/image_raw", Image, callback)

while True:
    rospy.spin()
```

### 6.5 Debugging Tools

| Command | Description |
|---------|-------------|
| `rostopic echo <TOPIC>` | View messages on a topic |
| `rostopic list` | List all active topics |
| `rqt_graph` | Visualize node communication graph |
| `rviz` | 3D visualization of sensor data and robot state |

---

## 7. Troubleshooting

| Issue | Solution |
|-------|----------|
| Low mAP (< 0.60) | Review dataset labels for errors/conflicts |
| CUDA out of memory | Reduce batch size or use smaller model |
| Model not detecting objects | Add more training images with variety |
| Near-identical images | Remove redundant frames from video extraction |
| PyTorch not using GPU | Verify CUDA installation: `torch.cuda.is_available()` |

---

## Next Steps

- **Extend Functionality:** Add object counting, logging, or event triggering
- **Edge Deployment:** Deploy on Raspberry Pi or Jetson devices
- **Model Improvement:** Add more diverse training images and review labels
- **Advanced ROS:** Integrate with navigation, manipulation, or mapping stacks

---

**Resources:**
- [Ultralytics Documentation](https://docs.ultralytics.com)
- [Train and Deploy YOLO Models Repository](https://github.com/EdjeElectronics/Train-and-Deploy-YOLO-Models)
- [VisDrone Dataset](https://github.com/VisDrone/VisDrone-Dataset)

---

*Happy Training! 🚀*
