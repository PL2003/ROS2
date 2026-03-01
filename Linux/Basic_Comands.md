# 🐧 Linux Commands Cheat Sheet (2026)

Linux commands are text-based instructions used in the terminal to control the operating system efficiently.

---
### Ref:

* [Gfg](https://www.geeksforgeeks.org/linux-unix/linux-commands-cheat-sheet/)
* [Books](file:///D:/L3T1/SS/ROS/Robot%20Operating%20System%20for%20Absolute%20Beginners_%20Robotics%20Programming%20Made%20Easy%20(%20PDFDrive%20).pdf)
  

## 📁 1. File Operations Commands

| Command | Function                  | Example (Syntax)               |                 |
| ------- | ------------------------- | ------------------------------ | --------------- |
| `cat`   | Display file content      | `cat file.txt`                 |                 |
| `cp`    | Copy files/directories    | `cp file.txt backup.txt`       |                 |
| `mv`    | Move or rename files      | `mv old.txt new.txt`           |                 |
| `rm`    | Delete files/directories  | `rm file.txt`                  |                 |
| `touch` | Create empty file         | `touch newfile.txt`            |                 |
| `file`  | Show file type            | `file document.pdf`            |                 |
| `head`  | Show first lines          | `head -n 5 file.txt`           |                 |
| `tail`  | Show last lines           | `tail -n 10 file.txt`          |                 |
| `diff`  | Compare files             | `diff file1.txt file2.txt`     |                 |
| `grep`  | Search text pattern       | `grep "error" file.txt`        |                 |
| `sort`  | Sort content              | `sort names.txt`               |                 |
| `cut`   | Extract columns           | `cut -d "," -f1 data.csv`      |                 |
| `tee`   | Output to file + terminal | `ls                            | tee output.txt` |
| `tar`   | Archive files             | `tar -cvf archive.tar folder/` |                 |
| `zip`   | Compress files            | `zip file.zip file.txt`        |                 |
| `unzip` | Extract zip archive       | `unzip file.zip`               |                 |
| `ln`    | Create links              | `ln -s file.txt link.txt`      |                 |
| `shred` | Securely delete file      | `shred file.txt`               |                 |
| `wc`    | Word/line/byte count      | `wc file.txt`                  |                 |

---

## 📂 2. Directory Operations Commands

| Command   | Function               | Example                |
| --------- | ---------------------- | ---------------------- |
| `pwd`     | Show current path      | `pwd`                  |
| `cd`      | Change directory       | `cd /home/user`        |
| `ls`      | List directory content | `ls -l`                |
| `mkdir`   | Create directory       | `mkdir project`        |
| `rmdir`   | Remove empty directory | `rmdir folder`         |
| `tree`    | Show directory tree    | `tree`                 |
| `du`      | Show directory size    | `du -sh folder`        |
| `find`    | Search files           | `find . -name "*.txt"` |
| `locate`  | Fast file search       | `locate file.txt`      |
| `dirname` | Extract directory path | `dirname /home/a.txt`  |

---

## 🔐 3. File Permission & Ownership Commands

| Command  | Function               | Example                |
| -------- | ---------------------- | ---------------------- |
| `chmod`  | Change permissions     | `chmod 755 script.sh`  |
| `chown`  | Change owner           | `chown user file.txt`  |
| `chgrp`  | Change group           | `chgrp admin file.txt` |
| `chattr` | Change file attributes | `chattr +i file.txt`   |

---

## 👤 4. User Management Commands

| Command   | Function          | Example                      |
| --------- | ----------------- | ---------------------------- |
| `useradd` | Create user       | `sudo useradd john`          |
| `userdel` | Delete user       | `sudo userdel john`          |
| `usermod` | Modify user       | `sudo usermod -aG sudo john` |
| `passwd`  | Set password      | `passwd john`                |
| `whoami`  | Show current user | `whoami`                     |
| `id`      | Show user ID info | `id john`                    |
| `who`     | Show logged users | `who`                        |

---

## 👥 5. Group Management Commands

| Command    | Function              | Example              |
| ---------- | --------------------- | -------------------- |
| `groupadd` | Create group          | `sudo groupadd devs` |
| `groupdel` | Delete group          | `sudo groupdel devs` |
| `groups`   | Show user groups      | `groups john`        |
| `gpasswd`  | Manage group password | `gpasswd devs`       |

---

## ⚙️ 6. Process Management Commands

| Command  | Function            | Example         |
| -------- | ------------------- | --------------- |
| `ps`     | Process snapshot    | `ps aux`        |
| `top`    | Real-time processes | `top`           |
| `htop`   | Interactive viewer  | `htop`          |
| `kill`   | Terminate process   | `kill 1234`     |
| `pidof`  | Get PID             | `pidof firefox` |
| `bg`     | Run in background   | `bg %1`         |
| `fg`     | Bring to foreground | `fg %1`         |
| `time`   | Measure execution   | `time ls`       |
| `watch`  | Repeat command      | `watch -n 2 df` |
| `uptime` | Show uptime         | `uptime`        |

---

## 🌐 7. Networking Commands

| Command      | Function          | Example                        |
| ------------ | ----------------- | ------------------------------ |
| `ping`       | Test connectivity | `ping google.com`              |
| `curl`       | Transfer via URL  | `curl https://example.com`     |
| `wget`       | Download file     | `wget file_url`                |
| `ssh`        | Remote login      | `ssh user@server`              |
| `scp`        | Secure copy       | `scp file.txt user@host:/path` |
| `rsync`      | Sync files        | `rsync -av src/ dest/`         |
| `ip`         | Network config    | `ip addr`                      |
| `netstat`    | Show connections  | `netstat -tuln`                |
| `traceroute` | Trace path        | `traceroute google.com`        |
| `nslookup`   | DNS query         | `nslookup google.com`          |

---

## 📦 8. Package Management

| Command   | Function                 | Example                |
| --------- | ------------------------ | ---------------------- |
| `apt`     | Manage packages (Debian) | `sudo apt install git` |
| `apt-get` | Package tool             | `sudo apt-get update`  |
| `dnf`     | Manage packages (RHEL)   | `sudo dnf install vim` |

---

## ⏰ 9. Job Scheduling

| Command   | Function                | Example                      |
| --------- | ----------------------- | ---------------------------- |
| `crontab` | Schedule recurring jobs | `crontab -e`                 |
| `cron`    | Background scheduler    | `sudo systemctl status cron` |
| `atq`     | View scheduled jobs     | `atq`                        |
| `atrm`    | Remove scheduled job    | `atrm 2`                     |

---

## 💽 10. Disk & File System

| Command | Function            | Example                |
| ------- | ------------------- | ---------------------- |
| `df`    | Disk usage          | `df -h`                |
| `du`    | Directory size      | `du -sh folder`        |
| `mount` | Mount filesystem    | `mount /dev/sdb1 /mnt` |
| `fdisk` | Partition disk      | `sudo fdisk /dev/sda`  |
| `sync`  | Flush write buffers | `sync`                 |

---

## 🖥 11. System Information

| Command    | Function      | Example     |       |
| ---------- | ------------- | ----------- | ----- |
| `uname`    | System info   | `uname -a`  |       |
| `hostname` | Show hostname | `hostname`  |       |
| `free`     | Memory usage  | `free -h`   |       |
| `dmesg`    | Kernel logs   | `dmesg      | less` |
| `lsusb`    | USB devices   | `lsusb`     |       |
| `lshw`     | Hardware info | `sudo lshw` |       |

---

## 📝 12. Text Processing

| Command | Function         | Example                      |
| ------- | ---------------- | ---------------------------- |
| `awk`   | Pattern scanning | `awk '{print $1}' file.txt`  |
| `sed`   | Stream editor    | `sed 's/old/new/g' file.txt` |
| `tr`    | Translate chars  | `tr a-z A-Z < file.txt`      |
| `fmt`   | Format text      | `fmt file.txt`               |

---

## 🔄 13. IO Redirection

| Syntax | Function            | Example            |
| ------ | ------------------- | ------------------ |
| `>`    | Redirect output     | `ls > file.txt`    |
| `>>`   | Append output       | `ls >> file.txt`   |
| `<`    | Redirect input      | `sort < file.txt`  |
| `2>`   | Redirect error      | `cmd 2> error.txt` |
| `&>`   | Redirect all output | `cmd &> all.txt`   |

---

## 🌍 14. Environment Variables

| Command    | Function         | Example                       |
| ---------- | ---------------- | ----------------------------- |
| `export`   | Set variable     | `export PATH=$PATH:/new/path` |
| `env`      | Show variables   | `env`                         |
| `unset`    | Remove variable  | `unset VAR`                   |
| `printenv` | Display variable | `printenv PATH`               |

---

## ✍ 15. Text Editors

| Editor  | Function                 | Example          |
| ------- | ------------------------ | ---------------- |
| `nano`  | Beginner-friendly editor | `nano file.txt`  |
| `vi`    | Classic editor           | `vi file.txt`    |
| `vim`   | Advanced vi              | `vim file.txt`   |
| `emacs` | Extensible editor        | `emacs file.txt` |

---

## ⌨ Bash Shortcuts

| Shortcut   | Function          |
| ---------- | ----------------- |
| `Ctrl + A` | Beginning of line |
| `Ctrl + E` | End of line       |
| `Ctrl + C` | Terminate command |
| `Ctrl + R` | Search history    |
| `Ctrl + L` | Clear screen      |

---

## 🛠 Development Commands

| Command | Function         | Example                |
| ------- | ---------------- | ---------------------- |
| `gcc`   | Compile C        | `gcc main.c -o main`   |
| `g++`   | Compile C++      | `g++ main.cpp -o main` |
| `gdb`   | Debugger         | `gdb ./a.out`          |
| `make`  | Build automation | `make`                 |

---

## 📖 Help Commands

| Command   | Function          | Example        |
| --------- | ----------------- | -------------- |
| `man`     | Manual            | `man ls`       |
| `help`    | Built-in help     | `help cd`      |
| `which`   | Show command path | `which python` |
| `history` | Command history   | `history`      |

---
