# 📘 Master Study Guide: ROS 2 Service–Client (C++ + Python)

---

## 🔰 1. Core Concept: Service–Client Communication

### 📌 Definition

* **Client Node** → Sends request
* **Service Node** → Processes & sends response
* Communication defined by a **`.srv` file**

---

## 🧠 Real-Life Analogy

Think of a **restaurant system**:

* 🧍 Client → Customer placing order
* 👨‍🍳 Service → Kitchen preparing food
* 🍽 Response → Delivered meal

Unlike topics:

* Topics = continuous streaming
* Services = **request → response (one-time interaction)**

---

## 🔹 Communication Flow

```text
Client ---> Request ---> Service
Client <--- Response <--- Service
```

---

## 🧠 What Happens Internally

1. Client sends structured request
2. ROS 2 uses DDS to locate service
3. Service executes callback
4. Response returned synchronously/asynchronously

---

# 📁 2. Service Definition (.srv)

## 📌 Structure

```text
int64 a
int64 b
---
int64 sum
```

---

## 🔍 Syntax Breakdown

| Section     | Meaning       |
| ----------- | ------------- |
| Above `---` | Request data  |
| Below `---` | Response data |

---

## 🧠 Concept

* `.srv` defines **contract between nodes**
* Both client & service must use SAME type

---

# 📁 3. Create Package (C++)

## 💻 Command

```bash
ros2 pkg create --build-type ament_cmake --license Apache-2.0 cpp_srvcli --dependencies rclcpp example_interfaces
```

---

## 🔍 Syntax Breakdown

| Part                 | Meaning                 |
| -------------------- | ----------------------- |
| `--dependencies`     | Auto adds required libs |
| `rclcpp`             | C++ ROS API             |
| `example_interfaces` | Contains `.srv`         |

---

# 📁 4. C++ Service Node

## 💻 Code

```cpp
#include "rclcpp/rclcpp.hpp"
#include "example_interfaces/srv/add_two_ints.hpp"

void add(
  const std::shared_ptr<example_interfaces::srv::AddTwoInts::Request> request,
  std::shared_ptr<example_interfaces::srv::AddTwoInts::Response> response)
{
  response->sum = request->a + request->b;
}
```

---

## 🧠 Real-Life Analogy

This function = **kitchen cooking logic**

---

### 💻 Main Function

```cpp
int main(int argc, char **argv)
{
  rclcpp::init(argc, argv);

  auto node = rclcpp::Node::make_shared("add_two_ints_server");

  auto service = node->create_service<example_interfaces::srv::AddTwoInts>(
    "add_two_ints", &add);

  rclcpp::spin(node);
  rclcpp::shutdown();
}
```

---

## 🔍 Syntax Breakdown

### Create Service

```cpp
create_service<ServiceType>("service_name", callback);
```

| Part         | Meaning             |
| ------------ | ------------------- |
| ServiceType  | `.srv` type         |
| service_name | Communication name  |
| callback     | Processing function |

---

## 🧠 Concept

* Service **waits idle**
* Executes only when request arrives

---

# 📁 5. C++ Client Node

## 💻 Code

```cpp
auto client = node->create_client<example_interfaces::srv::AddTwoInts>("add_two_ints");

auto request = std::make_shared<example_interfaces::srv::AddTwoInts::Request>();
request->a = atoll(argv[1]);
request->b = atoll(argv[2]);
```

---

## 🔍 Syntax Breakdown

| Part            | Meaning            |
| --------------- | ------------------ |
| `create_client` | Connect to service |
| `request->a/b`  | Input values       |

---

### ⏳ Wait for Service

```cpp
client->wait_for_service(1s);
```

| Part | Meaning        |
| ---- | -------------- |
| `1s` | Retry interval |

---

### 📤 Send Request

```cpp
auto result = client->async_send_request(request);
```

---

### 📥 Receive Response

```cpp
rclcpp::spin_until_future_complete(node, result);
result.get()->sum;
```

