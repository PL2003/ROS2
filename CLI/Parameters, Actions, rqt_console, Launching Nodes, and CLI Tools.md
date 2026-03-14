
# ROS 2 Notes: Parameters, Actions, rqt_console, Launching Nodes, and CLI Tools

Structured revision notes focused on **ROS2 system configuration, monitoring, and execution tools**.  
Includes **syntax explanations, command structure, and robotics use cases**.

---

# 1. ROS 2 System Overview

ROS2 systems are composed of **nodes** that communicate through several mechanisms.

| Communication Type | Purpose |
|---|---|
| Topics | Continuous data streams |
| Services | Request–response interaction |
| Actions | Long-running tasks with feedback |
| Parameters | Node configuration values |

Example robotics system:

```

Sensors → Nodes → Topics → Robot Controller
↑
Parameters

````

---

# 2. Running ROS 2 Nodes (Setup)

Example using **turtlesim**.

### Start Simulation Node

```bash
ros2 run turtlesim turtlesim_node
````

Explanation:

| Part             | Meaning         |
| ---------------- | --------------- |
| `ros2`           | ROS2 CLI tool   |
| `run`            | Execute a node  |
| `turtlesim`      | Package name    |
| `turtlesim_node` | Executable node |

---

### Start Teleoperation Node

```bash
ros2 run turtlesim turtle_teleop_key
```

Function:

Allows controlling the turtle using keyboard arrows.

---

# 3. ROS 2 Parameters

## Definition

Parameters are **configuration values stored inside nodes**.

They allow:

* Changing system behavior
* Runtime configuration
* Persistent configuration storage

Example:

```
background color
speed scaling
simulation time
```

---

# 4. Listing Parameters

### Command

```bash
ros2 param list
```

Example output

```
/turtlesim:
  background_b
  background_g
  background_r
```

Explanation:

| Element        | Meaning   |
| -------------- | --------- |
| `/turtlesim`   | Node name |
| `background_r` | Parameter |

---

# 5. Getting Parameter Value

### Syntax

```bash
ros2 param get <node_name> <parameter_name>
```

Example

```bash
ros2 param get /turtlesim background_g
```

Output

```
Integer value is: 86
```

Explanation:

| Element        | Meaning       |
| -------------- | ------------- |
| `/turtlesim`   | Node          |
| `background_g` | Parameter     |
| `86`           | Current value |

---

# 6. Changing Parameter Value

### Syntax

```bash
ros2 param set <node_name> <parameter_name> <value>
```

Example

```bash
ros2 param set /turtlesim background_r 150
```

Result:

The turtlesim background color changes.

---

# 7. Saving Parameters to File

### Syntax

```bash
ros2 param dump <node_name> > file.yaml
```

Example

```bash
ros2 param dump /turtlesim > turtlesim.yaml
```

Generated YAML file

```yaml
/turtlesim:
  ros__parameters:
    background_b: 255
    background_g: 86
    background_r: 150
```

Purpose:

* Save configuration
* Reuse later

---

# 8. Loading Parameters

### Syntax

```bash
ros2 param load <node_name> <file>
```

Example

```bash
ros2 param load /turtlesim turtlesim.yaml
```

This updates parameters inside the running node.

---

# 9. Loading Parameters at Startup

### Syntax

```bash
ros2 run <package> <node> --ros-args --params-file <file>
```

Example

```bash
ros2 run turtlesim turtlesim_node --ros-args --params-file turtlesim.yaml
```

Explanation:

| Flag            | Meaning               |
| --------------- | --------------------- |
| `--ros-args`    | Enables ROS arguments |
| `--params-file` | Load YAML parameters  |

---

# 10. ROS 2 Actions

## Definition

Actions handle **long-running tasks** with feedback.

Example tasks:

* Robot navigation
* Path planning
* Object manipulation

---

## Action Structure

Actions consist of **three parts**:

| Component | Purpose          |
| --------- | ---------------- |
| Goal      | Task request     |
| Feedback  | Progress updates |
| Result    | Final outcome    |

