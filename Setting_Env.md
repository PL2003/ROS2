# ROS 2 — Configuring the Environment

This repository provides two files:

1. **README.md** → Contains explanations, child-friendly stories, and formal notes for learning and reference.
2. **commands.md** → Contains only the raw commands (cheat-sheet style).

---

## README.md (Detailed Explanations with Stories)

### Source setup files

```bash
source /opt/ros/humble/setup.bash
```

*Story:* Imagine you just woke up your robot, but it doesn’t know where its toys are. By “sourcing” the setup file, you are handing the robot a treasure map that shows where all the ROS 2 tools and toys are kept.

*Formal:* Loads the ROS 2 environment into the current shell, enabling `ros2` commands and package discovery.

---

### Add sourcing to shell startup

```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

*Story:* Instead of giving the treasure map to your robot every single time, you glue a copy on the toy box lid. Now, whenever the box is opened, the map is already there.

*Formal:* Appends the `source` command to your shell startup script so the ROS 2 environment is automatically set up in every new terminal session.

---

### Check environment variables

```bash
printenv | grep -i ROS
echo "$ROS_DISTRO"
echo "$ROS_VERSION"
```

*Story:* This is like checking your robot’s battery gauge to confirm it really got the treasure map. If the gauges say “ROS 2, Humble,” you know everything is charged and ready.

*Formal:* Confirms ROS environment variables (e.g., `ROS_DISTRO`, `ROS_VERSION`) are properly set.

---

### Set ROS Domain ID

```bash
export ROS_DOMAIN_ID=42
echo 'export ROS_DOMAIN_ID=42' >> ~/.bashrc
```

*Story:* Think of this like tuning your walkie-talkie to channel 42. Only robots on the same channel can hear each other, so your messages don’t get mixed with the neighbor’s robots.

*Formal:* Assigns a DDS domain ID so ROS 2 systems on the same network don’t interfere with each other. Persisting ensures the same ID is used every session.

---

### Set ROS Localhost Only

```bash
export ROS_LOCALHOST_ONLY=1
echo 'export ROS_LOCALHOST_ONLY=1' >> ~/.bashrc
```

*Story:* This is like closing the windows so your robot can only talk to toys inside your room, not to the whole neighborhood.

*Formal:* Restricts ROS 2 communication to localhost (127.0.0.1), preventing traffic from being shared across the network.

---

### Workspaces (underlay + overlay)

```bash
# Build overlay workspace
colcon build --symlink-install

# Source underlay first
source /opt/ros/humble/setup.bash

# Then source overlay
source ~/ros2_ws/install/setup.bash
```

*Story:* Imagine baking a cake. The base cake (underlay) is store-bought, and your frosting and decorations (overlay) go on top. You must put the base first before adding the frosting.

*Formal:* Ensures the base ROS 2 installation (underlay) is sourced first, then local workspaces (overlays). Overlays take precedence in package discovery.

---

### Save notes to GitHub

```bash
git init
git add README.md commands.md
git commit -m "Add ROS 2 environment setup notes"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

*Story:* Think of GitHub as a magical notebook where your robot’s instructions are saved forever. By writing them there, you can always come back later or share them with friends.

*Formal:* Initializes a Git repo, stages the files, commits them, and pushes to a remote repository on GitHub.

---

## commands.md (Only Commands)

```bash
# Source setup file
source /opt/ros/humble/setup.bash

# Persist sourcing
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc

# Check environment variables
printenv | grep -i ROS
echo "$ROS_DISTRO"
echo "$ROS_VERSION"

# Set ROS Domain ID
export ROS_DOMAIN_ID=42
echo 'export ROS_DOMAIN_ID=42' >> ~/.bashrc

# Set ROS Localhost Only
export ROS_LOCALHOST_ONLY=1
echo 'export ROS_LOCALHOST_ONLY=1' >> ~/.bashrc

# Workspaces
colcon build --symlink-install
source /opt/ros/humble/setup.bash
source ~/ros2_ws/install/setup.bash

# GitHub setup
git init
git add README.md commands.md
git commit -m "Add ROS 2 environment setup notes"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```
