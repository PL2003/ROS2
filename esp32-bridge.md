# 🚀 [ROS 2 ↔ ESP32 Motor Control (BTS7960) — Exam Revision Notes](https://github.com/manojramesh-io/ROS2-ESP32-Serial-Bridge/tree/main)

---

# 📌 1. Definitions (Core Terms)

* **ROS 2 Workspace**: Structured directory containing ROS packages, built using `colcon`.
* **ESP32**: Microcontroller used for hardware interfacing (motors, sensors).
* **Bridge (ROS ↔ ESP32)**: Communication layer (usually Serial/UART/WiFi) to exchange commands/data.
* **Node (ROS 2)**: Executable process that publishes/subscribes to topics.
* **Topic**: Communication channel (e.g., `/cmd_vel`).
* **Twist Message (`geometry_msgs/Twist`)**: Standard ROS message for velocity control.
* **BTS7960 Motor Driver**: High-current H-bridge driver (43A) used for DC motor control via PWM.
* **PWM (Pulse Width Modulation)**: Controls motor speed by varying duty cycle.

---

# 🧠 2. System Architecture (Big Picture)

```
ROS 2 (PC)  ─────►  ESP32  ─────►  BTS7960  ─────►  Motor
   Node            Serial/WiFi        PWM Control
```

### 🔁 Data Flow

1. ROS publishes velocity command
2. ESP32 receives command
3. Converts to PWM signals
4. BTS7960 drives motor

---

# ⚙️ 3. Hardware Setup (BTS7960 + ESP32)

### 🔌 BTS7960 Pins

| Pin  | Function       |
| ---- | -------------- |
| RPWM | Forward PWM    |
| LPWM | Reverse PWM    |
| R_EN | Enable Forward |
| L_EN | Enable Reverse |
| VCC  | 5V Logic       |
| GND  | Ground         |

### 🔗 ESP32 Connection Example

* RPWM → GPIO 25
* LPWM → GPIO 26
* R_EN → GPIO 27
* L_EN → GPIO 14

---

# 🔄 4. Process Flow (Step-by-Step)

## 🧩 Step 1: ROS 2 Environment Setup

```bash
source /opt/ros/humble/setup.bash
```

* Loads ROS environment variables

---

## 🧩 Step 2: Create Workspace

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build
```

---

## 🧩 Step 3: Create Control Node

```bash
ros2 pkg create --build-type ament_python motor_control
```

---

## 🧩 Step 4: ROS 2 Publisher (Send Commands)

### 📜 Python Node

```python
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist

class MotorPublisher(Node):
    def __init__(self):
        super().__init__('motor_pub')
        self.pub = self.create_publisher(Twist, '/cmd_vel', 10)

    def send_cmd(self):
        msg = Twist()
        msg.linear.x = 0.5   # forward speed
        msg.angular.z = 0.0  # no turning
        self.pub.publish(msg)

rclpy.init()
node = MotorPublisher()
node.send_cmd()
rclpy.spin(node)
```

### 🔍 Syntax Understanding

* `create_publisher()` → creates topic publisher
* `Twist()` → velocity message
* `linear.x` → forward/backward
* `angular.z` → rotation

---

## 🧩 Step 5: Serial Bridge (ROS → ESP32)

### Python Serial Node

```python
import serial

ser = serial.Serial('/dev/ttyUSB0', 115200)

def send_to_esp(speed):
    command = f"{speed}\n"
    ser.write(command.encode())
```

### 🔍 Syntax

* `serial.Serial(port, baud)` → open communication
* `write()` → send bytes
* `.encode()` → string → bytes

---

## 🧩 Step 6: ESP32 Code (Motor Control)

```cpp
#define RPWM 25
#define LPWM 26
#define R_EN 27
#define L_EN 14

void setup() {
  Serial.begin(115200);

  pinMode(RPWM, OUTPUT);
  pinMode(LPWM, OUTPUT);
  pinMode(R_EN, OUTPUT);
  pinMode(L_EN, OUTPUT);

  digitalWrite(R_EN, HIGH);
  digitalWrite(L_EN, HIGH);
}

void loop() {
  if (Serial.available()) {
    int speed = Serial.parseInt();

    if (speed > 0) {
      analogWrite(RPWM, speed);
      analogWrite(LPWM, 0);
    } else {
      analogWrite(RPWM, 0);
      analogWrite(LPWM, -speed);
    }
  }
}
```

### 🔍 Syntax Breakdown

* `Serial.available()` → check incoming data
* `Serial.parseInt()` → read integer command
* `analogWrite(pin, value)` → PWM signal (0–255)
* Forward: RPWM active
* Reverse: LPWM active

---

# 📊 5. Comparison Table

| Component     | Role              | Input        | Output           |
| ------------- | ----------------- | ------------ | ---------------- |
| ROS 2 Node    | Command generator | User/logic   | Velocity (Twist) |
| Serial Bridge | Communication     | ROS data     | Serial data      |
| ESP32         | Controller        | Serial input | PWM signals      |
| BTS7960       | Power driver      | PWM          | Motor motion     |

---

# ⚖️ 6. Key Principles

### 🔑 Communication Principle

* ROS uses **publish-subscribe model**
* ESP32 uses **serial parsing**

### 🔑 Control Principle

* Speed ∝ PWM duty cycle
* Direction determined by:

  * RPWM vs LPWM

### 🔑 Layered Architecture

* **High-level (ROS)** → decision
* **Mid-level (ESP32)** → control logic
* **Low-level (Driver)** → power actuation

---

# 🔧 7. Extendability (Add Future Components)

## 🧩 Generic Pattern

### ROS Side

* Create new topic:

```bash
/light_cmd
/gps_data
/lora_tx
```

### ESP32 Side

```cpp
if(command == "LIGHT_ON") digitalWrite(pin, HIGH);
```

---

## 🔌 Add New Components

| Component | ROS Topic      | ESP32 Action |
| --------- | -------------- | ------------ |
| LED/Light | `/light_cmd`   | digitalWrite |
| GPS       | `/gps_data`    | Serial read  |
| LoRa      | `/lora_tx`     | SPI transmit |
| Sensor    | `/sensor_data` | analogRead   |

---

## 🔁 Modular Design Rule

> Each component = Separate topic + handler function

---

# 📌 8. Important Commands & Syntax

| Command                     | Purpose             |
| --------------------------- | ------------------- |
| `colcon build`              | Build workspace     |
| `source install/setup.bash` | Activate workspace  |
| `ros2 run pkg node`         | Run node            |
| `ros2 topic echo /cmd_vel`  | Debug topic         |
| `ros2 topic pub`            | Send manual command |

---

# 🧠 9. Memory Hooks (Visualization)

* 🧩 ROS = Brain
* ⚡ ESP32 = Nerves
* 🔋 BTS7960 = Muscles
* 🔄 Motor = Movement

---

# 📌 10. Final Summary (Quick Revision)

* ROS 2 sends velocity via `/cmd_vel`
* Serial bridge transfers commands to ESP32
* ESP32 converts commands → PWM signals
* BTS7960 drives motor (forward/reverse)
* PWM controls speed, direction via dual pins
* Workspace must be sourced before use
* Modular design allows adding sensors/devices easily
* Topics = communication backbone
* ESP32 acts as real-time controller
* System is scalable for full robotic platforms

---

# 🎯 End Goal

✔ ROS-based high-level control
✔ ESP32 real-time motor actuation
✔ Expandable robotics system for future components
