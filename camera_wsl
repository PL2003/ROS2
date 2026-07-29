That's the final piece of the puzzle! `usbipd` requires you to **bind** a USB device to make it available for sharing before attaching it to WSL.

Since your camera is on BUSID **`4-7`**, follow these quick steps in your PowerShell terminal to finish the connection.

---

## 🛠️ Step-by-Step Fix (In Windows PowerShell)

### Step 1: Bind the Camera (One-Time Setup)

Run this command in your PowerShell window:

```powershell
usbipd bind --busid 4-7

```

---

### Step 2: Attach it to WSL

Now run the attach command again:

```powershell
usbipd attach --wsl --busid 4-7

```

*(You should see the state change to **Attached** in `usbipd list`.)*

---

## 🛠️ Step 3: Verify & Test in WSL Ubuntu

Switch back to your **WSL terminal** (`laha@PALASHPRIYA`) and check for the camera:

```bash
ls -l /dev/video*

```

You should now see `/dev/video0` and `/dev/video1` listed!

To test the stream directly in your project folder (`/mnt/c/Users/pc/drone`):

```bash
v4l2-ctl --list-devices

```

---

## 💡 Quick Tips for Future Sessions

* **Auto-Attach on Boot:** To keep the camera continuously shared across restarts without needing to attach manually every time:
```powershell
usbipd attach --wsl --busid 4-7 --auto-attach

```


* **To Detach:** If you ever need the camera back exclusively on Windows:
```powershell
usbipd detach --busid 4-7

```
