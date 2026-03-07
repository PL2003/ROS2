
# C++ Programming on Linux (Ubuntu) — Beginner Friendly README

This guide explains the **fundamentals of C++ development on Linux** with clear explanations, command syntax, code examples, and real-life analogies.  

Focus:  
- Compiler installation  
- Compilation process  
- Debugging with GDB  
- Core OOP concepts  
- File operations  
- Namespaces  
- Exception handling  
- STL basics  
- Building projects (Makefile & CMake)

---

# 1. Installing a C/C++ Compiler

## Purpose
Before writing C++ programs on Linux, the system needs a **compiler**.  
A compiler translates human-written code into machine instructions.

**Real-life analogy:**  
A compiler is like a **language translator** converting English into another language so a machine can understand it.

---
````
## 1.1 Update Ubuntu Packages

### Command
```bash
sudo apt-get update
````

### Explanation

* `sudo` → Run command with administrator permission
* `apt-get` → Ubuntu package manager
* `update` → Refreshes package lists

### What happens internally

The system downloads the **latest software lists** from repositories.

---

## 1.2 Install Development Tools

### Command

```bash
sudo apt-get install build-essential manpages-dev
```

### Explanation

| Package         | Purpose                                |
| --------------- | -------------------------------------- |
| build-essential | Contains gcc, g++, make, and libraries |
| manpages-dev    | Documentation for development          |

**Result:**
Your system now has the **GNU Compiler Collection (GCC)**.

---

# 2. Verifying Installation

## 2.1 Locate the Compiler

```bash
whereis gcc
whereis g++
```

### What it does

Shows the location of the compiler and its documentation.

Example output:

```
gcc: /usr/bin/gcc
```

---

## 2.2 Check Executable Path

```bash
which gcc
which g++
```

Shows the **exact executable path**.

Example

```
/usr/bin/gcc
```

---

## 2.3 Check Compiler Version

```bash
gcc --version
g++ --version
```

Example output

```
g++ (Ubuntu 11.4.0)
```

**Why important?**

Some programs require specific compiler versions.

---

# 3. Writing Your First C++ Program

## hello_world.cpp

```cpp
#include <iostream>
using namespace std;

int main()
{
    cout << "Hello Ubuntu Linux" << endl;
    return 0;
}
```

---

## Code Explanation

### Header File

```cpp
#include <iostream>
```

Provides input/output functions.

Examples:

* `cout` → print output
* `cin` → take input

---

### Namespace

```cpp
using namespace std;
```

The **std namespace** contains standard C++ functions.

Without it:

```cpp
std::cout << "Hello";
```

---

### Main Function

```cpp
int main()
```

Entry point of every C++ program.

The OS starts executing code from here.

---

### Output Statement

```cpp
cout << "Hello Ubuntu Linux" << endl;
```

| Part | Meaning                   |
| ---- | ------------------------- |
| cout | console output            |
| <<   | stream insertion operator |
| endl | newline                   |

---

### Return Statement

```cpp
return 0;
```

Means **program executed successfully**.

---

# 4. Compiling a C++ Program

## Step 1 — Navigate to Program Folder

```bash
cd Desktop
```

`cd` = change directory

---

## Step 2 — List Files

```bash
ls
```

Shows files in current directory.

---

## Step 3 — Compile Code

```bash
g++ hello_world.cpp
```

### What happens

1. Compiler checks syntax
2. Converts code to machine instructions
3. Creates executable file `a.out`

---

## Step 4 — Run the Program

```bash
./a.out
```

Output

```
Hello Ubuntu Linux
```

---

## Custom Executable Name

```bash
g++ hello_world.cpp -o hello_world
```

### Explanation

| Option | Meaning          |
| ------ | ---------------- |
| -o     | output file name |

Run program:

```
./hello_world
```

---

# 5. Debugging Programs using GDB

## What is a Debugger?

A debugger helps **find and fix bugs**.

**Real-life analogy:**
Like a **doctor diagnosing a patient step by step**.

---

## Install GDB

```bash
sudo apt-get install gdb
```

---

## Example Debug Program

### sum.cpp

```cpp
#include <iostream>
using namespace std;

int main()
{
    int a = 5;
    int b = 10;
    int sum = a + b;

    cout << "Sum = " << sum << endl;

    return 0;
}
```

---

## Compile with Debug Symbols

```bash
g++ -g sum.cpp -o sum
```

`-g` adds debugging information.

---

## Start Debugger

```bash
gdb sum
```

---

## Important GDB Commands

| Command       | Function          |
| ------------- | ----------------- |
| b line_number | set breakpoint    |
| r             | run program       |
| n             | execute next line |
| p variable    | print variable    |
| q             | quit debugger     |

---

# 6. Object Oriented Programming (OOP)

OOP organizes programs using **objects and classes**.

**Real-life analogy**

| Programming | Real Life          |
| ----------- | ------------------ |
| Class       | Blueprint of a car |
| Object      | Actual car         |

---

# 6.1 Struct vs Class

### Struct Example

```cpp
struct Robot_Struct
{
    int id;
    int wheels;
};
```

Default access = **public**

---

### Class Example

```cpp
class Robot
{
public:
    int id;
};
```

Default access = **private**

---

# 6.2 Classes and Objects

```cpp
#include <iostream>
using namespace std;

