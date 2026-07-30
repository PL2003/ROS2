# ROS 2 Foxy Beginner CLI Tools — Complete Guide

> A comprehensive, hands-on reference for learning ROS 2 Foxy command-line tools using `turtlesim`, `rqt`, and the core ROS 2 concepts.

---

## Table of Contents

- [Prerequisites & Dependencies](#prerequisites--dependencies)
- [Configuring the Environment](#configuring-the-environment)
- [Using turtlesim, ros2, and rqt](#using-turtlesim-ros2-and-rqt)
- [Understanding Nodes](#understanding-nodes)
- [Understanding Topics](#understanding-topics)
- [Understanding Services](#understanding-services)
- [Understanding Parameters](#understanding-parameters)
- [Understanding Actions](#understanding-actions)
- [Using rqt_console to View Logs](#using-rqt_console-to-view-logs)
- [Launching Nodes](#launching-nodes)
- [Recording and Playing Back Data](#recording-and-playing-back-data)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)

---

## Prerequisites & Dependencies

### System Requirements
- **OS:** Ubuntu 20.04 (Focal Fossa)
- **ROS 2 Distro:** Foxy Fitzroy

### Required Packages

```bash
# Update apt
sudo apt update && sudo apt upgrade -y

# Install ROS 2 Foxy desktop (includes tools, rqt, rviz2)
sudo apt install ros-foxy-desktop

# Install turtlesim package
sudo apt install ros-foxy-turtlesim

# Install ros2bag and its storage/transport plugins
sudo apt install ros-foxy-ros2bag ros-foxy-rosbag2-storage-default-plugins

# Build dependencies (if building from source)
sudo apt install python3-colcon-common-extensions python3-rosdep python3-vcstool

# Initialize rosdep (first time only)
sudo rosdep init
rosdep update
```

### Verify Installation

```bash
# Check ROS 2 version
ros2 --version

# List available ROS 2 packages
ros2 pkg list | grep turtlesim
```

---

## Configuring the Environment

Every new terminal session must source the ROS 2 setup file to access commands and packages.

### Manual Sourcing (Per Terminal)

```bash
# Source the main ROS 2 installation
source /opt/ros/foxy/setup.bash

# Source your local workspace (if you have one)
source ~/ros2_ws/install/setup.bash
```

### Automatic Sourcing (Recommended)

Add the source command to your shell startup file so it runs automatically:

```bash
# For Bash users
echo "source /opt/ros/foxy/setup.bash" >> ~/.bashrc
source ~/.bashrc

# For Zsh users
echo "source /opt/ros/foxy/setup.zsh" >> ~/.zshrc
source ~/.zshrc
```

### Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `ROS_DISTRO` | Active ROS distribution | `foxy` |
| `ROS_VERSION` | ROS version number | `2` |
| `AMENT_PREFIX_PATH` | Paths to ament packages | `/opt/ros/foxy` |
| `PYTHONPATH` | Python module search paths | includes `/opt/ros/foxy/lib/python3.8/site-packages` |

```bash
# Inspect environment variables
printenv | grep -i ROS
```

### Domain ID (Multi-Machine / Isolation)

```bash
# Set a domain ID (0–101, default is 0)
export ROS_DOMAIN_ID=42

# Add to ~/.bashrc for persistence
echo "export ROS_DOMAIN_ID=42" >> ~/.bashrc
```

> **Note:** Nodes on different domain IDs cannot discover each other. Use this to isolate experiments or separate robots.

---

## Using turtlesim, ros2, and rqt

### 1. Start the Turtlesim Simulator

`turtlesim` is a lightweight simulator for learning ROS 2. It provides a window where a turtle moves based on velocity commands.

```bash
# Terminal 1: Launch turtlesim node
ros2 run turtlesim turtlesim_node
```

**What happens:**
- A window opens showing a turtle in the center.
- The node registers itself as `/turtlesim`.
- It publishes the turtle's pose on a topic.
- It subscribes to velocity commands.

### 2. Control the Turtle with Keyboard

```bash
# Terminal 2: Run the teleop node
ros2 run turtlesim turtle_teleop_key
```

| Key | Action |
|-----|--------|
| `↑` (Arrow Up) | Move forward |
| `↓` (Arrow Down) | Move backward |
| `←` (Arrow Left) | Turn left |
| `→` (Arrow Right) | Turn right |
| `G` `B` `V` `C` `D` `E` `R` `T` | Move to specific orientations |
| `Q` | Increase linear speed |
| `Z` | Decrease linear speed |
| `W` | Increase angular speed |
| `X` | Decrease angular speed |

> **Focus Requirement:** The `turtle_teleop_key` terminal must be the active window for keystrokes to register.

### 3. Using rqt

`rqt` is a Qt-based GUI framework for ROS 2. It provides plugins for visualizing the system.

```bash
# Launch the main rqt GUI
rqt

# Or launch specific plugins directly
ros2 run rqt_graph rqt_graph          # Node/topic graph
ros2 run rqt_console rqt_console      # Log viewer
ros2 run rqt_reconfigure rqt_reconfigure  # Dynamic parameter reconfiguration
```

**Common rqt Plugins:**

| Plugin | Purpose | Launch Command |
|--------|---------|----------------|
| **Node Graph** | Visualize nodes & topics | `rqt_graph` or `ros2 run rqt_graph rqt_graph` |
| **Console** | View log messages | `rqt_console` |
| **Plot** | Plot topic data over time | `ros2 run rqt_plot rqt_plot` |
| **Reconfigure** | Change parameters at runtime | `rqt_reconfigure` |
| **Topic Monitor** | Inspect topic values live | `ros2 run rqt_topic rqt_topic` |

---

## Understanding Nodes

A **node** is a fundamental ROS 2 computation unit. Each node is responsible for a single, modular purpose (e.g., controlling wheel motors, processing lidar data).

### List Active Nodes

```bash
ros2 node list
```

**Example output:**
```
/turtlesim
/teleop_turtle
```

### Inspect a Node

```bash
# View node info (subscribers, publishers, services, actions)
ros2 node info /turtlesim
```

**Sample output:**
```
/turtlesim
  Subscribers:
    /turtle1/cmd_vel: geometry_msgs/msg/Twist
  Publishers:
    /turtle1/color_sensor: turtlesim/msg/Color
    /turtle1/pose: turtlesim/msg/Pose
    /rosout: rcl_interfaces/msg/Log
  Service Servers:
    /clear: std_srvs/srv/Empty
    /kill: turtlesim/srv/Kill
    /reset: std_srvs/srv/Empty
    /spawn: turtlesim/srv/Spawn
    /turtle1/set_pen: turtlesim/srv/SetPen
    /turtle1/teleport_absolute: turtlesim/srv/TeleportAbsolute
    /turtle1/teleport_relative: turtlesim/srv/TeleportRelative
  Service Clients:

  Action Servers:
    /turtle1/rotate_absolute: turtlesim/action/RotateAbsolute
  Action Clients:
```

### Remapping Node Names

You can run multiple instances of the same node by remapping its name:

```bash
# Run a second turtlesim with a different node name and namespace
ros2 run turtlesim turtlesim_node --ros-args --remap __node:=turtlesim2 --remap __ns:=/ns2
```

---

## Understanding Topics

**Topics** are named buses over which nodes exchange messages. They implement a publish/subscribe communication pattern.

### List Topics

```bash
# List all active topics
ros2 topic list

# List topics with their message types
ros2 topic list -t
```

**Example output:**
```
/parameter_events [rcl_interfaces/msg/ParameterEvent]
/rosout [rcl_interfaces/msg/Log]
/turtle1/cmd_vel [geometry_msgs/msg/Twist]
/turtle1/color_sensor [turtlesim/msg/Color]
/turtle1/pose [turtlesim/msg/Pose]
```

### Echo Topic Data

```bash
# View live data published on a topic
ros2 topic echo /turtle1/pose

# Echo with once-only (useful in scripts)
ros2 topic echo /turtle1/pose --once
```

### Publish to a Topic

```bash
# Publish a velocity command (Twist message) from the CLI
ros2 topic pub /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"

# Publish once
ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"

# Publish at a rate (e.g., 1 Hz)
ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```

### Topic Info & Bandwidth

```bash
# Detailed info about a topic
ros2 topic info /turtle1/pose

# Measure publication rate
ros2 topic hz /turtle1/pose

# Measure bandwidth usage
ros2 topic bw /turtle1/pose
```

### Find Topics by Type

```bash
# Find all topics using a specific message type
ros2 topic find geometry_msgs/msg/Twist
```

### Topic Graph Visualization

```bash
# Open rqt_graph to see the publisher/subscriber relationships
rqt_graph
```

---

## Understanding Services

**Services** implement a request/response model. A client node sends a request to a service server, which processes it and returns a response.

### List Services

```bash
# List all available services
ros2 service list

# List services with their types
ros2 service list -t
```

**Example output:**
```
/clear [std_srvs/srv/Empty]
/kill [turtlesim/srv/Kill]
/reset [std_srvs/srv/Empty]
/spawn [turtlesim/srv/Spawn]
/turtle1/set_pen [turtlesim/srv/SetPen]
/turtle1/teleport_absolute [turtlesim/srv/TeleportAbsolute]
/turtle1/teleport_relative [turtlesim/srv/TeleportRelative]
```

### Inspect Service Types

```bash
# Show the service type definition (request/response structure)
ros2 service type /spawn
ros2 interface show turtlesim/srv/Spawn
```

**Sample `turtlesim/srv/Spawn` definition:**
```
float32 x
float32 y
float32 theta
string name # Optional.  A unique name will be created and returned if this is empty
---
string name
```

> The `---` separates the **request** (above) from the **response** (below).

### Call a Service

```bash
# Clear the turtlesim background trails
ros2 service call /clear std_srvs/srv/Empty

# Spawn a new turtle at position (2, 2) facing 0.5 radians
ros2 service call /spawn turtlesim/srv/Spawn "{x: 2.0, y: 2.0, theta: 0.5, name: 'turtle2'}"

# Set pen color (red=255, green=0, blue=0, width=5, off=0)
ros2 service call /turtle1/set_pen turtlesim/srv/SetPen "{r: 255, g: 0, b: 0, width: 5, 'off': 0}"

# Teleport turtle to absolute position
ros2 service call /turtle1/teleport_absolute turtlesim/srv/TeleportAbsolute "{x: 5.5, y: 5.5, theta: 0.0}"

# Kill a turtle by name
ros2 service call /kill turtlesim/srv/Kill "{name: 'turtle2'}"
```

### Find Services by Type

```bash
ros2 service find std_srvs/srv/Empty
```

---

## Understanding Parameters

**Parameters** are configuration values associated with a node. They can be integers, floats, booleans, strings, or lists.

### List Parameters

```bash
# List all parameters for all nodes
ros2 param list

# List parameters for a specific node
ros2 param list /turtlesim
```

**Example output for `/turtlesim`:**
```
  background_b
  background_g
  background_r
  use_sim_time
```

### Get Parameter Values

```bash
# Get a single parameter
ros2 param get /turtlesim background_r

# Get all parameters for a node
ros2 param dump /turtlesim
```

### Set Parameters

```bash
# Change the background color to blue (R=0, G=0, B=255)
ros2 param set /turtlesim background_r 0
ros2 param set /turtlesim background_g 0
ros2 param set /turtlesim background_b 255

# Set use_sim_time
ros2 param set /turtlesim use_sim_time true
```

### Save & Load Parameters

```bash
# Dump parameters to a YAML file
ros2 param dump /turtlesim > turtlesim_params.yaml

# Load parameters from a YAML file
ros2 param load /turtlesim turtlesim_params.yaml
```

**Sample `turtlesim_params.yaml`:**
```yaml
turtlesim:
  ros__parameters:
    background_b: 255
    background_g: 86
    background_r: 69
    use_sim_time: false
```

### Start a Node with Parameters

```bash
# Launch turtlesim with custom background color via YAML
ros2 run turtlesim turtlesim_node --ros-args --params-file turtlesim_params.yaml
```

---

## Understanding Actions

**Actions** are the preferred pattern for long-running tasks. They consist of:
- **Goal:** Sent by the client to request a task.
- **Feedback:** Periodic updates from the server during execution.
- **Result:** Final outcome sent when the task completes (or fails).

### List Actions

```bash
ros2 action list
ros2 action list -t
```

**Example output:**
```
/turtle1/rotate_absolute [turtlesim/action/RotateAbsolute]
```

### Inspect Action Types

```bash
# Show the action type
ros2 action type /turtle1/rotate_absolute

# Show the full action definition (goal, feedback, result)
ros2 interface show turtlesim/action/RotateAbsolute
```

**Definition:**
```
# Goal
float32 theta
---
# Result
float32 delta
---
# Feedback
float32 remaining
```

### Send an Action Goal

```bash
# Send a goal to rotate the turtle to 1.57 radians (90 degrees)
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}"

# Send a goal and stream feedback
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: -1.57}" --feedback
```

**Output with feedback:**
```
Goal accepted with ID: 12345678-1234-1234-1234-123456789abc

Feedback:
    Remaining: 1.52

Feedback:
    Remaining: 1.25

...

Result:
    Delta: 1.5700000524520874

Goal finished with status: SUCCEEDED
```

### Action States

| State | Description |
|-------|-------------|
| `ACCEPTED` | Goal was accepted by the server |
| `EXECUTING` | Goal is currently being processed |
| `CANCELING` | Cancellation was requested |
| `SUCCEEDED` | Goal completed successfully |
| `CANCELED` | Goal was canceled before completion |
| `ABORTED` | Goal was aborted by the server |

### Cancel an Action

```bash
# List active action goals
ros2 action info /turtle1/rotate_absolute

# (Cancellation is typically done programmatically, but can be simulated via service calls to the action internals)
```

---

## Using rqt_console to View Logs

ROS 2 uses a centralized logging system. Nodes publish log messages to the `/rosout` topic, which can be viewed in `rqt_console`.

### Launch rqt_console

```bash
# Method 1: Standalone plugin
ros2 run rqt_console rqt_console

# Method 2: From within rqt
rqt
# Then select Plugins → Logging → Console
```

### Log Severity Levels

| Level | Numeric Value | Color in rqt_console | Typical Use |
|-------|---------------|----------------------|-------------|
| `DEBUG` | 10 | Gray | Detailed diagnostic info |
| `INFO` | 20 | Green | General information |
| `WARN` | 30 | Yellow | Potential issues |
| `ERROR` | 40 | Red | Recoverable failures |
| `FATAL` | 50 | Dark Red / Purple | Critical, unrecoverable errors |

### Generate Logs via CLI

```bash
# Publish a log message directly to /rosout (for testing)
ros2 topic pub /rosout rcl_interfaces/msg/Log "{level: 20, name: 'test_node', msg: 'Hello from CLI', stamp: {sec: 0, nanosec: 0}}"
```

### Filter Logs in rqt_console

1. **Severity Filter:** Click the severity icons to show/hide levels.
2. **Message Filter:** Use the text box to filter by substring.
3. **Node Filter:** Filter messages by originating node name.
4. **Time Range:** Filter by timestamp.

### Save & Load Log Configurations

```bash
# Save the current rqt_console perspective (filters, layout)
# Use the rqt GUI: Perspectives → Save
```

### View Logs via CLI

```bash
# Echo the /rosout topic directly
ros2 topic echo /rosout

# Use grep to filter for errors
ros2 topic echo /rosout | grep -i error
```

### Log File Location

Logs are also written to disk:

```bash
# Default log directory
~/.ros/log/

# Find the latest session log
ls -lt ~/.ros/log/ | head
```

---

## Launching Nodes

**Launch files** allow you to start multiple nodes with specific configurations in a single command.

### Using `ros2 launch`

```bash
# Launch a package's launch file
ros2 launch turtlesim multisim.launch.py

# Launch with arguments
ros2 launch my_package my_launch.py arg_name:=value
```

### Anatomy of a Python Launch File

Create `launch_example.py`:

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        # Node 1: Turtlesim simulator
        Node(
            package='turtlesim',
            executable='turtlesim_node',
            name='sim',
            parameters=[{'background_r': 255, 'background_g': 255, 'background_b': 0}]
        ),
        # Node 2: Teleop keyboard controller
        Node(
            package='turtlesim',
            executable='turtle_teleop_key',
            name='teleop',
            prefix='xterm -e'  # Open in a separate terminal window
        ),
    ])
```

Run it:

```bash
ros2 launch my_package launch_example.py
```

### Launch File Locations

| Location | Purpose |
|----------|---------|
| `/opt/ros/foxy/share/<package>/launch/` | Installed package launch files |
| `~/ros2_ws/src/<package>/launch/` | Your custom workspace launch files |

### Inspecting Launch Files

```bash
# Print the launch description without executing (dry-run)
ros2 launch --print-description my_package my_launch.py

# Show launch arguments
ros2 launch my_package my_launch.py --show-args
```

### Common Launch Patterns

```python
# Include another launch file
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.substitutions import PathJoinSubstitution

IncludeLaunchDescription(
    PythonLaunchDescriptionSource([
        PathJoinSubstitution([
            FindPackageShare('other_package'),
            'launch',
            'other_launch.py'
        ])
    ])
)
```

---

## Recording and Playing Back Data

`ros2 bag` is the ROS 2 tool for recording topic data to files and playing it back later. This is essential for debugging, testing algorithms offline, and dataset creation.

### Recording Data

```bash
# Record a single topic
ros2 bag record /turtle1/cmd_vel

# Record multiple topics
ros2 bag record /turtle1/cmd_vel /turtle1/pose

# Record ALL topics (use with caution — generates large files)
ros2 bag record -a

# Record with a custom output directory name
ros2 bag record -o my_session /turtle1/cmd_vel /turtle1/pose

# Record with compression (SQLite3 storage)
ros2 bag record --compression-mode file --compression-format zstd /turtle1/cmd_vel
```

**While recording:**
- Press `Ctrl+C` to stop.
- A folder named `<timestamp>` or your custom `-o` name is created.

### Inspecting Recorded Bags

```bash
# Info about a bag file
ros2 bag info my_session/

# Example output:
# Files:             my_session.db3
# Bag size:          45.5 KiB
# Storage id:        sqlite3
# Duration:          12.3s
# Start:             Jul 30 2026 12:00:00.000
# End:               Jul 30 2026 12:00:12.300
# Messages:          246
# Topic information: Topic: /turtle1/cmd_vel | Type: geometry_msgs/msg/Twist | Count: 123 | Serialization Format: cdr
#                    Topic: /turtle1/pose | Type: turtlesim/msg/Pose | Count: 123 | Serialization Format: cdr
```

### Playing Back Data

```bash
# Replay all recorded topics
ros2 bag play my_session/

# Replay at half speed
ros2 bag play my_session/ -r 0.5

# Replay at double speed
ros2 bag play my_session/ -r 2.0

# Replay in a loop
ros2 bag play my_session/ -l

# Replay starting from a specific offset
ros2 bag play my_session/ -s 5  # Skip first 5 seconds
```

### Recording Best Practices

| Practice | Command / Note |
|----------|----------------|
| Exclude image topics unless needed | They create huge files |
| Use compression | `--compression-mode file --compression-format zstd` |
| Name bags descriptively | `-o experiment_1_trial_3` |
| Verify with `ros2 bag info` | Before sharing or processing |
| Synchronize clocks | Use `use_sim_time:=true` when playing back |

### Bag File Storage Formats

| Format | Extension | Description |
|--------|-----------|-------------|
| **SQLite3** | `.db3` | Default, human-inspectable with `sqlite3` CLI |
| **MCAP** | `.mcap` | High-performance, preferred for large datasets |

```bash
# Convert between storage formats
ros2 bag convert -i my_session/ -o converted/ --storage mcap
```

### Integration with `use_sim_time`

When playing back a bag, you often want nodes to use the recorded timestamps instead of wall-clock time:

```bash
# Terminal 1: Start a clock publisher (built into ros2 bag play when -r is used, or manually)
ros2 bag play my_session/ --clock 100  # Publish /clock at 100 Hz

# Terminal 2: Launch your node with sim time enabled
ros2 run my_package my_node --ros-args -p use_sim_time:=true
```

---

## Quick Reference Cheat Sheet

### Environment

```bash
source /opt/ros/foxy/setup.bash
source ~/.bashrc
export ROS_DOMAIN_ID=0
```

### Nodes

```bash
ros2 node list
ros2 node info /node_name
ros2 run package_name executable_name
```

### Topics

```bash
ros2 topic list [-t]
ros2 topic echo /topic_name
ros2 topic pub /topic_name msg_type "{data}"
ros2 topic hz /topic_name
ros2 topic bw /topic_name
ros2 topic info /topic_name
```

### Services

```bash
ros2 service list [-t]
ros2 service type /service_name
ros2 service find msg_type
ros2 service call /service_name srv_type "{request}"
ros2 interface show srv_type
```

### Parameters

```bash
ros2 param list [/node_name]
ros2 param get /node_name param_name
ros2 param set /node_name param_name value
ros2 param dump /node_name
ros2 param load /node_name file.yaml
```

### Actions

```bash
ros2 action list [-t]
ros2 action info /action_name
ros2 action send_goal /action_name action_type "{goal}" [--feedback]
ros2 interface show action_type
```

### Bag

```bash
ros2 bag record [-a] [-o name] /topic1 /topic2
ros2 bag play bag_name/ [-r rate] [-l] [-s offset]
ros2 bag info bag_name/
```

### rqt Plugins

```bash
rqt
rqt_graph
rqt_console
rqt_reconfigure
ros2 run rqt_plot rqt_plot
ros2 run rqt_topic rqt_topic
```

---

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| `command not found: ros2` | Setup file not sourced | `source /opt/ros/foxy/setup.bash` |
| `Package not found` | Package not built or sourced | `colcon build` then `source install/setup.bash` |
| Nodes don't see each other | Different `ROS_DOMAIN_ID` | Check `echo $ROS_DOMAIN_ID` on all terminals |
| `turtlesim` window doesn't open | Missing display / SSH without X11 | Use `export DISPLAY=:0` or VNC |
| Bag file won't play | Wrong CWD or path | Use absolute path: `ros2 bag play /absolute/path/to/bag/` |
| Permission denied on `/dev/*` | User not in `dialout` group | `sudo usermod -a -G dialout $USER` then re-login |

---

## Additional Resources

- [ROS 2 Foxy Documentation](https://docs.ros.org/en/foxy/)
- [ROS 2 Tutorials Index](https://docs.ros.org/en/foxy/Tutorials.html)
- [turtlesim Package Docs](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Introducing-Turtlesim/Introducing-Turtlesim.html)
- [Understanding ROS 2 Nodes](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Nodes/Understanding-ROS2-Nodes.html)
- [Understanding ROS 2 Topics](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Topics/Understanding-ROS2-Topics.html)
- [Understanding ROS 2 Services](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Services/Understanding-ROS2-Services.html)
- [Understanding ROS 2 Parameters](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Parameters/Understanding-ROS2-Parameters.html)
- [Understanding ROS 2 Actions](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Actions/Understanding-ROS2-Actions.html)
- [Using rqt_console](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Using-Rqt-Console/Using-Rqt-Console.html)
- [Launching Nodes](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Launching-Multiple-Nodes/Launching-Multiple-Nodes.html)
- [Recording and Playing Back Data](https://docs.ros.org/en/foxy/Tutorials/Beginner-CLI-Tools/Recording-And-Playing-Back-Data/Recording-And-Playing-Back-Data.html)

---

*Last updated: July 2026 | Target ROS 2 Distro: Foxy Fitzroy*
