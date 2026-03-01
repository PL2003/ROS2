# ROS 2 README – Nodes, Topics & Services (with `turtlesim`)

Reference system: **ROS 2** with the `turtlesim` package.

---

## 1️⃣ Nodes in ROS 2

### 🔹 Definition

A **Node** is a single, modular process in ROS 2 that performs a specific task (e.g., sensor reading, motor control, visualization).

**Daily life analogy:**
A node is like a worker in a factory. Each worker has a specific role but communicates with others.

---

## 1.1 `ros2 run` – Launch a Node

### 📌 Syntax

```bash
ros2 run <package_name> <executable_name>
```

### 📌 Example

```bash
ros2 run turtlesim turtlesim_node
```

### 🔎 What it does

* `ros2 run` → executes a compiled program.
* `turtlesim` → package name.
* `turtlesim_node` → executable inside that package.
* Launches the simulator window.

**Daily Life Example:**
Like opening a specific app from a folder.

---

## 1.2 `ros2 node list` – See Active Nodes

### 📌 Syntax

```bash
ros2 node list
```

### 📌 Output Example

```bash
/turtlesim
/teleop_turtle
```

### 🔎 What it does

* Lists all running nodes.
* Helps identify names for further interaction.

**Analogy:** Checking which workers are currently active.

---

## 1.3 Node Remapping

### 📌 Syntax

```bash
ros2 run <pkg> <exe> --ros-args --remap __node:=<new_name>
```

### 📌 Example

```bash
ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle
```

### 🔎 What it does

* `--ros-args` → enables ROS-specific arguments.
* `--remap` → changes default properties.
* `__node:=my_turtle` → renames node.

**Analogy:** Renaming a worker’s ID badge.

---

## 1.4 `ros2 node info` – Inspect a Node

### 📌 Syntax

```bash
ros2 node info <node_name>
```

### 📌 Example

```bash
ros2 node info /my_turtle
```

### 🔎 Shows:

* Publishers
* Subscribers
* Services
* Actions

**Purpose:** Understand node’s communication graph.

---

# 2️⃣ Topics in ROS 2

### 🔹 Definition

A **Topic** is a named communication channel used for continuous data streaming between nodes.

**Analogy:**
A public broadcast channel (like a radio frequency).

---

## 2.1 `rqt_graph` – Visual Graph Tool

```bash
rqt_graph
```

### Shows:

* Nodes
* Topics
* Connections

**Purpose:** Visual debugging tool.

---

## 2.2 `ros2 topic list`

```bash
ros2 topic list
```

With type:

```bash
ros2 topic list -t
```

### Example Output

```bash
/turtle1/cmd_vel [geometry_msgs/msg/Twist]
```

### Meaning

* Topic name: `/turtle1/cmd_vel`
* Message type: `geometry_msgs/msg/Twist`

---

## 2.3 `ros2 topic echo` – See Live Data

```bash
ros2 topic echo <topic_name>
```

### Example

```bash
ros2 topic echo /turtle1/cmd_vel
```

### Output

```yaml
linear:
  x: 2.0
angular:
  z: 0.0
```

### What it does

* Prints live message data.

**Analogy:** Listening to a radio broadcast.

---

## 2.4 `ros2 topic info`

```bash
ros2 topic info /turtle1/cmd_vel
```

### Output

```
Type: geometry_msgs/msg/Twist
Publisher count: 1
Subscription count: 2
```

Shows communication direction.

---

## 2.5 `ros2 interface show` – Inspect Message Structure

```bash
ros2 interface show geometry_msgs/msg/Twist
```

### Output

```
Vector3 linear
float64 x
float64 y
float64 z
Vector3 angular
float64 x
float64 y
float64 z
```

### Meaning

* Two vectors: `linear`, `angular`
* Each has x, y, z values

**Why important?**
Publisher and subscriber must match message structure.

---

## 2.6 `ros2 topic pub` – Publish Data Manually

### 📌 Syntax

```bash
ros2 topic pub <topic_name> <msg_type> '<args>'
```

### 📌 Example

```bash
ros2 topic pub /turtle1/cmd_vel geometry_msgs/msg/Twist \
"{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
```

### Explanation

* YAML format required.
* Sends velocity command.
* Default rate: 1 Hz.

### Publish Once

```bash
ros2 topic pub --once -w 2 /turtle1/cmd_vel geometry_msgs/msg/Twist "{...}"
```

* `--once` → send once.
* `-w 2` → wait for 2 subscribers.

**Analogy:** Sending a command over a walkie-talkie.

---

# 3️⃣ Services in ROS 2

### 🔹 Definition

A **Service** is a request-response communication model.

Unlike topics (continuous), services respond only when called.

**Analogy:**
Making a phone call and waiting for an answer.

---

## 3.1 `ros2 service list`

```bash
ros2 service list
```

With type:

```bash
ros2 service list -t
```

---

## 3.2 `ros2 service type`

```bash
ros2 service type /clear
```

Output:

```
std_srvs/srv/Empty
```

---

## 3.3 `ros2 service find`

```bash
ros2 service find std_srvs/srv/Empty
```

Finds all services of that type.

---

## 3.4 `ros2 interface show` (Service Structure)

```bash
ros2 interface show turtlesim/srv/Spawn
```

Output:

```
float32 x
float32 y
float32 theta
string name
---
string name
```

### Structure Meaning

Above `---` → Request
Below `---` → Response

---

## 3.5 `ros2 service call`

### 📌 Syntax

```bash
ros2 service call <service_name> <service_type> <arguments>
```

### 📌 Example 1 – Clear Screen

```bash
ros2 service call /clear std_srvs/srv/Empty
```

### 📌 Example 2 – Spawn New Turtle

```bash
ros2 service call /spawn turtlesim/srv/Spawn \
"{x: 2, y: 2, theta: 0.2, name: ''}"
```

### What Happens

* Sends pose request.
* Returns:

```
name: turtle2
```

* New turtle appears.

---

# 🔁 Topic vs Service Comparison

| Feature       | Topic                 | Service              |
| ------------- | --------------------- | -------------------- |
| Communication | One-way               | Request/Response     |
| Data Flow     | Continuous            | On demand            |
| Best For      | Sensor data, velocity | Configuration, reset |
| Example       | `/cmd_vel`            | `/spawn`             |

---

# 🧠 Key Concept Flow

```
Node → publishes → Topic → subscribed by → Node
Node → calls → Service → responds → Node
```

---

# 📌 Quick Memory Hooks

* **Node** = Worker
* **Topic** = Broadcast channel
* **Service** = Phone call
* **Message** = Data format
* **Interface show** = Blueprint
* **rqt_graph** = Communication map

---

# 🚀 Final Summary

* Nodes are modular processes.
* Topics enable continuous streaming.
* Services use request-response model.
* Use `ros2 node list/info` for node inspection.
* Use `ros2 topic list/echo/pub/info` for topic control.
* Use `ros2 service list/type/call` for services.
* Message types must match between nodes.
* YAML syntax required for CLI publishing/calling.
* `rqt_graph` visualizes the ROS graph.

---

If required, next logical step: **Parameters and Actions in ROS 2.**