class Robot
{
public:
    string name;
    int wheels;

    void show()
    {
        cout << name << " has " << wheels << " wheels" << endl;
    }
};

int main()
{
    Robot r1;

    r1.name = "Mars Rover";
    r1.wheels = 6;

    r1.show();
}
```

### Output

```
Mars Rover has 6 wheels
```

---

# 6.3 Inheritance

Inheritance allows **reusing code from another class**.

---

## Example

```cpp
#include <iostream>
using namespace std;

class Robot
{
public:
    string name;
};

class Rover : public Robot
{
public:
    int wheels;
};

int main()
{
    Rover r;

    r.name = "Explorer";
    r.wheels = 6;

    cout << r.name << " wheels: " << r.wheels << endl;
}
```

---

### Types of Inheritance

| Type      | Access Result              |
| --------- | -------------------------- |
| public    | public stays public        |
| protected | public becomes protected   |
| private   | everything becomes private |

---

# 7. C++ File Read / Write Program

## Code

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main()
{
    ofstream outFile;
    outFile.open("config.txt");

    outFile << "Robot_ID=1" << endl;

    outFile.close();

    ifstream inFile;
    string data;

    inFile.open("config.txt");
    inFile >> data;

    cout << data << endl;

    inFile.close();
}
````

---

# 7.1 Purpose of the Program

This program:

1. Creates a file
2. Writes data to the file
3. Reads the data from the file
4. Prints the data to the terminal

### Real Life Example

Imagine a **robot saving its configuration** (Robot ID) into a file and later reading it when the system starts.

---

# 7.2 Header Files

```cpp
#include <iostream>
```

Provides **input/output operations**.

Examples:

* `cout` → print output
* `cin` → take input

---

```cpp
#include <fstream>
```

Provides **file stream classes**.

| Class    | Purpose    |
| -------- | ---------- |
| ofstream | write file |
| ifstream | read file  |
| fstream  | read/write |

---

# 7.3 Namespace

```cpp
using namespace std;
```

Allows access to standard library objects without writing `std::`.

Without namespace:

```cpp
std::cout << "Hello";
```

---

# 7.4 Creating Output File Stream

```cpp
ofstream outFile;
```

### Syntax

```
ofstream object_name;
```

### Meaning

Creates an object of **output file stream**.

Purpose: write data to files.

---

# 7.5 Opening a File

```cpp
outFile.open("config.txt");
```

### Syntax

```
fileObject.open("filename");
```

### What Happens

1. File `config.txt` is created if it does not exist.
2. If it exists, it is opened for writing.

---

# 7.6 Writing Data to File

```cpp
outFile << "Robot_ID=1" << endl;
```

### Syntax

```
fileObject << data;
```

### Explanation

| Element        | Meaning                   |
| -------------- | ------------------------- |
| `<<`           | stream insertion operator |
| `"Robot_ID=1"` | data written to file      |
| `endl`         | newline                   |

### Result in File

```
Robot_ID=1
```

---

# 7.7 Closing the File

```cpp
outFile.close();
```

### Purpose

* Saves data
* Releases file resources

**Important rule:** Always close files after writing.

---

# 7.8 Creating Input File Stream

```cpp
ifstream inFile;
```

### Syntax

```
ifstream object_name;
```

Used to **read data from files**.

---

# 7.9 Creating Variable to Store Data

```cpp
string data;
```

Stores the data read from file.

---

# 7.10 Opening File for Reading

```cpp
inFile.open("config.txt");
```

Opens the file created earlier.

---

# 7.11 Reading Data

```cpp
inFile >> data;
```

### Syntax

```
fileObject >> variable;
```

Reads file content and stores it into variable.

---

# 7.12 Printing the Data

```cpp
cout << data << endl;
```

Output

```
Robot_ID=1
```

---

# 7.13 Program Execution Flow

```
Start
 ↓
Create output file stream
 ↓
Open file config.txt
 ↓
Write Robot_ID=1
 ↓
Close file
 ↓
Open same file for reading
 ↓
Read data
 ↓
Print data
 ↓
End
```

---

# 8. Namespace Example

## Code

```cpp
namespace RobotA
{
    void move()
    {
        cout << "RobotA moving";
    }
}

