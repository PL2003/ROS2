To implement a **Hybrid Architecture** on your Raspberry Pi 4, we will combine high-speed **OpenCV Lane Following** with intermittent **YOLOv8 Safety Checks**. 

Since you are using **ROS 2 Humble** (the modern standard for 2026), we will avoid the older `rospy` (ROS 1) syntax from your provided text and use `rclpy` (ROS 2) to ensure it works on your Pi 4.

---

## 🛠 Phase 1: Environment Setup
You need to install the bridge between ROS 2 and OpenCV, plus the Ultralytics engine.

```bash
# 1. Update and install ROS 2 CV Bridge
sudo apt update
sudo apt install ros-humble-cv-bridge ros-humble-vision-opencv -y

# 2. Install Ultralytics (YOLOv8) and dependencies
pip install ultralytics ros-numpy
```
> **🔍 Syntax Breakdown:**
> * `ros-humble-cv-bridge`: This is a "translator" that converts ROS image messages into OpenCV-compatible arrays.
> * `ultralytics`: The official engine for YOLOv8.

---

## 🧠 Phase 2: The Hybrid Brain (Python Code)
This script performs the "Pro Way" logic: 
1. **Always** calculates lane error (fast).
2. **Sometimes** (every 10 frames) checks for people/obstacles (YOLO).



Create a file named `hybrid_driver.py` in your workspace:

```python
import rclpy
from rclpy.node import Node
from sensor_msgs.msg import Image
from geometry_msgs.msg import Twist
from cv_bridge import CvBridge
import cv2 as cv
import numpy as np
from ultralytics import YOLO

class HybridBot(Node):
    def __init__(self):
        super().__init__('hybrid_bot')
        # Subscribes to the camera feed
        self.sub = self.create_subscription(Image, '/image_raw', self.process_callback, 10)
        # Publishes movement commands
        self.pub = self.create_publisher(Twist, '/cmd_vel', 10)
        
        self.br = CvBridge()
        self.yolo = YOLO('yolov8n.pt') # Use 'nano' for Pi 4 performance
        self.frame_count = 0
        self.obstacle_detected = False

    def process_callback(self, msg):
        self.frame_count += 1
        # Convert ROS image to OpenCV BGR
        frame = self.br.imgmsg_to_cv2(msg, desired_encoding='bgr8')
        
        # --- LOOP 1: YOLO Safety Check (Every 10 frames to save CPU) ---
        if self.frame_count % 10 == 0:
            results = self.yolo(frame, verbose=False, conf=0.5)
            self.obstacle_detected = any(box.cls == 0 for box in results[0].boxes) # Class 0 is 'person'

        # --- LOOP 2: OpenCV Lane Following (Every frame for smoothness) ---
        twist = Twist()
        
        if self.obstacle_detected:
            twist.linear.x = 0.0  # STOP
            twist.angular.z = 0.0
            self.get_logger().warn("OBSTACLE DETECTED: Emergency Stop")
        else:
            # Simple Thresholding Logic
            gray = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)
            _, thresh = cv.threshold(gray[120:, :], 150, 255, cv.THRESH_BINARY)
            M = cv.moments(thresh)
            
            if M['m00'] > 0:
                cx = int(M['m10'] / M['m00'])
                error = cx - (frame.shape[1] / 2)
                
                twist.linear.x = 0.2 # Forward speed
                twist.angular.z = -float(error) / 100 # Steering correction
        
        self.pub.publish(twist)

def main():
    rclpy.init()
    node = HybridBot()
    rclpy.spin(node)
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

---

## 📁 Phase 3: Deployment to the Pi

### 1. Create the ROS 2 Package
Follow the standard structure for your `my_cv_package`.

```bash
# Navigate to your workspace
cd ~/dev_ws/src
ros2 pkg create --build-type ament_python delivery_vision --dependencies rclpy sensor_msgs geometry_msgs cv_bridge
```

### 2. Move your code
Copy the `hybrid_driver.py` code into `~/dev_ws/src/delivery_vision/delivery_vision/hybrid_driver.py`.

### 3. Build and Source
```bash
cd ~/dev_ws
colcon build --packages-select delivery_vision
source install/setup.bash
```
> **🔍 Syntax Breakdown:**
> * `colcon build`: The ROS 2 build tool. `--packages-select` saves time by only building your vision code.
> * `source install/setup.bash`: This "registers" your new package so the terminal can find the `hybrid_bot` node.

---

## 🛠 Phase 4: Debugging & Validation
Use the tools mentioned in your prompt to ensure the data is flowing.

1. **Check Camera Stream:**
   `ros2 topic list` (Ensure `/image_raw` is present).
2. **Monitor Velocity Output:**
   `ros2 topic echo /cmd_vel`
   *If you put your hand in front of the camera, the `linear.x` should drop to `0.0` immediately.*
3. **Visualize:**
   Run `ros2 run rqt_graph rqt_graph` on your laptop to see the "Hybrid Brain" connected to the camera and motors.



---

## 📈 Pro-Tip: Hardware Acceleration
YOLOv8n on a Pi 4 CPU will run at roughly **1-2 FPS**. To make the "Safety Loop" faster:
* **Export to NCNN:** Use `model.export(format='ncnn')`. This optimizes the math for the Pi's specific processor.
* **Reduce Input Size:** Change the YOLO call to `results = self.yolo(frame, imgsz=320)`.

**Would you like me to provide the ESP32 code to handle the `/cmd_vel` signals and drive your motors?**
