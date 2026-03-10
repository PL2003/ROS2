
# Python Fundamentals for Robotics Programming (Linux/Ubuntu)

Structured revision notes covering **Python basics, scripting, classes, file I/O, modules, serial communication, and robotics-related libraries**.  
Focus: **syntax understanding, program flow, and practical robotics use cases**.

---

# 1. Python Overview

Python is a **high-level, interpreted programming language** widely used in:

- Robotics
- Machine Learning
- Data Science
- Automation
- Scientific computing

### Key Features

| Feature | Explanation |
|------|------|
| Interpreted language | Code runs line-by-line using Python interpreter |
| Easy syntax | Simple and readable |
| Cross-platform | Works on Linux, Windows, macOS |
| Large libraries | Supports robotics, AI, visualization |

---

# 2. Python Interpreter (Ubuntu)

Python is usually **preinstalled in Ubuntu Linux**.

### Start Interpreter

```bash
python
````

or

```bash
python3
```

### Interpreter Example

```python
print("Hello World")
```

Output

```
Hello World
```

---

# 3. Checking Python Installation

### Check Python Path

```bash
which python
which python3
```

Shows location of Python executable.

---

### Find Python Files

```bash
whereis python
```

Displays:

* Binary location
* Source files
* Documentation

---

# 4. Installing Python (if not installed)

```bash
sudo apt-get install python python3
```

---

# 5. Python Programming Methods

Two ways to write Python code:

| Method           | Description               |
| ---------------- | ------------------------- |
| Interpreter Mode | Run commands line-by-line |
| Script Mode      | Write code in `.py` file  |

Standard practice: **use script mode**.

---

# 6. Writing Your First Python Script

Create a file:

```
hello_world.py
```

### Code

```python
#!/usr/bin/env python3

print("Hello World")
```

### Syntax Explanation

| Part                     | Meaning                                   |
| ------------------------ | ----------------------------------------- |
| `#!/usr/bin/env python3` | Shebang line (selects Python interpreter) |
| `print()`                | Outputs text to screen                    |
| `"Hello World"`          | String data                               |

---

# 7. Running Python Script

Navigate to folder and run:

```bash
python3 hello_world.py
```

Output

```
Hello World
```

---

# 8. Python Classes (Object-Oriented Programming)

Classes allow modeling **real-world objects** such as robots.

Example: Robot movement simulation.

---

# 9. Robot Class Example

```python
class Robot:

    def __init__(self):
        print("Started Robot")

    def __del__(self):
        print("Robot stopped")

    def move_forward(self, distance):
        print("Robot moving forward: " + str(distance) + "m")

    def move_backward(self, distance):
        print("Robot moving backward: " + str(distance) + "m")

    def move_left(self, distance):
        print("Robot moving left: " + str(distance) + "m")

    def move_right(self, distance):
        print("Robot moving right: " + str(distance) + "m")
```

---

# 10. Constructor

### Syntax

```python
def __init__(self):
```

### Purpose

Runs automatically **when object is created**.

Example

```python
print("Started Robot")
```

Output when robot object created:

```
Started Robot
```

---

# 11. Destructor

### Syntax

```python
def __del__(self):
```

### Purpose

Runs automatically **when object is destroyed**.

Output

```
Robot stopped
```

---

# 12. Class Methods

Example

```python
def move_forward(self, distance):
```

### Syntax Structure

```
def function_name(self, parameters):
```

| Element    | Meaning                  |
| ---------- | ------------------------ |
| `def`      | Defines a function       |
| `self`     | Current object reference |
| `distance` | Input parameter          |

---

### Function Body

```python
print("Robot moving forward: " + str(distance) + "m")
```

Explanation

| Part    | Meaning                   |
| ------- | ------------------------- |
| `str()` | Converts number to string |
| `+`     | String concatenation      |

---

# 13. Creating an Object

```python
obj = Robot()
```

### What Happens

1. Memory allocated
2. Constructor called

Output

```
Started Robot
```

---

# 14. Calling Class Methods

```python
obj.move_forward(2)
obj.move_backward(2)
obj.move_left(2)
obj.move_right(2)
```

Output

```
Robot moving forward: 2m
Robot moving backward: 2m
Robot moving left: 2m
Robot moving right: 2m
```

---

# 15. Python File Handling

File handling is important in robotics for:

* Sensor logging
* Configuration files
* Data storage

---

# 16. Opening a File

```python
file_obj = open("test.txt","w+")
```

### Syntax

```
open(filename, mode)
```

### Modes

| Mode | Function     |
| ---- | ------------ |
| `r`  | Read         |
| `w`  | Write        |
| `a`  | Append       |
| `w+` | Read + Write |