namespace RobotB
{
    void move()
    {
        cout << "RobotB moving";
    }
}
```

---

# 8.1 Purpose

Namespaces avoid **name conflicts** when multiple libraries contain the same function name.

---

# 8.2 Namespace Syntax

```
namespace NamespaceName
{
    code
}
```

---

# 8.3 Function Definition

```cpp
void move()
```

### Syntax

```
return_type function_name()
```

Here:

| Part | Meaning                  |
| ---- | ------------------------ |
| void | function returns nothing |
| move | function name            |

---

# 8.4 Two Functions with Same Name

Both namespaces contain:

```
move()
```

But they belong to different namespaces.

---

# 8.5 Calling the Functions

Example usage:

```cpp
RobotA::move();
RobotB::move();
```

### Explanation

| Syntax           | Meaning                   |
| ---------------- | ------------------------- |
| `::`             | scope resolution operator |
| `RobotA::move()` | call move from RobotA     |

---

### Output

```
RobotA moving
RobotB moving
```

---

### Real Life Analogy

Two people named **Alex** working in different departments.

```
Engineering::Alex
Marketing::Alex
```

---

# 9. Exception Handling Example

## Code

```cpp
#include <iostream>
using namespace std;

int main()
{
    int a = 10;
    int b = 0;

    try
    {
        if(b == 0)
            throw "Division by zero";

        cout << a/b;
    }

    catch(const char* msg)
    {
        cout << msg;
    }
}
```

---

# 9.1 Purpose

Handles **runtime errors safely**.

Example: division by zero.

---

# 9.2 Variables

```cpp
int a = 10;
int b = 0;
```

Numbers for division.

---

# 9.3 Try Block

```cpp
try
{
    ...
}
```

Code inside `try` is monitored for errors.

---

# 9.4 Throw Statement

```cpp
throw "Division by zero";
```

### Syntax

```
throw error_message;
```

Stops program and sends error to `catch`.

---

# 9.5 Catch Block

```cpp
catch(const char* msg)
```

### Syntax

```
catch(data_type variable)
```

Captures error thrown by `throw`.

---

# 9.6 Printing Error Message

```cpp
cout << msg;
```

Output

```
Division by zero
```

---

# 9.7 Execution Flow

```
Start
 ↓
a = 10
b = 0
 ↓
try block starts
 ↓
check if b == 0
 ↓
throw error
 ↓
catch block executes
 ↓
print error
 ↓
End
```

---

# 10. STL Vector Example

## Code

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    vector<int> numbers;

    numbers.push_back(10);
    numbers.push_back(20);

    cout << numbers[0];
}
```

---

# 10.1 STL Vector

`vector` is a **dynamic array**.

Difference from normal arrays:

| Array      | Vector       |
| ---------- | ------------ |
| fixed size | dynamic size |

---

# 10.2 Include Vector Library

```cpp
#include <vector>
```

Allows use of STL vector container.

---

# 10.3 Creating Vector

```cpp
vector<int> numbers;
```

### Syntax

```
vector<data_type> name;
```

Example

```
vector<int>
```

Means vector storing integers.

---

# 10.4 Adding Elements

```cpp
numbers.push_back(10);
```

### Syntax

```
vector.push_back(value);
```

Adds element at end of vector.

After execution:

```
numbers = [10]
```

---

Next:

```
numbers.push_back(20)
```

Vector becomes

```
numbers = [10, 20]
```

---

# 10.5 Accessing Elements

```cpp
numbers[0]
```

### Syntax

```
vector[index]
```

Index starts at **0**.

So:

```
numbers[0] → 10
```

---

# 10.6 Output

```
10
```

---

# 10.7 Execution Flow

```
Start
 ↓
Create empty vector
 ↓
Add 10
 ↓
Add 20
 ↓
Access element 0
 ↓
Print 10
 ↓
End
```

---

# 11. Building Large C++ Projects

When programs become large, compiling each file manually becomes difficult.

Two tools help:

| Tool     | Purpose                     |
| -------- | --------------------------- |
| Makefile | build automation            |
| CMake    | cross-platform build system |

---

# 11.1 Makefile Example

```makefile
all:
	g++ main.cpp -o program
```

Run build:

```
make
```

---

# 11.2 CMake Example

## Install CMake

```bash
sudo apt install cmake
```

---

## CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.5)

project(MyProject)

add_executable(my_program main.cpp)
```

---

## Build Project

```
mkdir build
cd build
cmake ..
make
```

---

# Final Summary

Key ideas covered:

1. Install C++ compiler using `build-essential`
2. Verify installation using `gcc --version`
3. Write programs using `iostream`
4. Compile using `g++`
5. Execute using `./program`
6. Debug programs using **GDB**
7. Learn **OOP concepts**: classes, objects, inheritance
8. Read and write files using **fstream**
9. Use **namespaces** to avoid conflicts
10. Handle runtime errors using **exceptions**
11. Use **STL** for efficient data structures
12. Build large projects using **Makefiles or CMake**

---

If useful, the next logical extension would be a **"C++ for ROS2 Robotics Development README"**, which connects these concepts directly to **ROS nodes, publishers, subscribers, and robot control code**.

```
```
