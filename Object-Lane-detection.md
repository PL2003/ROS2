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
#!/usr/bin/env python3
"""
hybrid_bot.py  —  ROS 2 Lane-Following + Obstacle-Avoidance Node
================================================================
Topics consumed
  /image_raw          sensor_msgs/Image   (camera feed)

Topics published
  /cmd_vel            geometry_msgs/Twist (velocity commands)
  /lane_debug/raw     sensor_msgs/Image   (HUD overlay)
  /lane_debug/bird    sensor_msgs/Image   (bird's-eye debug view)

Parameters (set via ros2 param set or a YAML launch file)
  focal_length_px     float  default 600.0
  stop_distance_m     float  default 2.0
  linear_speed        float  default 0.20
  steer_speed         float  default 0.40
  yolo_every_n        int    default 10
  poly_alpha          float  default 0.80
  n_windows           int    default 9
  sw_margin           int    default 80
  sw_min_pix          int    default 50

Quick-start
  ros2 run <your_pkg> hybrid_bot
  ros2 topic echo /cmd_vel
  ros2 run rqt_image_view rqt_image_view   # subscribe to /lane_debug/raw
"""

import rclpy
from rclpy.node   import Node
from rclpy.qos    import QoSProfile, ReliabilityPolicy, HistoryPolicy

from sensor_msgs.msg    import Image
from geometry_msgs.msg  import Twist
from cv_bridge          import CvBridge

import cv2
import numpy as np
from ultralytics import YOLO


# ══════════════════════════════════════════════════════════════════
#  CLASS MAP  —  YOLO class-id → (display label, BGR colour, real height m)
# ══════════════════════════════════════════════════════════════════
OBSTACLE_CLASSES: dict[int, tuple[str, tuple, float]] = {
    0:  ("Person",        (0,   0,   255), 1.70),
    2:  ("Car",           (255, 128,   0), 1.50),
    5:  ("Bus",           (255,  80,   0), 3.00),
    7:  ("Truck",         (200,  60,   0), 2.50),
    9:  ("Traffic Light", (0,   255, 255), 0.50),
    11: ("Stop Sign",     (0,    50, 255), 0.75),
}

# Directional icons shown in the HUD
_DIR_ICONS: dict[str, str] = {
    "STOP":        "■",
    "GO STRAIGHT": "▲",
    "STEER LEFT":  "◄",
    "STEER RIGHT": "►",
    "GO RIGHT":    "►",
    "GO LEFT":     "◄",
    "SEARCHING":   "?",
}