---

## Action Architecture

```
Action Client
      ↓
Send Goal
      ↓
Action Server
      ↓
Feedback → Client
      ↓
Final Result
```

---

# 11. Turtlesim Action Example

Action used:

```
/turtle1/rotate_absolute
```

Function:

Rotate turtle to a specific angle.

---

# 12. Inspect Node Information

### Syntax

```bash
ros2 node info <node_name>
```

Example

```bash
ros2 node info /turtlesim
```

Shows:

* Publishers
* Subscribers
* Services
* Action servers

---

# 13. Listing Actions

### Syntax

```bash
ros2 action list
```

Example

```
/turtle1/rotate_absolute
```

---

# 14. Display Action Types

### Syntax

```bash
ros2 action list -t
```

Example output

```
/turtle1/rotate_absolute [turtlesim/action/RotateAbsolute]
```

---
 ## 14.1 Showing Action Info 

### Syntax

```bash
ros2 action info /turtle1/rotate_absolute
```

Example output

```
Action: /turtle1/rotate_absolute
Action clients: 1
    /teleop_turtle
Action servers: 1
    /turtlesim
```

---
 
# 15. Inspect Action Structure

### Syntax

```bash
ros2 interface show <action_type>
```

Example

```bash
ros2 interface show turtlesim/action/RotateAbsolute
```

Output

```

# The desired heading in radians
float32 theta
---
# The angular displacement in radians to the starting position
float32 delta
---
# The remaining rotation in radians
float32 remaining

```

Explanation:

| Section | Meaning  |
| ------- | -------- |
| First   | Goal     |
| Second  | Result   |
| Third   | Feedback |

---

# 16. Sending an Action Goal

### Syntax

```bash
ros2 action send_goal <action_name> <action_type> <values>
```

Example

```bash
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}"
```

Explanation:

| Parameter   | Meaning            |
| ----------- | ------------------ |
| action_name | Action identifier  |
| action_type | Message definition |
| YAML values | Goal data          |

---

# 17. Viewing Feedback

### Syntax

```bash
ros2 action send_goal ... --feedback
```

Example output

```
Feedback:
remaining: -3.11
```

Meaning:

Remaining angle before rotation finishes.

---

# 18. rqt_console (ROS Logging GUI)

## Definition

`rqt_console` is a **GUI tool to monitor ROS log messages**.

Functions:

* View logs
* Filter messages
* Debug systems

---

# 19. Starting rqt_console

```bash
ros2 run rqt_console rqt_console
```

This opens the logging interface.

---

# 20. Logger Severity Levels

| Level | Meaning                     |
| ----- | --------------------------- |
| Fatal | System will terminate       |
| Error | Serious malfunction         |
| Warn  | Unexpected behavior         |
| Info  | System status               |
| Debug | Detailed internal execution |

Default level:

```
INFO
```

---

# 21. Setting Logger Level

### Syntax

```bash
ros2 run turtlesim turtlesim_node --ros-args --log-level WARN
```

Result:

Only logs:

```
WARN
ERROR
FATAL
```

---

# 22. Launch Files (Node Automation)

## Definition

Launch files start **multiple nodes automatically**.

Instead of:

```
open multiple terminals
run commands manually
```

Use:

```
one launch command
```

---

# 23. Running Launch File

```bash
ros2 launch turtlesim multisim.launch.py
```

---

# 24. Example Launch File

```python
from launch import LaunchDescription
import launch_ros.actions

def generate_launch_description():
    return LaunchDescription([
        launch_ros.actions.Node(
            namespace='turtlesim1',
            package='turtlesim',
            executable='turtlesim_node',
            output='screen'),

        launch_ros.actions.Node(
            namespace='turtlesim2',
            package='turtlesim',
            executable='turtlesim_node',
            output='screen'),
    ])
```

---

# 25. Launch File Syntax Explanation

### Import Libraries

```python
from launch import LaunchDescription
```

