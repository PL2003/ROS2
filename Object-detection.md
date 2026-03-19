# 📘 ROS 2 Object Detection (YOLO-based) — Exam Revision Note

---

# 🧠 1. Definitions (Core Terms)

* **ROS 2 (Robot Operating System)**
  Middleware framework for building robotic systems using nodes, topics, services, and actions.

* **Object Detection**
  Process of identifying objects in an image and locating them using bounding boxes.

* **YOLO (You Only Look Once)**
  A **single-shot detection algorithm** that predicts:

  * Object class
  * Bounding box
  * Confidence score
    in one forward pass.

* **Node**
  Independent executable performing a task (e.g., camera node, detection node).

* **Topic**
  Communication channel where nodes publish/subscribe messages (e.g., `/camera/raw_image`).

* **Launch File**
  Script to start multiple nodes with configurations simultaneously.

* **rqt_image_view**
  GUI tool to visualize image topics.

---

# ⚙️ 2. System Architecture (Big Picture)

```
[Camera Node] → [Image Topic] → [YOLO Detection Node] → [Processed Image Topic]
                                          ↓
                                 [Detection Output]
                                          ↓
                                  [Visualization Tool]
```

---

# 🔄 3. Step-by-Step Process (Workflow)

## 🪜 Step 1: Setup Environment

```bash
source /opt/ros/humble/setup.bash
source ~/ros2_ws/install/setup.bash
```

### 🔍 Explanation

* `source` → loads environment variables
* First line → ROS 2 base system (underlay)
* Second line → your workspace (overlay)

---

## 🪜 Step 2: Launch Camera Node

```bash
ros2 run <camera_package> camera_node
```

### 📌 Function

* Captures webcam frames
* Publishes images to topic:

```
/camera/raw_image
```

---

## 🪜 Step 3: Launch Detection System

```bash
ros2 launch <package_name> cross.launch.py
```

### 🔍 Syntax Breakdown

| Part              | Meaning         |
| ----------------- | --------------- |
| `ros2 launch`     | Run launch file |
| `<package_name>`  | ROS package     |
| `cross.launch.py` | Launch script   |

### 📌 What Happens Internally

* Starts YOLO detection node
* Subscribes to `/camera/raw_image`
* Processes frames
* Publishes annotated images

---

## 🪜 Step 4: YOLO Detection Processing

### 🔄 Internal Pipeline

1. Receive image
2. Preprocess (resize, normalize)
3. Run YOLO model
4. Output:

   * Bounding boxes
   * Class labels
   * Confidence score
5. Draw overlay on image

---

## 🪜 Step 5: Visualization

```bash
ros2 run rqt_image_view rqt_image_view
```

### 📌 Select Topic:

```
/detection/image
```

### Output:

* Live video
* Bounding boxes
* Object labels

---

## 🪜 Step 6: Terminal Output

Example:

```
Object: Yellow Ball | Confidence: 92%
```

---

# 🔢 4. Important Concepts (How It Works Internally)

## 📦 YOLO Detection Logic

* Divides image into grid
* Each grid predicts:

  * Bounding box `(x, y, w, h)`
  * Confidence
  * Class probability

---

## 📊 Key Parameters

| Parameter    | Meaning                         |
| ------------ | ------------------------------- |
| `theta`      | Not used here (used in actions) |
| `confidence` | Probability of detection        |
| `bbox`       | Bounding box coordinates        |
| `class_id`   | Object category                 |

---

# 📐 5. Important Formulas

### 🟡 Bounding Box Representation

```
(x, y, w, h)
```

| Symbol | Meaning         |
| ------ | --------------- |
| x, y   | Center position |
| w      | Width           |
| h      | Height          |

---

### 🟡 Confidence Score

```
Confidence = P(Object) × IoU
```

| Term      | Meaning                   |
| --------- | ------------------------- |
| P(Object) | Probability object exists |
| IoU       | Intersection over Union   |

---

# ⚖️ 6. Comparison Table

## 🔍 YOLO vs Traditional Detection

