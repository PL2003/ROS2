# ROS2

# ROS 2 — Configuring the Environment

This repository provides two files:

1. **README.md** → Contains explanations, child-friendly stories, and formal notes for learning and reference.
2. **commands.md** → Contains only the raw commands (cheat-sheet style).

---

## README.md (Detailed Explanations)

### Source setup files

```bash
source /opt/ros/humble/setup.bash
```

* Loads ROS 2 environment into the shell.

### Add sourcing to shell startup

```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

* Persists sourcing across terminal sessions.

### Check environment variables

```bash
printenv | grep -i ROS
echo "$ROS_DISTRO"
echo "$ROS_VERSION"
```

* Confirms ROS variables are set.

### Set ROS Domain ID

```bash
export ROS_DOMAIN_ID=42
echo 'export ROS_DOMAIN_ID=42' >> ~/.bashrc
```

* Isolates ROS 2 communication groups.

### Set ROS Localhost Only

```bash
export ROS_LOCALHOST_ONLY=1
echo 'export ROS_LOCALHOST_ONLY=1' >> ~/.bashrc
```

* Restricts ROS 2 communication to localhost.

### Workspaces (underlay + overlay)

```bash
# Build overlay workspace
colcon build --symlink-install

# Source underlay first
source /opt/ros/humble/setup.bash

# Then source overlay
source ~/ros2_ws/install/setup.bash
```

* Layers workspaces in correct order.

### Save notes to GitHub

```bash
git init
git add README.md commands.md
git commit -m "Add ROS 2 environment setup notes"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
`
