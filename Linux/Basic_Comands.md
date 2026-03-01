# 🐧 Linux Commands Cheat Sheet (2026)

Linux commands are text-based instructions used in the terminal to control the operating system efficiently.

---
### Ref:

* [Gfg](https://www.geeksforgeeks.org/linux-unix/linux-commands-cheat-sheet/)
* [Books][file:///D:/L3T1/SS/ROS/Robot%20Operating%20System%20for%20Absolute%20Beginners_%20Robotics%20Programming%20Made%20Easy%20(%20PDFDrive%20).pdf]

## 1️⃣ File Operations Commands

| Command | Function                  | Example + Syntax               | What Happens in Example                                                 |                                    |
| ------- | ------------------------- | ------------------------------ | ----------------------------------------------------------------------- | ---------------------------------- |
| `ls`    | List directory contents   | `ls -l`                        | Lists files in long format showing permissions, owner, size, timestamp. |                                    |
| `cat`   | Display file content      | `cat file.txt`                 | Reads file and prints content to stdout.                                |                                    |
| `cp`    | Copy files                | `cp file1.txt file2.txt`       | Creates duplicate of file1 as file2.                                    |                                    |
| `mv`    | Move/Rename file          | `mv old.txt new.txt`           | Renames file without copying data.                                      |                                    |
| `rm`    | Remove file               | `rm file.txt`                  | Deletes file permanently.                                               |                                    |
| `touch` | Create empty file         | `touch new.txt`                | Creates empty file or updates timestamp.                                |                                    |
| `file`  | Show file type            | `file image.png`               | Detects file type using magic numbers.                                  |                                    |
| `diff`  | Compare files             | `diff a.txt b.txt`             | Shows line differences between files.                                   |                                    |
| `head`  | Show first lines          | `head -5 file.txt`             | Prints first 5 lines.                                                   |                                    |
| `tail`  | Show last lines           | `tail -f log.txt`              | Continuously monitors file updates.                                     |                                    |
| `grep`  | Search text pattern       | `grep "error" log.txt`         | Filters lines containing "error".                                       |                                    |
| `sort`  | Sort file content         | `sort names.txt`               | Arranges lines alphabetically.                                          |                                    |
| `wc`    | Count words/lines         | `wc -l file.txt`               | Counts number of lines.                                                 |                                    |
| `tee`   | Output to file + terminal | `ls                            | tee list.txt`                                                           | Displays output and saves to file. |
| `tar`   | Archive files             | `tar -cvf archive.tar folder/` | Creates archive without compression.                                    |                                    |
| `zip`   | Compress files            | `zip file.zip file.txt`        | Compresses file into zip archive.                                       |                                    |

---

## 2️⃣ Directory Operations

| Command | Function               | Example                | What Happens                      |
| ------- | ---------------------- | ---------------------- | --------------------------------- |
| `pwd`   | Show current directory | `pwd`                  | Displays absolute path.           |
| `cd`    | Change directory       | `cd /home/user`        | Moves shell session to directory. |
| `mkdir` | Create directory       | `mkdir project`        | Creates new directory.            |
| `rmdir` | Remove empty directory | `rmdir test`           | Deletes empty directory only.     |
| `find`  | Search files           | `find . -name "*.txt"` | Recursively finds .txt files.     |
| `du`    | Disk usage             | `du -sh folder`        | Shows total size of folder.       |

---

## 3️⃣ Permission & Ownership

| Command | Function           | Example                | What Happens                |
| ------- | ------------------ | ---------------------- | --------------------------- |
| `chmod` | Change permissions | `chmod 755 script.sh`  | Sets rwxr-xr-x permissions. |
| `chown` | Change owner       | `chown user file.txt`  | Transfers ownership.        |
| `chgrp` | Change group       | `chgrp admin file.txt` | Changes group ownership.    |

---

## 4️⃣ User Management

| Command   | Function     | Example             | What Happens              |
| --------- | ------------ | ------------------- | ------------------------- |
| `useradd` | Add user     | `sudo useradd john` | Creates new account.      |
| `userdel` | Delete user  | `sudo userdel john` | Removes account.          |
| `passwd`  | Set password | `passwd john`       | Updates user password.    |
| `whoami`  | Current user | `whoami`            | Displays active username. |

---

## 5️⃣ Process Management

| Command | Function             | Example       | What Happens                    |
| ------- | -------------------- | ------------- | ------------------------------- |
| `ps`    | Process snapshot     | `ps aux`      | Lists all running processes.    |
| `top`   | Live process monitor | `top`         | Real-time CPU/memory usage.     |
| `htop`  | Interactive monitor  | `htop`        | Enhanced visual process viewer. |
| `kill`  | Terminate process    | `kill 1234`   | Sends SIGTERM to PID 1234.      |
| `watch` | Repeat command       | `watch df -h` | Executes df every 2 seconds.    |

---

## 6️⃣ Networking Commands

| Command | Function          | Example                   | What Happens                             |
| ------- | ----------------- | ------------------------- | ---------------------------------------- |
| `ping`  | Test connectivity | `ping google.com`         | Sends ICMP packets to test reachability. |
| `curl`  | Transfer data     | `curl example.com`        | Fetches webpage content.                 |
| `wget`  | Download files    | `wget file.url`           | Downloads remote file.                   |
| `ssh`   | Remote login      | `ssh user@host`           | Opens secure remote shell.               |
| `scp`   | Secure copy       | `scp file user@host:/dir` | Transfers file over SSH.                 |

---

## 7️⃣ Package Management

| Command | Function             | Example                | What Happens          |
| ------- | -------------------- | ---------------------- | --------------------- |
| `apt`   | Manage packages      | `sudo apt update`      | Updates package list. |
| `dnf`   | RHEL package manager | `sudo dnf install git` | Installs git package. |

---

## 8️⃣ Disk & System Info

| Command  | Function        | Example    | What Happens                     |
| -------- | --------------- | ---------- | -------------------------------- |
| `df`     | Disk free space | `df -h`    | Shows disk usage human-readable. |
| `du`     | Directory size  | `du -sh *` | Displays size of each folder.    |
| `uname`  | System info     | `uname -a` | Kernel + architecture info.      |
| `free`   | Memory usage    | `free -h`  | Displays RAM usage.              |
| `uptime` | System uptime   | `uptime`   | Shows running duration + load.   |

---

## 9️⃣ IO Redirection

| Syntax | Function        | Example               | Result                 |
| ------ | --------------- | --------------------- | ---------------------- |
| `>`    | Redirect output | `ls > out.txt`        | Overwrites file.       |
| `>>`   | Append output   | `echo hi >> file.txt` | Appends text.          |
| `<`    | Redirect input  | `sort < file.txt`     | Uses file as input.    |
| `2>`   | Redirect error  | `ls wrong 2> err.txt` | Stores error output.   |
| `&>`   | Redirect all    | `cmd &> all.txt`      | Saves stdout + stderr. |

---

## 🔟 Environment Variables

| Command  | Function        | Example                 | What Happens                        |
| -------- | --------------- | ----------------------- | ----------------------------------- |
| `export` | Set variable    | `export PATH=/new/path` | Updates environment variable.       |
| `env`    | List variables  | `env`                   | Displays all environment variables. |
| `unset`  | Remove variable | `unset PATH`            | Deletes variable.                   |

---

## 1️⃣1️⃣ Bash Shortcuts

| Shortcut | Action                 |
| -------- | ---------------------- |
| Ctrl + A | Move to start of line  |
| Ctrl + E | Move to end of line    |
| Ctrl + C | Stop running command   |
| Ctrl + R | Search command history |
| Ctrl + L | Clear screen           |

---

## 1️⃣2️⃣ Text Editors

| Editor | Usage                    | Example          |
| ------ | ------------------------ | ---------------- |
| nano   | Beginner-friendly editor | `nano file.txt`  |
| vi     | Classic editor           | `vi file.txt`    |
| vim    | Advanced vi              | `vim file.txt`   |
| emacs  | Feature-rich editor      | `emacs file.txt` |

---

# ⚡ Quick Summary

* Linux commands allow fast, scriptable OS control
* File commands manage data (cp, mv, rm)
* Process tools monitor performance (ps, top)
* Networking tools enable remote access (ssh, scp)
* Permissions ensure security (chmod, chown)
* IO redirection controls data flow
* Package managers install/update software
* Shell shortcuts improve speed
* Text editors enable terminal-based file editing
* Automation possible via scripting + scheduling