Used to define launch configuration.

---

### Node Definition

```python
launch_ros.actions.Node(...)
```

Creates a ROS node.

---

### Parameters

| Field      | Purpose         |
| ---------- | --------------- |
| namespace  | Node group name |
| package    | ROS package     |
| executable | Node program    |
| output     | Display logs    |

---

# 26. Example Control After Launch

Control two turtles independently:

```bash
ros2 topic pub /turtlesim1/turtle1/cmd_vel geometry_msgs/msg/Twist ...
```

---

# 27. ros2 bag (Recording Data)

## Definition

`ros2 bag` records **topic messages into database files**.

Uses:

* Debugging robots
* Replaying experiments
* Dataset collection

---

# 28. Recording Topic Data

### Syntax

```bash
ros2 bag record <topic>
```

Example

```bash
ros2 bag record /turtle1/cmd_vel
```

---

# 29. Recording Multiple Topics

```bash
ros2 bag record -o subset /turtle1/cmd_vel /turtle1/pose
```

Explanation:

| Flag   | Meaning         |
| ------ | --------------- |
| `-o`   | Output filename |
| topics | Data sources    |

---

# 30. Viewing Bag File Info

```bash
ros2 bag info subset
```

Shows:

* duration
* topic count
* message types

---

# 31. Playing Recorded Data

```bash
ros2 bag play subset
```

Result:

Robot repeats recorded motion.

---

# 32. Real Robotics Workflow

```
Robot Sensors
     ↓
ROS Nodes
     ↓
Topics (data streams)
     ↓
Actions (long tasks)
     ↓
Parameters (config)
     ↓
Logging (rqt_console)
     ↓
Launch Files (automation)
```

---

# 33. ROS2 CLI Tools Reference

| CLI Tool    | Function           | Pros                     | Cons                         | Example                                      |
| ----------- | ------------------ | ------------------------ | ---------------------------- | -------------------------------------------- |
| ros2 run    | Run a node         | Simple                   | Single node only             | `ros2 run turtlesim turtlesim_node`          |
| ros2 launch | Run multiple nodes | Automation               | Requires launch file         | `ros2 launch pkg file.launch.py`             |
| ros2 param  | Manage parameters  | Runtime configuration    | Node must support parameters | `ros2 param set /turtlesim background_r 150` |
| ros2 node   | Inspect nodes      | Debugging                | Read-only introspection      | `ros2 node info /turtlesim`                  |
| ros2 topic  | Inspect topics     | Real-time data view      | High output volume           | `ros2 topic echo /turtle1/pose`              |
| ros2 action | Manage actions     | Supports long tasks      | More complex messages        | `ros2 action send_goal`                      |
| ros2 bag    | Record/play data   | Reproducible experiments | Large storage                | `ros2 bag record /topic`                     |
| rqt_console | GUI log monitor    | Easy debugging           | Requires GUI                 | `ros2 run rqt_console rqt_console`           |

---

# 34. Real-Life Robotics Example

Autonomous delivery robot.

System structure:

```
Navigation Node
     ↓
Action: move_to_goal
     ↓
Robot drives to destination
     ↓
Feedback: distance remaining
     ↓
Result: reached target
```

CLI tools usage:

| Tool        | Example                     |
| ----------- | --------------------------- |
| ros2 run    | Start robot navigation node |
| ros2 param  | Adjust speed parameter      |
| ros2 action | Send navigation goal        |
| ros2 bag    | Record sensor data          |
| rqt_console | Monitor errors              |
| ros2 launch | Start entire robot system   |

---

# 35. Quick Revision Summary

* **Parameters** configure node behavior.
* **Actions** manage long-running tasks with feedback.
* **rqt_console** monitors ROS logs visually.
* **Launch files** start multiple nodes automatically.
* **ros2 bag** records topic data for replay.
* **CLI tools** provide debugging and system control.
* **ROS systems combine nodes, topics, services, actions, and parameters** to control robots.

---

```
```
