# 📘 Master Study Guide: ROS 2 Publisher & Subscriber (C++ + Python)

---

## 🔰 1. Core Concept: ROS 2 Communication (Topic-Based)

### 📌 What is Happening?

* Two **nodes** communicate via a **topic**
* One node = **Publisher (Talker)** → sends data
* Another node = **Subscriber (Listener)** → receives data

### 🧠 Real-Life Analogy

Think of a **radio station system**:

* 📡 Publisher = Radio station broadcasting
* 📻 Subscriber = Radios tuned to that frequency
* 📶 Topic = Frequency channel

---

## 🔹 Communication Flow

```text
Publisher Node  --->  Topic  --->  Subscriber Node
   (Talker)         (channel)      (Listener)
```

---

## 🧠 What Happens Internally

1. ROS 2 creates a **graph system**
2. Publisher advertises a **topic**
3. Subscriber listens to the same topic
4. Messages are transferred using **DDS middleware**

---

# 📁 2. Workspace & Package Creation

## 💻 Command

```bash
ros2 pkg create --build-type ament_cmake --license Apache-2.0 cpp_pubsub
```

## 🔍 Syntax Breakdown

| Part                       | Meaning              |
| -------------------------- | -------------------- |
| `ros2 pkg create`          | Create a new package |
| `--build-type ament_cmake` | C++ build system     |
| `--license Apache-2.0`     | License type         |
| `cpp_pubsub`               | Package name         |

---

## 🧠 Concept

* ROS packages are like **modules**
* Each contains:

  * Source code
  * Dependencies
  * Build configuration

---

# 📁 3. C++ Publisher Node (Talker)

## 💻 Code

```cpp
#include <chrono>
#include <functional>
#include <memory>
#include <string>

#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"

using namespace std::chrono_literals;

class MinimalPublisher : public rclcpp::Node
{
public:
    MinimalPublisher() : Node("minimal_publisher"), count_(0)
    {
        publisher_ = this->create_publisher<std_msgs::msg::String>("topic", 10);

        timer_ = this->create_wall_timer(
            500ms, std::bind(&MinimalPublisher::timer_callback, this));
    }

private:
    void timer_callback()
    {
        auto message = std_msgs::msg::String();
        message.data = "Hello, world! " + std::to_string(count_++);

        RCLCPP_INFO(this->get_logger(), "Publishing: '%s'", message.data.c_str());

        publisher_->publish(message);
    }

    rclcpp::TimerBase::SharedPtr timer_;
    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr publisher_;
    size_t count_;
};

int main(int argc, char * argv[])
{
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<MinimalPublisher>());
    rclcpp::shutdown();
    return 0;
}
```

---

## 🔍 Syntax Breakdown

### Publisher Creation

```cpp
create_publisher<std_msgs::msg::String>("topic", 10);
```

| Part                    | Meaning      |
| ----------------------- | ------------ |
| `std_msgs::msg::String` | Message type |
| `"topic"`               | Topic name   |
| `10`                    | Queue size   |

---

### Timer

```cpp
create_wall_timer(500ms, callback);
```

| Part       | Meaning            |
| ---------- | ------------------ |
| `500ms`    | Runs every 0.5 sec |
| `callback` | Function executed  |

---

### Publish Message

```cpp
publisher_->publish(message);
```

* Sends data to topic

---

## 🧠 Concept

* Publisher runs on a **timer loop**
* Generates and sends data continuously

---

# 📁 4. C++ Subscriber Node (Listener)

## 💻 Code

```cpp
#include <memory>
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"

using std::placeholders::_1;

class MinimalSubscriber : public rclcpp::Node
{
public:
    MinimalSubscriber() : Node("minimal_subscriber")
    {
        subscription_ = this->create_subscription<std_msgs::msg::String>(
            "topic", 10,
            std::bind(&MinimalSubscriber::topic_callback, this, _1));
    }

private:
    void topic_callback(const std_msgs::msg::String & msg) const
    {
        RCLCPP_INFO(this->get_logger(), "I heard: '%s'", msg.data.c_str());
    }

    rclcpp::Subscription<std_msgs::msg::String>::SharedPtr subscription_;
};

int main(int argc, char * argv[])
{
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<MinimalSubscriber>());
    rclcpp::shutdown();
    return 0;
}
```

---

## 🔍 Syntax Breakdown

### Subscription

```cpp
create_subscription<std_msgs::msg::String>("topic", 10, callback);
```

