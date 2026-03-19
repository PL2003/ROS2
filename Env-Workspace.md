# 📘 ROS 2 Environment & Workspace (Exam-Focused README Notes)

---

# 🔰 1. Definitions (Core Concepts)

### 🔹 Environment (ROS 2 Environment)

* A **set of system variables + paths** that allows the terminal to recognize ROS 2 commands.
* Activated using **`source setup.bash`**

📌 Without environment → `ros2` command ❌ not recognized

---

### 🔹 Workspace

* A **project folder** that contains ROS 2 packages.
* Acts as a **development environment for robotics systems**

📁 Typical structure:

```
ros2_ws/
 ├── src/
 ├── build/
 ├── install/
 └── log/
```

---

### 🔹 Underlay vs Overlay

| Term         | Meaning                            |
| ------------ | ---------------------------------- |
| **Underlay** | Base ROS 2 installation            |
| **Overlay**  | Your custom workspace built on top |

📌 Overlay overrides underlay when conflicts occur

---

### 🔹 Package

* Smallest unit of ROS 2 code
* Contains:

  * `package.xml` → metadata
  * `CMakeLists.txt` → build instructions

---

# ⚙️ 2. Environment Setup Process (Step-by-Step)

## 🧩 Step 1: Source ROS 2 (Temporary Setup)

```bash
source /opt/ros/<distro>/setup.bash
```

### 🔍 Syntax Explanation:

* `source` → runs script in current terminal
* `/opt/ros/<distro>/` → ROS installation path
* `setup.bash` → config script

📌 Example:

```bash
source /opt/ros/humble/setup.bash
```

---

## 🧠 What Happens Internally?

* Adds ROS 2 paths to:

  * PATH
  * LD_LIBRARY_PATH
* Enables:

  * `ros2` commands
  * package discovery

---

## 🧪 Step 2: Verify

```bash
ros2 <TAB>
```

✔ If commands appear → environment active

---

## 🔁 Step 3: Problem

New terminal → environment lost ❌

---

## 🔄 Step 4: Permanent Setup (.bashrc)

```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

### 🔍 Syntax:

* `echo` → prints text
* `>>` → appends to file
* `~/.bashrc` → startup script

---

## 🧠 Concept: `.bashrc`

* Runs automatically when terminal opens
* Used to:

  * set environment variables
  * automate commands

---

# 🏗️ 3. Workspace Creation Process

## 🧩 Step 1: Create Workspace

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```

### 🔍 Syntax:

* `mkdir -p` → create nested folders
* `~/` → home directory
* `src` → source folder for packages

---

## 📁 Structure Now:

```
ros2_ws/
 └── src/
```

---

## 🧩 Step 2: Add Source Code

```bash
git clone https://github.com/ros2/examples src/examples -b humble
```

### 🔍 Syntax:

* `git clone` → download repo
* `-b humble` → specific branch

---

## 🧠 Concept:

* Workspace becomes useful only when it contains packages

---

# 🔗 4. Dependency Resolution

```bash
rosdep install -i --from-path src --rosdistro humble -y
```

### 🔍 Syntax:

* `rosdep` → dependency manager
* `--from-path src` → scan packages
* `-y` → auto confirm

---

## 🧠 What Happens:

* Reads `package.xml`
* Installs missing dependencies

---

# 🔨 5. Build Process (colcon)

```bash
colcon build --symlink-install
```

---

## 🔍 Syntax Breakdown:

| Part                | Meaning                                       |
| ------------------- | --------------------------------------------- |
| `colcon build`      | Build all packages                            |
| `--symlink-install` | Faster updates (no rebuild needed for Python) |

---

## 🧠 What Happens Internally:

1. Read packages in `src/`
2. Compile code
3. Create:

   * `build/` → intermediate files
   * `install/` → final usable files
   * `log/` → debug logs

---

## 📁 After Build:

```
ros2_ws/
 ├── build/
 ├── install/
 ├── log/
 └── src/
```

---

# 🔄 6. Source the Workspace (Overlay)

```bash
source install/setup.bash
```

---

## 🔍 Syntax:

* Loads workspace into environment
* Makes packages executable

---

## ⚠️ Important Rule:

✔ Always open **new terminal before sourcing overlay**

---

## 🧠 Key Insight:

```
Underlay → Base system
Overlay → Your custom packages
```

---

# ▶️ 7. Running Nodes (Execution)

```bash
ros2 run <package> <executable>
```

📌 Example:

```bash
ros2 run examples_rclcpp_minimal_publisher publisher_member_function
```

---

## 🔍 Syntax:

* `ros2 run` → execute node
* `<package>` → package name
* `<executable>` → compiled program

---

# 🧪 8. Testing Workspace

```bash
colcon test
```

* Runs package tests automatically

---

# 📦 9. Creating a Package

## 🧩 Command:

```bash
ros2 pkg create --build-type ament_cmake --node-name my_node my_package
```

---

## 🔍 Syntax Breakdown:

| Part              | Meaning          |
| ----------------- | ---------------- |
| `ros2 pkg create` | Create package   |
| `--build-type`    | CMake or Python  |
| `--node-name`     | auto-create node |
| `my_package`      | package name     |

---

## 📁 Generated Structure:

```
my_package/
 ├── CMakeLists.txt
 ├── package.xml
 ├── src/
 └── include/
```

---

# 🧠 10. Key Principles

### ⚙️ 1. Sourcing Principle

* Required to **activate environment**
* Must be done before running ROS 2 commands

---

### 🔄 2. Overlay Principle

* Overlay **overrides underlay**
* Allows safe experimentation

---

### 📦 3. Modularity

* System = collection of packages
* Each package = single responsibility

---

### 🔁 4. Build Isolation

* `colcon` builds packages separately
* Avoids conflicts

---

# 📊 11. Comparison Table

| Feature     | Environment         | Workspace              |
| ----------- | ------------------- | ---------------------- |
| Purpose     | Enable ROS commands | Develop projects       |
| Scope       | System-level        | Project-level          |
| Setup       | `source setup.bash` | `mkdir + colcon build` |
| Persistence | Temporary / .bashrc | Permanent folder       |
| Example     | `/opt/ros/humble`   | `~/ros2_ws`            |

---

# 🧠 12. Process Flow (Memory Hook)

```
Install ROS → Source Environment → Create Workspace → Add Code → Resolve Dependencies → Build → Source Overlay → Run Nodes
```

---

# 🧩 13. Real-Life Analogy

| Concept     | Real-Life Example  |
| ----------- | ------------------ |
| Environment | OS settings / PATH |
| Workspace   | Project folder     |
| Package     | App/module         |
| Underlay    | Operating system   |
| Overlay     | Installed apps     |

---

# 📌 14. Summary (Quick Revision)

* ROS 2 requires **environment sourcing** to work
* Workspace = **project container**
* `src/` holds packages
* `colcon build` compiles everything
* `install/` contains usable outputs
* Overlay overrides base system
* `.bashrc` automates environment setup
* Packages are modular and reusable
* `rosdep` handles dependencies
* `ros2 run` executes nodes

---

# 🎯 Final Memory Trick

👉 **“S C A R B S R”**

* **S**ource environment
* **C**reate workspace
* **A**dd packages
* **R**esolve dependencies
* **B**uild
* **S**ource overlay
* **R**un nodes

---

If needed, a diagram-based visualization or flowchart version can be created for faster revision.