---

## 🧠 Concept

* Client:

  1. Waits for service
  2. Sends request
  3. Waits for response

---

# 📁 6. CMake Configuration

## 💻 Code

```cmake
find_package(rclcpp REQUIRED)
find_package(example_interfaces REQUIRED)

add_executable(server src/add_two_ints_server.cpp)
ament_target_dependencies(server rclcpp example_interfaces)

add_executable(client src/add_two_ints_client.cpp)
ament_target_dependencies(client rclcpp example_interfaces)

install(TARGETS
  server
  client
  DESTINATION lib/${PROJECT_NAME})
```

---

## 🧠 Concept

* Registers executable for `ros2 run`

---

# 📁 7. Python Service Node

## 💻 Code

```python
from example_interfaces.srv import AddTwoInts
import rclpy
from rclpy.node import Node

class MinimalService(Node):

    def __init__(self):
        super().__init__('minimal_service')
        self.srv = self.create_service(AddTwoInts, 'add_two_ints', self.callback)

    def callback(self, request, response):
        response.sum = request.a + request.b
        return response
```

---

## 🔍 Syntax Breakdown

| Part             | Meaning           |
| ---------------- | ----------------- |
| `create_service` | Creates service   |
| `callback`       | Processes request |

---

# 📁 8. Python Client Node

## 💻 Code

```python
self.cli = self.create_client(AddTwoInts, 'add_two_ints')

while not self.cli.wait_for_service(timeout_sec=1.0):
    pass

self.req = AddTwoInts.Request()
self.req.a = a
self.req.b = b

future = self.cli.call_async(self.req)
```

---

## 🔍 Syntax Breakdown

| Part               | Meaning             |
| ------------------ | ------------------- |
| `create_client`    | Connect to service  |
| `wait_for_service` | Ensure availability |
| `call_async`       | Send request        |

---

## 🧠 Concept

* Python uses **async future system**
* Non-blocking execution

---

# ⚖️ Topic vs Service vs Action

| Feature  | Topic   | Service          | Action     |
| -------- | ------- | ---------------- | ---------- |
| Type     | Stream  | Request/Response | Long tasks |
| Blocking | No      | Yes              | Async      |
| Use Case | Sensors | Commands         | Navigation |

---

# 🚀 9. Build & Run

## 💻 Install Dependencies

```bash
rosdep install -i --from-path src --rosdistro iron -y
```

---

## 💻 Build

```bash
colcon build --packages-select cpp_srvcli
```

---

## 💻 Source

```bash
source install/setup.bash
```

---

## 💻 Run Service

```bash
ros2 run cpp_srvcli server
```

---

## 💻 Run Client

```bash
ros2 run cpp_srvcli client 2 3
```

---

## 🧠 Internal Execution Flow

1. Service node starts → waits
2. Client starts → sends request
3. Service processes → sends response
4. Client prints result

---

# 📌 Pro Tips (Efficiency)

* Check services:

```bash
ros2 service list
```

* Call manually:

```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 2, b: 3}"
```

* Inspect type:

```bash
ros2 interface show example_interfaces/srv/AddTwoInts
```

---

# 🧠 Common Mistakes

| Issue         | Reason              |
| ------------- | ------------------- |
| No response   | Service not running |
| Type mismatch | Different `.srv`    |
| Infinite wait | Wrong service name  |

---

# 🔁 Memory Hook: **SCR**

| Letter | Meaning              |
| ------ | -------------------- |
| **S**  | Service created      |
| **C**  | Client sends request |
| **R**  | Response returned    |

---

## 🧠 Final Insight

> **Service = Synchronous, structured, one-shot communication**

Used for:

* Commands (start/stop robot)
* Calculations
* Configuration changes

---

If you want next:
➡️ I can connect this with **ESP32 + ROS2 service control (real robot control)** or
➡️ Move to **Actions (advanced async tasks like navigation)**