`w+` overwrites existing file.

---

# 17. Writing to File

```python
file_obj.write(text)
```

Writes text into the file.

Example

```
Robot Data
```

---

# 18. Closing File

```python
file_obj.close()
```

Important because it:

* Saves data
* Releases memory

---

# 19. Reading from File

```python
file_obj = open("test.txt",'r')
```

Read a line

```python
text = file_obj.readline()
```

---

# 20. Python Modules

Modules allow **code reuse**.

Similar to **header files in C/C++**.

---

# 21. Importing Modules

### Syntax

```python
import module_name
```

Example

```python
import os
import sys
```

---

# 22. Import Specific Class

```python
from module_name import class_name
```

Example

```python
from os import system
```

---

# 23. Creating Custom Module

File:

```
test.py
```

Example code

```python
class Test:

    def execute(self, text):
        print(text)
```

---

# 24. Using the Module

```python
import test

obj = test.Test()
obj.execute("Hello Module")
```

---

# 25. Python Serial Communication[REF](https://www.instructables.com/Linux-Serial-Port-Communication-With-Arduino-Using/)

Robots communicate with devices through:

* USB
* UART
* Serial ports

Python uses **PySerial library**.

---

# 26. Installing PySerial

```bash
sudo apt-get update
sudo apt install python3-pip python3-venv
```

---

# 27. Finding Serial Device

Use command

```bash
dmesg
```

Example output

```
/dev/ttyUSB0
```

---

# 28. Serial Port Permissions

```bash
sudo chmod 777 /dev/ttyUSB0
```

or

```bash
sudo adduser $USER dialout
```

---

# 29. Serial Communication Code

```python
import serial

ser = serial.Serial('/dev/ttyUSB0',9600)

ser.write(b'Hello')

text = ser.readline()

print(text)
```

---

# 30. Syntax Explanation

| Code            | Meaning              |
| --------------- | -------------------- |
| `import serial` | Load PySerial module |
| `Serial()`      | Opens serial port    |
| `/dev/ttyUSB0`  | Serial device path   |
| `9600`          | Baud rate            |
| `write()`       | Send data            |
| `readline()`    | Read incoming data   |

---

# 31. Reading Serial Data

```python
text = ser.read()
```

Reads **1 byte**.

---

```python
text = ser.read(10)
```

Reads **10 bytes**.

---

# 32. Scientific Computing Libraries

| Library    | Purpose                               |
| ---------- | ------------------------------------- |
| NumPy      | Numerical computing                   |
| SciPy      | Engineering and scientific algorithms |
| Matplotlib | Data visualization                    |

---

# 33. Machine Learning Libraries

| Library      | Purpose                             |
| ------------ | ----------------------------------- |
| TensorFlow   | Deep learning framework             |
| Keras        | High-level neural network API       |
| Scikit-learn | Machine learning algorithms         |
| Theano       | Mathematical expression computation |
| Caffe        | Deep learning framework             |

---

# 34. Computer Vision Libraries

| Library | Purpose                              |
| ------- | ------------------------------------ |
| OpenCV  | Image processing and computer vision |
| PIL     | Image manipulation                   |

---

# 35. Python in Robotics

Python is widely used in **ROS (Robot Operating System)**.

Main interface:

```
rospy
```

Used for:

* Sensor communication
* Robot control
* Navigation algorithms

---

# 36. Python IDEs

| IDE     | Features                        |
| ------- | ------------------------------- |
| PyCharm | Professional Python development |
| Geany   | Lightweight editor              |
| Spyder  | Scientific computing IDE        |

---

# 37. Python Workflow in Robotics

Typical robotics workflow:

```
Sensors
   ↓
Microcontroller / Arduino
   ↓
Serial Communication
   ↓
Python Program
   ↓
Data Processing / AI
   ↓
Robot Control Commands
```

---

# 38. Key Programming Principles

### Object-Oriented Programming

Robots modeled as objects.

### Modular Programming

Modules improve reusability.

### File Logging

Sensor data stored for analysis.

### Serial Communication

Interface with hardware.

---

# 39. Quick Revision Summary

* Python is an interpreted language widely used in robotics.
* Code can run in **interpreter or script mode**.
* Python scripts use `.py` extension.
* Classes model robot behavior using **constructor, methods, destructor**.
* File I/O allows **sensor logging and configuration storage**.
* Modules allow reusable code.
* PySerial enables **USB/UART communication with robots**.
* Libraries like **NumPy, TensorFlow, OpenCV** support AI and vision.
* Python integrates easily with **ROS for robot control**.

---

```
```