| Feature    | YOLO (Single-shot)  | Two-shot (R-CNN) |
| ---------- | ------------------- | ---------------- |
| Speed      | Very Fast ⚡         | Slow             |
| Accuracy   | Moderate-High       | Very High        |
| Real-time  | Yes ✅               | No ❌             |
| Complexity | Low                 | High             |
| Use Case   | Robotics, real-time | Research         |

---

## 🔍 ROS Components Involved

| Component      | Role            |
| -------------- | --------------- |
| Node           | Processing unit |
| Topic          | Data transfer   |
| Launch File    | System startup  |
| rqt_image_view | Visualization   |

---

# 🧩 7. Code Snippet (Basic Subscriber Node - Python)

```python
import rclpy
from rclpy.node import Node
from sensor_msgs.msg import Image

class ImageSubscriber(Node):
    def __init__(self):
        super().__init__('image_subscriber')

        self.subscription = self.create_subscription(
            Image,
            '/camera/raw_image',
            self.listener_callback,
            10
        )

    def listener_callback(self, msg):
        self.get_logger().info('Receiving image frame')
```

---

## 🔍 Syntax Explanation

| Code                    | Meaning                      |
| ----------------------- | ---------------------------- |
| `Node`                  | Base class for ROS node      |
| `create_subscription()` | Subscribe to topic           |
| `Image`                 | Message type                 |
| `listener_callback`     | Function executed on message |
| `10`                    | Queue size                   |

---

# 🚀 8. Launch File Example

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='yolo_pkg',
            executable='detector_node',
            output='screen'
        )
    ])
```

---

## 🔍 Explanation

* `LaunchDescription()` → defines launch config
* `Node()` → starts a node
* `package` → where node exists
* `executable` → node name

---

# 🧠 9. Key Principles

### ⚡ Real-Time Processing

* YOLO processes image in one pass → fast

### 🔗 Modularity

* ROS nodes are independent → scalable

### 📡 Publisher-Subscriber Model

* Camera → publishes
* Detector → subscribes

### 🔄 Overlay System

* Custom detection overrides default behavior

---

# 🏭 10. Real-Life Application (Memory Hook)

## 🏗️ Factory Conveyor System

* Camera monitors conveyor
* YOLO detects defective/yellow objects
* Robot arm:

  * Picks object
  * Removes from line

👉 Flow:

```
Camera → Detection → Decision → Robot Action
```

---

# 🧾 11. CLI Tools (Final Consolidation)

| Tool              | Function           | Example                          | Pros         | Cons             |
| ----------------- | ------------------ | -------------------------------- | ------------ | ---------------- |
| `ros2 run`        | Run node           | `ros2 run pkg node`              | Simple       | Single node only |
| `ros2 launch`     | Run multiple nodes | `ros2 launch pkg file.launch.py` | Scalable     | Setup needed     |
| `ros2 topic echo` | View data          | `/camera/raw_image`              | Debugging    | Raw output       |
| `ros2 topic list` | Show topics        | —                                | Easy inspect | No data          |
| `ros2 bag`        | Record/replay      | `ros2 bag record`                | Testing      | Storage heavy    |
| `rqt_image_view`  | Visualize images   | —                                | GUI          | Limited analysis |

---

# 📌 12. Summary (Quick Revision)

* YOLO = **fast single-shot object detection**
* ROS 2 uses:

  * **Nodes + Topics + Launch files**
* Workflow:

  1. Camera publishes image
  2. YOLO node processes
  3. Output published
  4. Visualized via GUI
* Key outputs:

  * Bounding box
  * Label
  * Confidence
* Launch files simplify multi-node execution
* Real-world use: **robotics automation, surveillance, simulation**

---

# 🧩 Memory Trick

👉 **“C-D-V” Flow**

* **C**amera → capture
* **D**etect → YOLO
* **V**isualize → rqt

---

If needed, a next-level note can include:

* YOLO training pipeline
* OpenCV integration
* ROS2 + GPU acceleration
* Deployment on Jetson / Raspberry Pi