# ══════════════════════════════════════════════════════════════════
#  ROS 2 NODE
# ══════════════════════════════════════════════════════════════════
class HybridBot(Node):

    def __init__(self):
        super().__init__("hybrid_bot")

        # ── Declare & read parameters ─────────────────────────────
        self.declare_parameter("focal_length_px", 600.0)
        self.declare_parameter("stop_distance_m", 2.0)
        self.declare_parameter("linear_speed",    0.20)
        self.declare_parameter("steer_speed",     0.40)
        self.declare_parameter("yolo_every_n",    10)
        self.declare_parameter("poly_alpha",      0.80)
        self.declare_parameter("n_windows",       9)
        self.declare_parameter("sw_margin",       80)
        self.declare_parameter("sw_min_pix",      50)

        self._focal   = self.get_parameter("focal_length_px").value
        self._stop_d  = self.get_parameter("stop_distance_m").value
        self._lin_spd = self.get_parameter("linear_speed").value
        self._str_spd = self.get_parameter("steer_speed").value
        self._yolo_n  = self.get_parameter("yolo_every_n").value
        self._alpha   = self.get_parameter("poly_alpha").value
        self._nwin    = self.get_parameter("n_windows").value
        self._margin  = self.get_parameter("sw_margin").value
        self._minpix  = self.get_parameter("sw_min_pix").value

        # ── CV utilities ──────────────────────────────────────────
        self._bridge = CvBridge()
        self._yolo   = YOLO("yolov8n.pt")

        # ── Internal state ────────────────────────────────────────
        self._frame_count      = 0
        self._prev_left_fit    = None
        self._prev_right_fit   = None

        self._obstacle_detected  = False
        self._obstacle_direction = None          # 'left' | 'center' | 'right'
        self._obstacle_distance  = float("inf")

        self._M_warp   = None   # built on first frame
        self._M_unwarp = None
        self._frame_cx = None   # image horizontal centre

        # ── QoS — use sensor-data best-effort for the image stream ─
        sensor_qos = QoSProfile(
            reliability=ReliabilityPolicy.BEST_EFFORT,
            history=HistoryPolicy.KEEP_LAST,
            depth=1,
        )

        # ── Subscribers ───────────────────────────────────────────
        self._img_sub = self.create_subscription(
            Image, "/image_raw",
            self._image_callback,
            qos_profile=sensor_qos,
        )

        # ── Publishers ────────────────────────────────────────────
        self._cmd_pub       = self.create_publisher(Twist, "/cmd_vel", 10)
        self._hud_pub       = self.create_publisher(Image, "/lane_debug/raw",  1)
        self._bird_pub      = self.create_publisher(Image, "/lane_debug/bird", 1)

        self.get_logger().info("HybridBot initialised — waiting for /image_raw …")

    # ══════════════════════════════════════════════════════════════
    #  IMAGE CALLBACK  (called every frame)
    # ══════════════════════════════════════════════════════════════
    def _image_callback(self, msg: Image):
        frame = self._bridge.imgmsg_to_cv2(msg, desired_encoding="bgr8")
        self._frame_count += 1

        # ── One-time setup on the first frame ────────────────────
        if self._M_warp is None:
            self._M_warp, self._M_unwarp = self._build_perspective(frame)
            self._frame_cx = frame.shape[1] // 2
            self.get_logger().info(
                f"Perspective matrices built for "
                f"{frame.shape[1]}×{frame.shape[0]} px"
            )

        # ── YOLO (every N frames to keep CPU affordable) ─────────
        if self._frame_count % self._yolo_n == 0:
            frame = self._run_yolo(frame)

        # ── Lane detection → bird's-eye → polynomial fit ─────────
        output, bird_viz, lane_cx = self._process_frame(frame)

        # ── Control decision ──────────────────────────────────────
        action, color = self._decide_action(lane_cx)
        linear_x, angular_z = self._compute_velocity(action, lane_cx)

        # ── HUD overlay ───────────────────────────────────────────
        output = self._draw_hud(output, action, color,
                                lane_cx, linear_x, angular_z)

        # ── Publish /cmd_vel ──────────────────────────────────────
        twist           = Twist()
        twist.linear.x  = float(linear_x)
        twist.angular.z = float(angular_z)
        self._cmd_pub.publish(twist)

        # ── Publish debug images (only if subscribers exist) ──────
        if self._hud_pub.get_subscription_count() > 0:
            self._hud_pub.publish(
                self._bridge.cv2_to_imgmsg(output, encoding="bgr8")
            )
        if self._bird_pub.get_subscription_count() > 0:
            self._bird_pub.publish(
                self._bridge.cv2_to_imgmsg(bird_viz, encoding="bgr8")
            )

        # ── Console summary ───────────────────────────────────────
        obs_str = (
            f"  | OBS {self._obstacle_direction} @ {self._obstacle_distance:.1f} m"
            if self._obstacle_detected else ""
        )
        self.get_logger().info(
            f"[{self._frame_count:05d}] {action:<14} "
            f"lin={linear_x:+.2f}  ang={angular_z:+.3f}{obs_str}"
        )

    # ══════════════════════════════════════════════════════════════
    #  1. PERSPECTIVE TRANSFORM
    # ══════════════════════════════════════════════════════════════
    def _build_perspective(self, frame: np.ndarray):
        h, w = frame.shape[:2]
        src = np.float32([
            [w * 0.10, h * 0.95],   # bottom-left
            [w * 0.42, h * 0.60],   # top-left
            [w * 0.58, h * 0.60],   # top-right
            [w * 0.90, h * 0.95],   # bottom-right
        ])
        dst = np.float32([
            [w * 0.10, h],
            [w * 0.10, 0],
            [w * 0.90, 0],
            [w * 0.90, h],
        ])
        M        = cv2.getPerspectiveTransform(src, dst)
        M_unwarp = cv2.getPerspectiveTransform(dst, src)
        return M, M_unwarp

    def _warp(self, image: np.ndarray, M: np.ndarray) -> np.ndarray:
        h, w = image.shape[:2]
        return cv2.warpPerspective(image, M, (w, h), flags=cv2.INTER_LINEAR)

    # ══════════════════════════════════════════════════════════════
    #  2. COLOUR FILTER + EDGE DETECTION
    # ══════════════════════════════════════════════════════════════
    def _colour_filter(self, image: np.ndarray) -> np.ndarray:
        hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
        white  = cv2.inRange(hsv, np.array([0,   0, 200]), np.array([180, 25, 255]))
        yellow = cv2.inRange(hsv, np.array([15, 100, 100]), np.array([35, 255, 255]))
        mask   = cv2.bitwise_or(white, yellow)
        return cv2.bitwise_and(image, image, mask=mask)

    def _detect_edges(self, frame: np.ndarray) -> np.ndarray:
        filtered = self._colour_filter(frame)
        gray     = cv2.cvtColor(filtered, cv2.COLOR_BGR2GRAY)
        blur     = cv2.GaussianBlur(gray, (7, 7), 0)
        median   = np.median(blur)
        edges    = cv2.Canny(blur,
                             int(max(0,   0.7 * median)),
                             int(min(255, 1.3 * median)))
        kernel   = np.ones((5, 5), np.uint8)
        return cv2.morphologyEx(edges, cv2.MORPH_CLOSE, kernel)

    # ══════════════════════════════════════════════════════════════
    #  3. POLYNOMIAL LANE FITTING  (x = Ay² + By + C)
    # ══════════════════════════════════════════════════════════════
    def _fit_polynomial(self, binary_warped: np.ndarray):
        h, w          = binary_warped.shape[:2]
        histogram     = np.sum(binary_warped[h // 2:, :], axis=0)
        mid           = w // 2
        leftx_base    = int(np.argmax(histogram[:mid]))
        rightx_base   = int(np.argmax(histogram[mid:]) + mid)

        win_h         = h // self._nwin
        nonzeroy, nonzerox = binary_warped.nonzero()

        leftx_cur, rightx_cur = leftx_base, rightx_base
        left_inds, right_inds = [], []

        viz = np.dstack([binary_warped] * 3).astype(np.uint8) * 255

        for win in range(self._nwin):
            y_lo = h - (win + 1) * win_h
            y_hi = h - win * win_h
            xl_lo, xl_hi = leftx_cur  - self._margin, leftx_cur  + self._margin
            xr_lo, xr_hi = rightx_cur - self._margin, rightx_cur + self._margin

            cv2.rectangle(viz, (xl_lo, y_lo), (xl_hi, y_hi), (0, 255, 0), 2)
            cv2.rectangle(viz, (xr_lo, y_lo), (xr_hi, y_hi), (0, 255, 0), 2)

            gl = ((nonzeroy >= y_lo) & (nonzeroy < y_hi) &
                  (nonzerox >= xl_lo) & (nonzerox < xl_hi)).nonzero()[0]
            gr = ((nonzeroy >= y_lo) & (nonzeroy < y_hi) &
                  (nonzerox >= xr_lo) & (nonzerox < xr_hi)).nonzero()[0]

            left_inds.append(gl);  right_inds.append(gr)
            if len(gl) > self._minpix: leftx_cur  = int(np.mean(nonzerox[gl]))
            if len(gr) > self._minpix: rightx_cur = int(np.mean(nonzerox[gr]))

        left_inds  = np.concatenate(left_inds)
        right_inds = np.concatenate(right_inds)
        leftx,  lefty  = nonzerox[left_inds],  nonzeroy[left_inds]
        rightx, righty = nonzerox[right_inds], nonzeroy[right_inds]

        left_fit  = np.polyfit(lefty,  leftx,  2) if len(leftx)  > 50 else None
        right_fit = np.polyfit(righty, rightx, 2) if len(rightx) > 50 else None

        # Temporal smoothing
        if left_fit is not None:
            self._prev_left_fit = (
                self._alpha * self._prev_left_fit + (1 - self._alpha) * left_fit
                if self._prev_left_fit is not None else left_fit
            )
        if right_fit is not None:
            self._prev_right_fit = (
                self._alpha * self._prev_right_fit + (1 - self._alpha) * right_fit
                if self._prev_right_fit is not None else right_fit
            )

        left_fit  = self._prev_left_fit
        right_fit = self._prev_right_fit

        if left_fit  is not None: viz[lefty,  leftx]  = [255, 50,  50]
        if right_fit is not None: viz[righty, rightx] = [50,  50, 255]
        return left_fit, right_fit, viz

    # ══════════════════════════════════════════════════════════════
    #  4. DRAW POLYNOMIAL LANES (unwarped back to camera view)
    # ══════════════════════════════════════════════════════════════
    def _draw_lanes(self, original: np.ndarray,
                    left_fit, right_fit) -> tuple[np.ndarray, int | None]:
        h, w       = original.shape[:2]
        ploty      = np.linspace(0, h - 1, h)
        color_warp = np.zeros((h, w, 3), dtype=np.uint8)
        lane_cx    = None

        if left_fit is not None and right_fit is not None:
            lfx = np.polyval(left_fit,  ploty)
            rfx = np.polyval(right_fit, ploty)
            lane_cx = int((lfx[-1] + rfx[-1]) / 2)

            pts_l = np.array([np.transpose(np.vstack([lfx, ploty]))], dtype=np.int32)
            pts_r = np.array([np.flipud(np.transpose(np.vstack([rfx, ploty])))],
                             dtype=np.int32)
            cv2.fillPoly(color_warp, np.hstack((pts_l, pts_r)), (0, 180, 0))

            for i in range(len(ploty) - 1):
                iy1, iy2 = int(ploty[i]), int(ploty[i + 1])
                lx1, lx2 = int(lfx[i]), int(lfx[i + 1])
                rx1, rx2 = int(rfx[i]), int(rfx[i + 1])
                if 0 <= lx1 < w and 0 <= lx2 < w:
                    cv2.line(color_warp, (lx1, iy1), (lx2, iy2), (255, 80, 80), 6)
                if 0 <= rx1 < w and 0 <= rx2 < w:
                    cv2.line(color_warp, (rx1, iy1), (rx2, iy2), (80, 80, 255), 6)

        unwarped = self._warp(color_warp, self._M_unwarp)
        result   = cv2.addWeighted(original, 1.0, unwarped, 0.35, 0)
        return result, lane_cx

    # ══════════════════════════════════════════════════════════════
    #  5. FULL FRAME PIPELINE
    # ══════════════════════════════════════════════════════════════
    def _process_frame(self, frame: np.ndarray):
        edges      = self._detect_edges(frame)
        warped     = self._warp(edges, self._M_warp)
        lf, rf, bv = self._fit_polynomial(warped)
        output, cx = self._draw_lanes(frame, lf, rf)
        return output, bv, cx

    # ══════════════════════════════════════════════════════════════
    #  6. YOLO — expanded classes + distance estimation
    # ══════════════════════════════════════════════════════════════
    def _estimate_distance(self, cls_id: int, bbox_h_px: float) -> float | None:
        _, _, real_h = OBSTACLE_CLASSES[cls_id]
        if bbox_h_px > 0:
            return round((real_h * self._focal) / bbox_h_px, 2)
        return None

    def _run_yolo(self, frame: np.ndarray) -> np.ndarray:
        results   = self._yolo(frame, verbose=False, conf=0.5)
        annotated = results[0].plot()
        fw        = frame.shape[1]

        self._obstacle_detected  = False
        self._obstacle_direction = None
        self._obstacle_distance  = float("inf")

        for box in results[0].boxes:
            cls_id = int(box.cls)
            if cls_id not in OBSTACLE_CLASSES:
                continue

            x1, y1, x2, y2 = box.xyxy[0].tolist()
            bbox_cx  = (x1 + x2) / 2.0
            dist     = self._estimate_distance(cls_id, y2 - y1)
            label, color, _ = OBSTACLE_CLASSES[cls_id]

            direction = (
                "left"   if bbox_cx < fw * 0.38 else
                "right"  if bbox_cx > fw * 0.62 else
                "center"
            )

            dist_str  = f"{dist:.1f} m" if dist else "? m"
            cv2.putText(annotated, f"{label} [{dist_str}]",
                        (int(x1), max(int(y1) - 10, 10)),
                        cv2.FONT_HERSHEY_SIMPLEX, 0.60, color, 2)

            eff = dist if dist else 0.0
            if not self._obstacle_detected or eff < self._obstacle_distance:
                self._obstacle_detected  = True
                self._obstacle_direction = direction
                self._obstacle_distance  = eff if dist else float("inf")

        return annotated

    # ══════════════════════════════════════════════════════════════
    #  7. CONTROL DECISION
    # ══════════════════════════════════════════════════════════════
    def _decide_action(self, lane_cx: int | None) -> tuple[str, tuple]:
        if self._obstacle_detected:
            if (self._obstacle_direction == "center"
                    and self._obstacle_distance <= self._stop_d):
                return "STOP",      (0,   0,   255)
            elif self._obstacle_direction == "left":
                return "GO RIGHT",  (0, 165,   255)
            elif self._obstacle_direction == "right":
                return "GO LEFT",   (0, 165,   255)
            else:
                return "STOP",      (0, 100,   255)

        if lane_cx is not None:
            err = lane_cx - self._frame_cx
            if abs(err) < 30:  return "GO STRAIGHT", (0,  220,    0)
            elif err > 0:      return "STEER LEFT",  (0,  200,  255)
            else:              return "STEER RIGHT", (0,  200,  255)

        return "SEARCHING", (180, 180, 180)

    def _compute_velocity(self, action: str,
                          lane_cx: int | None) -> tuple[float, float]:
        if action == "STOP":
            return 0.0, 0.0
        if action in ("GO LEFT", "STEER LEFT"):
            return self._lin_spd * 0.75, +self._str_spd
        if action in ("GO RIGHT", "STEER RIGHT"):
            return self._lin_spd * 0.75, -self._str_spd
        if action == "GO STRAIGHT" and lane_cx is not None:
            err = lane_cx - self._frame_cx
            return self._lin_spd, -err / 200.0
        return 0.0, 0.0   # SEARCHING

    # ══════════════════════════════════════════════════════════════
    #  8. HUD OVERLAY
    # ══════════════════════════════════════════════════════════════
    def _draw_hud(self, frame: np.ndarray,
                  action: str, color: tuple,
                  lane_cx: int | None,
                  lin: float, ang: float) -> np.ndarray:
        h, w = frame.shape[:2]
        cx   = self._frame_cx or w // 2

        # Semi-transparent bottom bar
        overlay = frame.copy()
        cv2.rectangle(overlay, (0, h - 130), (w, h), (20, 20, 20), -1)
        cv2.addWeighted(overlay, 0.55, frame, 0.45, 0, frame)

        # Lane centre guide-line
        if lane_cx is not None:
            cv2.line(frame, (cx, h - 140), (cx, h - 135), (255, 255, 0), 2)
            cv2.line(frame, (lane_cx, h - 140), (cx, h - 140), (255, 255, 0), 2)
            cv2.circle(frame, (lane_cx, h - 140), 5, (255, 255, 0), -1)

        # Directional label
        icon  = _DIR_ICONS.get(action, "?")
        label = f"  {icon}  {action}  {icon}  "
        ts    = cv2.getTextSize(label, cv2.FONT_HERSHEY_DUPLEX, 1.4, 2)[0]
        cv2.putText(frame, label,
                    ((w - ts[0]) // 2, h - 72),
                    cv2.FONT_HERSHEY_DUPLEX, 1.4, color, 2, cv2.LINE_AA)

        # Obstacle distance badge
        if self._obstacle_detected and self._obstacle_distance != float("inf"):
            badge = (f"Obstacle: {self._obstacle_distance:.1f} m  "
                     f"[{(self._obstacle_direction or '').upper()}]")
            bts = cv2.getTextSize(badge, cv2.FONT_HERSHEY_SIMPLEX, 0.60, 1)[0]
            cv2.putText(frame, badge,
                        ((w - bts[0]) // 2, h - 40),
                        cv2.FONT_HERSHEY_SIMPLEX, 0.60, (0, 180, 255), 1,
                        cv2.LINE_AA)

        # Telemetry row
        telem = f"Speed: {lin:+.2f} m/s   Steer: {ang:+.3f} rad/s"
        tts   = cv2.getTextSize(telem, cv2.FONT_HERSHEY_SIMPLEX, 0.52, 1)[0]
        cv2.putText(frame, telem,
                    ((w - tts[0]) // 2, h - 10),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.52, (200, 200, 200), 1,
                    cv2.LINE_AA)
        return frame


# ══════════════════════════════════════════════════════════════════
#  ENTRY POINT
# ══════════════════════════════════════════════════════════════════
def main(args=None):
    rclpy.init(args=args)
    node = HybridBot()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        node.get_logger().info("Shutting down HybridBot …")
    finally:
        # Zero velocity on shutdown — safety first
        stop = Twist()
        node._cmd_pub.publish(stop)
        node.destroy_node()
        rclpy.shutdown()


if __name__ == "__main__":
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
