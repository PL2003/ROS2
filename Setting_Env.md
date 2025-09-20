# ROS 2 — Configuring the Environment

This repository provides two files:

1. **README.md** → Contains explanations, child-friendly stories, syntax breakdowns, and process notes.
2. **commands.md** → Contains only the raw commands (cheat-sheet style).

---

## README.md (Detailed Explanations with Stories, Syntax, and Process)

### Source setup files

```bash
source /opt/ros/humble/setup.bash
```

*Story:* Imagine you just woke up your robot, but it doesn’t know where its toys are. By “sourcing” the setup file, you are handing the robot a treasure map that shows where all the ROS 2 tools and toys are kept.

*Process:* This command loads the ROS 2 environment variables into the current shell, enabling the `ros2` command-line tools and package discovery.

*Syntax Explanation:*

* `source` → reads and executes commands from the file in the current shell.
* `/opt/ros/humble/setup.bash` → the path to ROS 2 Humble’s setup script.

---

### Add sourcing to shell startup

```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

*Story:* Instead of giving the treasure map to your robot every single time, you glue a copy on the toy box lid. Now, whenever the box is opened, the map is already there.

*Process:* Appends the sourcing command to your `~/.bashrc`, so every new terminal session automatically configures ROS 2.

*Syntax Explanation:*

* `echo "..." >> file` → appends text to a file.
* `~/.bashrc` → shell startup file executed when a new terminal session begins.

---

### Check environment variables

```bash
printenv | grep -i ROS
echo "$ROS_DISTRO"
echo "$ROS_VERSION"
```

*Story:* This is like checking your robot’s battery gauge to confirm it really got the treasure map. If the gauges say “ROS 2, Humble,” you know everything is charged and ready.

*Process:* Verifies ROS-related environment variables are set correctly, ensuring the setup file worked.

*Syntax Explanation:*

* `printenv` → prints all environment variables.
* `| grep -i ROS` → filters output for variables containing “ROS” (case-insensitive).
* `echo "$VAR"` → prints the value of a variable.

---

### Set ROS Domain ID

```bash
export ROS_DOMAIN_ID=42
echo 'export ROS_DOMAIN_ID=42' >> ~/.bashrc
```

*Story:* Think of this like tuning your walkie-talkie to channel 42. Only robots on the same channel can hear each other, so your messages don’t get mixed with the neighbor’s robots.

*Process:* Defines a unique DDS (Data Distribution Service) domain ID for communication. Persisting it ensures consistency across sessions.

*Syntax Explanation:*

* `export VAR=value` → sets an environment variable for the current session.
* `echo '...' >> ~/.bashrc` → makes the change permanent.

---

### Set ROS Localhost Only

```bash
export ROS_LOCALHOST_ONLY=1
echo 'export ROS_LOCALHOST_ONLY=1' >> ~/.bashrc
```

*Story:* This is like closing the windows so your robot can only talk to toys inside your room, not to the whole neighborhood.

*Process:* Restricts ROS 2 communication to your own computer (127.0.0.1), preventing network broadcasting.

*Syntax Explanation:*

* `ROS_LOCALHOST_ONLY=1` → enables localhost-only communication.

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

*Process:* The base workspace (ROS 2 installation) must be sourced first, then your custom workspace (overlay). The overlay takes priority for package discovery.

*Syntax Explanation:*

* `colcon build` → compiles your packages.
* `--symlink-install` → allows editing source files without reinstalling.
* `source ...` → activates underlay then overlay.

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

*Process:* Initializes a local Git repo, stages the files, commits them, and pushes them to a GitHub repository.

*Syntax Explanation:*

* `git init` → initializes an empty repository.
* `git add` → stages files for commit.
* `git commit -m` → saves changes with a message.
* `git branch -M main` → sets default branch as main.
* `git remote add origin URL` → links to remote GitHub repo.
* `git push -u origin main` → uploads code to GitHub.

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
