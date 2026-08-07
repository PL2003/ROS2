Give me updated line with line numbers as i cannot replace entire file

The error is from the GitHub API download on **original lines 25–32**. Replace **lines 24–34** of your existing file with the following block.

ROS officially supports installing Humble through the Ubuntu package repository on Ubuntu 22.04.[1]

## 1. Back up the file

```bash
cp setup_ros_humble_sim.sh setup_ros_humble_sim.sh.backup
```

## 2. Open the file with line numbers

```bash
nano -c setup_ros_humble_sim.sh
```

In `nano`, go to line 24 with:

```text
Ctrl + _
```

Enter:

```text
24
```

Delete lines **24 through 34**, then insert this replacement:

```bash
# Configure the ROS repository directly without the GitHub release API.
KEYRING=/usr/share/keyrings/ros-archive-keyring.gpg

if [[ ! -s "$KEYRING" ]]; then
  curl -fsSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc \
    | sudo gpg --dearmor --yes -o "$KEYRING"
fi

CODENAME="${UBUNTU_CODENAME:-${VERSION_CODENAME}}"
ARCH="$(dpkg --print-architecture)"

echo "deb [arch=${ARCH} signed-by=${KEYRING}] http://packages.ros.org/ros2/ubuntu ${CODENAME} main" \
  | sudo tee /etc/apt/sources.list.d/ros2.list >/dev/null

sudo apt-get update
```

Save and exit:

```text
Ctrl + O
Enter
Ctrl + X
```

## 3. Check the edited section

Run:

```bash
nl -ba setup_ros_humble_sim.sh | sed -n '20,45p'
```

The output should show the new repository block around lines 24–38. The remaining installation lines should follow it, beginning with something similar to:

```bash
sudo apt-get install -y \
  ros-humble-desktop ...
```

## 4. Run the script again

```bash
chmod +x setup_ros_humble_sim.sh
./setup_ros_humble_sim.sh
```

The script is safe to rerun. Already-installed packages will be skipped by `apt`, and the existing ROS workspace will be reused.

## If the new key URL also fails

Test GitHub access:

```bash
curl -I https://raw.githubusercontent.com
```

If that command returns an error, your network is blocking GitHub or DNS is not working. Test the ROS server separately:

```bash
curl -I http://packages.ros.org
```

You can also check the architecture before continuing:

```bash
dpkg --print-architecture
```

For Ubuntu 22.04 ROS Humble binary installation, it should normally be `amd64` or `arm64`.

Citations:
[1] Ubuntu (deb packages) — ROS 2 Documentation: Humble documentation https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html
