
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

A Linux makefile is a tool to compile one or more sources in a single
command and build the executable.

our project has
three files.
• main.cpp: The main code that we are going to build.
• add.h: The header file of the add class. It has a
declaration of the class.
• add.cpp: This file has the entire definition of the add class.

## "add.h"
```makefile
#include <iostream>
class add
{
public:
int cal(int n1,int n2);
};

```
## add.cpp
```makefile
#include "add.h"
int add::cal(int a,int b){
	return (a+b);
}

```
## main.cpp

```makefile
#include"add.h"
using namespace std;
int main()
{
add obj;
int result = obj.cal(3,10);
cout << "sum of result " << result << endl;
return 0;

}
```

```makefile
all:
	g++ -c add.cpp -o add.o
	g++ -c main.cpp -o main.o
	g++ add.o main.o -o main

	./main
```

Run build:

```
make
```

---
# Linux Makefile and CMake File Creation (C++ Build Process)

---

# 1. Build Systems in Linux (Overview)

**Build systems** automate the process of converting **source code → executable program**.

### Main Tools

| Tool         | Purpose                                                     |
| ------------ | ----------------------------------------------------------- |
| **Makefile** | Automates compilation using `make`                          |
| **CMake**    | Generates Makefiles automatically for cross-platform builds |

**Typical compilation flow**

```
Source Code (.cpp)
       ↓
Compiler (g++)
       ↓
Object Files (.o)
       ↓
Linking
       ↓
Executable Program
```

---

# 2. Linux Makefile

## Definition

A **Makefile** is a script used by the **`make` utility** to automate compilation and linking of programs.

It describes:

* How to compile files
* Which files depend on others
* How to create the final executable

---

# 3. Makefile Example Code

```make
CC = g++
CFLAGS = -c
SOURCES = main.cpp add.cpp
OBJECTS = $(SOURCES:.cpp=.o)
EXECUTABLE = main

all: $(OBJECTS) $(EXECUTABLE)

$(EXECUTABLE) : $(OBJECTS)
        $(CC) $(OBJECTS) -o $@

.cpp.o: *.h
        $(CC) $(CFLAGS) $< -o $@

clean :
        -rm -f $(OBJECTS) $(EXECUTABLE)

.PHONY: all clean
```

---

# 4. Makefile Structure

A Makefile follows the structure:

```
target : dependencies
        command
```

Example:

```
program : main.o add.o
        g++ main.o add.o -o program
```

Meaning:

* To build **program**
* First build **main.o and add.o**
* Then run the command.

---

# 5. Explanation of Each Line

## Compiler Definition

```make
CC = g++
```

**Meaning**

* Defines the **compiler**.
* `g++` is the **GNU C++ compiler**.

---

## Compiler Flags

```make
CFLAGS = -c
```

**Meaning**

* `-c` → Compile only
* Produces **object file (.o)** without linking.

Example

```
g++ -c main.cpp
```

Output

```
main.o
```

---

## Source Files

```make
SOURCES = main.cpp add.cpp
```

Defines all **C++ source files** in the project.

---

## Object Files

```make
OBJECTS = $(SOURCES:.cpp=.o)
```

### Syntax Explanation

```
$(variable:old=new)
```

Meaning:

Replace `.cpp` with `.o`.

Result:

```
main.o add.o
```

---

## Executable Name

```make
EXECUTABLE = main
```

Defines the final **program name**.

Output program will be:

```
main
```

---

# 6. Main Build Rule

```make
all: $(OBJECTS) $(EXECUTABLE)
```

### Meaning

When user runs:

```
make
```

Make builds:

```
main.o
add.o
main (executable)
```

---

# 7. Linking Rule

```make
$(EXECUTABLE) : $(OBJECTS)
        $(CC) $(OBJECTS) -o $@
```

### Syntax Elements

| Symbol | Meaning          |
| ------ | ---------------- |
| `$@`   | Target name      |
| `$<`   | First dependency |

### Expanded Command

```
g++ main.o add.o -o main
```

Purpose:

* Link object files
* Produce executable **main**

---

# 8. Compilation Rule