| Part     | Meaning              |
| -------- | -------------------- |
| Topic    | Must match publisher |
| Queue    | Buffer               |
| Callback | Runs on data receive |

---

## 🧠 Concept

* Subscriber is **event-driven**
* Executes only when data arrives

---

# 📁 5. Build System (CMake)

## 💻 CMakeLists.txt

```cmake
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)

add_executable(talker src/publisher_member_function.cpp)
ament_target_dependencies(talker rclcpp std_msgs)

add_executable(listener src/subscriber_member_function.cpp)
ament_target_dependencies(listener rclcpp std_msgs)

install(TARGETS
  talker
  listener
  DESTINATION lib/${PROJECT_NAME})
```

---

## 🔍 Syntax Breakdown

| Command                     | Purpose               |
| --------------------------- | --------------------- |
| `find_package`              | Load dependencies     |
| `add_executable`            | Compile node          |
| `ament_target_dependencies` | Link libraries        |
| `install`                   | Make runnable via ROS |

---

# 📁 6. Python Version

---

## 💻 Publisher

```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MinimalPublisher(Node):
    def __init__(self):
        super().__init__('minimal_publisher')

        self.publisher_ = self.create_publisher(String, 'topic', 10)
        self.timer = self.create_timer(0.5, self.timer_callback)
        self.i = 0

    def timer_callback(self):
        msg = String()
        msg.data = f'Hello World: {self.i}'
        self.publisher_.publish(msg)

        self.get_logger().info(f'Publishing: "{msg.data}"')
        self.i += 1

def main():
    rclpy.init()
    node = MinimalPublisher()
    rclpy.spin(node)
    rclpy.shutdown()
```

---

## 💻 Subscriber

```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MinimalSubscriber(Node):
    def __init__(self):
        super().__init__('minimal_subscriber')

        self.subscription = self.create_subscription(
            String,
            'topic',
            self.listener_callback,
            10)

    def listener_callback(self, msg):
        self.get_logger().info(f'I heard: "{msg.data}"')

def main():
    rclpy.init()
    node = MinimalSubscriber()
    rclpy.spin(node)
    rclpy.shutdown()
```

---

# ⚖️ C++ vs Python (ROS 2)

| Feature    | C++                | Python            |
| ---------- | ------------------ | ----------------- |
| Speed      | Fast               | Slower            |
| Complexity | High               | Low               |
| Use Case   | Real-time robotics | Rapid prototyping |
| Syntax     | Verbose            | Simple            |

---

# 🚀 7. Build & Run

## 💻 Install Dependencies

```bash
rosdep install -i --from-path src --rosdistro iron -y
```

### 🔍 Syntax Breakdown

| Part               | Meaning              |
| ------------------ | -------------------- |
| `rosdep install`   | Install missing deps |
| `--from-path src`  | Scan workspace       |
| `--rosdistro iron` | ROS version          |
| `-y`               | Auto confirm         |

---

## 💻 Build

```bash
colcon build --packages-select cpp_pubsub
```

---

## 💻 Source Workspace

```bash
source install/setup.bash
```

---

## 💻 Run Nodes

### Terminal 1

```bash
ros2 run cpp_pubsub talker
```

### Terminal 2

```bash
ros2 run cpp_pubsub listener
```

---

# 🧠 What Happens Internally (Execution Phase)

1. `colcon build` compiles nodes
2. `source` registers package in ROS environment
3. `ros2 run` launches node
4. DDS middleware connects nodes
5. Messages flow via topic

---

# 📌 Pro Tips (Efficiency)

* Use:

```bash
ros2 topic list
```

→ View active topics

* Debug:

```bash
ros2 topic echo /topic
```

* Visualize graph:

```bash
rqt_graph
```

* Auto source:

```bash
echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
```

---

# 🔁 Memory Hook: **PUBLISH**

| Letter | Meaning            |
| ------ | ------------------ |
| **P**  | Package create     |
| **U**  | Use dependencies   |
| **B**  | Build (colcon)     |
| **L**  | Launch nodes       |
| **I**  | Interact via topic |
| **S**  | Subscriber listens |
| **H**  | Handle callbacks   |

---

## 🧠 Final Insight

ROS 2 communication is fundamentally:

> **Asynchronous, topic-based, and loosely coupled**

Mastering Publisher/Subscriber = foundation for:

* Sensors
* Actuators
* AI perception
* Full robotic systems