```make
.cpp.o: *.h
        $(CC) $(CFLAGS) $< -o $@
```

### Meaning

Compile `.cpp` files into `.o` files.

Expanded example:

```
g++ -c main.cpp -o main.o
```

Explanation:

| Symbol | Meaning     |
| ------ | ----------- |
| `$<`   | input file  |
| `$@`   | output file |

---

# 9. Clean Rule

```make
clean :
        -rm -f $(OBJECTS) $(EXECUTABLE)
```

Purpose:

Delete generated files.

Command:

```
make clean
```

Removes:

```
main.o
add.o
main
```

---

# 10. Phony Targets

```make
.PHONY: all clean
```

Prevents confusion if files named `all` or `clean` exist.

Ensures these are **commands**, not files.

---

# 11. Building the Project

### Step 1 — Save Makefile

```
Makefile
```

### Step 2 — Run Build Command

```
make
```

Process:

```
main.cpp
add.cpp
   ↓
Compile
   ↓
main.o add.o
   ↓
Link
   ↓
main (executable)
```

---

# 12. Running the Program

Execute:

```
./main
```

`./` indicates program inside **current directory**.

---

# 13. CMake Build System

## Definition

**CMake** is a **cross-platform build system generator**.

It automatically creates:

* Makefiles
* Visual Studio projects
* Other build systems.

---

# 14. Installing CMake

```
sudo apt-get install cmake
```

---

# 15. CMakeLists.txt Example

```cmake
cmake_minimum_required(VERSION 3.0)

set(CMAKE_BUILD_TYPE Release)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")

project(main)

add_executable(
       main
       add.cpp
       main.cpp
)
```

---

# 16. Explanation of CMake Commands

## Minimum Version

```cmake
cmake_minimum_required(VERSION 3.0)
```

Ensures CMake version ≥ **3.0**.

---

## Build Type

```cmake
set(CMAKE_BUILD_TYPE Release)
```

Build mode options:

| Mode    | Purpose           |
| ------- | ----------------- |
| Debug   | Debugging         |
| Release | Optimized program |

---

## C++ Standard

```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")
```

Uses **C++14 standard**.

---

## Project Name

```cmake
project(main)
```

Defines project identifier.

---

## Create Executable

```cmake
add_executable(
       main
       add.cpp
       main.cpp
)
```

Meaning:

Create executable **main** using:

```
add.cpp
main.cpp
```

Equivalent compile command:

```
g++ add.cpp main.cpp -o main
```

---

# 17. CMake Build Process

## Step 1 — Create Build Folder

```
mkdir build
```

Purpose:

Keep **compiled files separate** from source code.

---

## Step 2 — Move to Build Directory

```
cd build
```

---

## Step 3 — Generate Makefile

```
cmake ..
```

Explanation:

```
..  → parent directory (contains CMakeLists.txt)
```

Process:

```
CMakeLists.txt
      ↓
CMake
      ↓
Makefile
```

---

## Step 4 — Compile Project

```
make
```

Result:

```
main (executable)
```

---

## Step 5 — Run Program

```
./main
```

---

# 18. Makefile vs CMake

| Feature     | Makefile                | CMake                   |
| ----------- | ----------------------- | ----------------------- |
| Platform    | Mainly Linux            | Cross-platform          |
| Complexity  | Manual rules            | Automatic generation    |
| Maintenance | Hard for large projects | Easier                  |
| Use Case    | Small programs          | Large software projects |

---

# 19. Key Principles

### Dependency Tracking

Make rebuilds files **only if source changed**.

### Automation

Build steps are automated instead of manual compilation.

### Modular Compilation

Large programs split into multiple files.

### Reusability

Build scripts reused across systems.

---

# 20. Quick Revision Summary

* **Makefile** automates C++ compilation using `make`.
* Structure: `target : dependencies`.
* `g++` compiles `.cpp` files into `.o` object files.
* Linking combines `.o` files into executable.
* `make clean` removes compiled files.
* **CMake** generates Makefiles automatically.
* `CMakeLists.txt` defines project configuration.
* Build process: `cmake .. → make → ./program`.
* Build folder keeps compiled files separate.

---
