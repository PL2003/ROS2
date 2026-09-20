# ROS 2 Custom Interfaces & Service/Client Communication

## 1. Header Block

| Field | Value |
|---|---|
| **Topic** | Defining Custom Interfaces (msg/srv) and Implementing Service/Client Nodes in ROS 2 |
| **Prerequisites** | (1) Basic ROS 2 workspace setup (`colcon`, `ros2_ws`) · (2) Familiarity with ROS 2 packages (`package.xml`, `CMakeLists.txt`) · (3) Basic C++ or Python syntax · (4) Understanding of ROS 2 nodes and topics |
| **Study Time** | 45–60 minutes |
| **Difficulty** | Intermediate |

---

## 2. Concept Map

- ROS 2 communication (topics, services, actions) is built on **interfaces** — strongly-typed data contracts defined in `.msg`, `.srv`, and `.action` files.
- A **service** is a synchronous, request/response communication pattern, distinct from the asynchronous, many-to-many **topic** pattern.
- Custom interfaces let developers define domain-specific data structures (e.g., nested types, arrays of structs) instead of relying only on built-in types like `std_msgs`.
- Interfaces are compiled by `rosidl` into language-specific bindings (C++, Python) so both server and client can reference the same generated type.
- The `cpp_srvcli` package demonstrates the canonical service/client pattern: a **server** node registers a callback that computes a result, and a **client** node sends a request and awaits the response.
- Custom `.msg` files can contain **arrays of custom types** (e.g., a "Contact" message nested inside an "AddressBook" message), enabling structured, repeated data.
- Building with `colcon build --merge-install --packages-select <pkg>` compiles only the targeted package, then `ros2 run` launches the resulting executables from separate terminals.

---

## 3. Core Theory

### 3.1 Interfaces as Data Contracts

A ROS 2 **interface** is a formal, language-agnostic definition of a data structure used for communication between nodes. There are three interface types:

1. **`.msg`** — defines a message structure for **topics** (publish/subscribe).
2. **`.srv`** — defines a **request** and **response** pair for **services** (synchronous call/return).
3. **`.action`** — defines **goal**, **result**, and **feedback** for long-running, preemptible tasks.

> **Insight Box:** Think of an interface as a *form template*. A `.msg` file is like a form with fixed fields (name, age, address) — anyone filling it out or reading it agrees on the field order and types. A `.srv` file is like a two-part form: a *request slip* you hand in, and a *receipt* you get back.

### 3.2 Service/Client Communication Model

Unlike topics (continuous, decoupled streaming), a **service** follows a **request–response** model:

- The **server** advertises a service name and waits for requests.
- The **client** sends a request and **blocks (or awaits asynchronously)** until a response arrives.
- Exactly one server typically handles a given request (unlike topics, which can have many subscribers).

This can be expressed conceptually as:

```
Client --request--> Server
Client <--response-- Server
```

### 3.3 Nested and Array Fields in Custom Messages

Custom `.msg` files support:

1. **Primitive fields** (`string`, `int32`, `float64`, etc.)
2. **Nested custom types** — one message referencing another (e.g., `Contact` used inside `AddressBook`).
3. **Arrays of custom types** — e.g., `Contact[] contacts`, allowing a single message to carry a *collection* of structured records.

For a bounded/unbounded array of `N` elements of a nested type, the memory and serialization cost scales as:

$$
S_{total} = \sum_{i=1}^{N} S(\text{Contact}_i)
$$

where $S(\text{Contact}_i)$ is the serialized size of the $i$-th `Contact` element and $N$ is the number of entries pushed into the array (e.g., via `push_back`).

### 3.4 Build and Execution Flow

1. **Define** the interface (`.msg`/`.srv`) in a `msg/` or `srv/` folder.
2. **Register** it in `CMakeLists.txt` (via `rosidl_generate_interfaces`) and `package.xml` dependencies.
3. **Build** the specific package with `colcon build --merge-install --packages-select <pkg>`.
4. **Source** the workspace overlay (`source install/setup.bash`) in every new terminal.
5. **Run** server and client executables in separate terminals so they can communicate over the ROS 2 graph.

> **Insight Box:** `--merge-install` is like keeping all your tools in one shared toolbox (`install/`) instead of a separate toolbox per package — it simplifies sourcing but rebuilds can take slightly longer since it touches a shared install tree.

---

## 4. Visual Scaffolding

### 4.1 Comparison Table — Topics vs. Services

| Feature | Topic (Pub/Sub) | Service (Req/Resp) |
|---|---|---|
| Communication style | Asynchronous, streaming | Synchronous request/response |
| Cardinality | Many publishers, many subscribers | Typically one server, one client per call |
| Use case | Continuous sensor data, state updates | On-demand computation (e.g., `add_two_ints`) |
| Blocking behavior | Non-blocking | Client waits (or awaits) for a result |
| Interface file | `.msg` | `.srv` (contains request + response) |

### 4.2 Summary Table — Custom Interface File Types

| File Type | Purpose | Structure | Example Use |
|---|---|---|---|
| `.msg` | Topic payload definition | Flat or nested fields, arrays | `AddressBook.msg` containing `Contact[]` |
| `.srv` | Service request/response | Two sections separated by `---` | `AddTwoInts.srv` (`int64 a`, `int64 b` \| `int64 sum`) |
| `.action` | Long-running task definition | Goal / Result / Feedback, separated by `---` | Navigation, manipulation tasks |

### 4.3 Diagram Description — Service Call Sequence

Imagine a horizontal timeline diagram with two vertical lifelines:

- **Left lifeline**, labeled `Client Node (cpp_srvcli client)`, positioned at `x = 100`.
- **Right lifeline**, labeled `Server Node (cpp_srvcli server)`, positioned at `x = 500`.
- An arrow from `(100, 50)` to `(500, 50)` labeled **"Request: a=2, b=3"** — solid line, arrowhead pointing right.
- A small box at `(500, 100)` labeled **"Compute sum = a + b"** on the server lifeline.
- A dashed arrow from `(500, 150)` back to `(100, 150)` labeled **"Response: sum=5"** — dashed line, arrowhead pointing left.
- Time flows top-to-bottom along both lifelines.

This mirrors classic UML sequence diagrams: solid arrows for requests, dashed arrows for responses.

---

## 5. Step-by-Step Procedure: Creating and Using a Custom Interface

| Step | Action | Purpose/Result |
|---|---|---|
| 1 | Create a `msg/` (or `srv/`) folder inside your package | Houses interface definition files |
| 2 | Write the `.msg` file (e.g., define `Contact` with `first_name`, `last_name`, `phone_number`, `phone_type`) | Declares the data contract's fields and types |
| 3 | Reference nested/array types (e.g., `Contact[] address_book` in `AddressBook.msg`) | Enables structured, repeated records in one message |
| 4 | Add `rosidl_generate_interfaces(${PROJECT_NAME} "msg/Contact.msg" "msg/AddressBook.msg")` to `CMakeLists.txt` | Tells the build system to generate language bindings |
| 5 | Add `rosidl_default_generators` and `rosidl_default_runtime` to `package.xml` | Declares build/runtime dependencies for interface generation |
| 6 | Build with `colcon build --merge-install --packages-select <pkg>` | Compiles only the target package, generating C++/Python bindings |
| 7 | Source the workspace: `source install/setup.bash` in **every new terminal** | Makes the new executables and interfaces discoverable |
| 8 | **Decision point:** Is this a service or a topic? → If service, generate request/response classes; if topic, generate message class only | Determines which generated API (`Request`/`Response` vs. plain message) you use in code |
| 9 | In code, instantiate the type (e.g., `rosidl_tutorials_msgs::msg::Contact contact;`), set fields, then `push_back` into the array | Populates the structured data before publishing |
| 10 | Run server and client in separate sourced terminals: `ros2 run cpp_srvcli server` / `ros2 run cpp_srvcli client 2 3 1` | Executes the communication and observes request/response or message output |

---

## 6. Worked Example

**Problem:** Using the `cpp_srvcli` package's `AddTwoInts` service, a client sends the integers `a = 2`, `b = 3` (with an extra argument `1`, e.g., representing a call count or flag). Trace the request/response cycle and compute the result.

**Step 1 — Identify the interface.**
The service interface `AddTwoInts.srv` defines:
```
int64 a
int64 b
---
int64 sum
```
The top section (`a`, `b`) is the **request**; the section after `---` (`sum`) is the **response**.

**Step 2 — Client constructs and sends the request.**
The client node parses command-line arguments `2` and `3`, assigns them to `request->a = 2` and `request->b = 3`, then calls the service asynchronously.

**Step 3 — Server receives the request and computes.**
The server's registered callback executes:
$$
\text{sum} = a + b = 2 + 3 = 5
$$

**Step 4 — Server sends the response.**
The server populates `response->sum = 5` and returns it over the service connection.

**Step 5 — Client receives and logs the result.**
The client's callback (or blocking future) receives `sum = 5` and typically logs:
```
Result of add_two_ints: 5
```

**Final Answer:**
```
sum = 5
```

### Verification

Re-derive independently: request fields were `a=2`, `b=3`. Since the service contract guarantees `sum = a + b`, substitute directly: $2 + 3 = 5$. This matches the logged output, confirming the request was correctly transmitted, processed, and returned — no fields were dropped or misordered in serialization.

---

## 7. Key Formulas / Cheat Sheet

```
Service result:        sum = a + b
Serialized array size: S_total = Σ S(element_i),  i = 1..N
Build command:          colcon build --merge-install --packages-select <pkg>
Source command:         source install/setup.bash
Run server:             ros2 run <pkg> server
Run client:              ros2 run <pkg> client <arg1> <arg2> [...]
CMake registration:     rosidl_generate_interfaces(${PROJECT_NAME} "msg/<Name>.msg" "srv/<Name>.srv")
```

---

## 8. FAQ / Misconceptions

**Q1: Why do we need custom interfaces instead of just using built-in message types?**
A: Built-in types (`std_msgs`, `geometry_msgs`) are generic and don't capture domain-specific structure. Custom interfaces (like `Contact` or `AddressBook`) let you group related fields, nest nested types, and use arrays — producing type-safe, self-documenting data contracts specific to your application.

**Q2: What is the difference between a `.msg` file and a `.srv` file?**
A: A `.msg` file defines a single, one-way data structure used with topics (publish/subscribe). A `.srv` file defines **two** structures — a request and a response — separated by `---`, used for synchronous service calls where a client expects a specific reply.

**Q3: What is the most common mistake when building custom interfaces?**
A: Forgetting to re-source the workspace (`source install/setup.bash`) in **every** terminal after rebuilding — this causes "package not found" or stale-type errors even though the build succeeded, because the shell's environment still points to the old overlay.

**Q4: How does this connect to the broader ROS 2 communication model?**
A: Custom interfaces are the shared vocabulary underlying **all three** ROS 2 communication patterns — topics, services, and actions. Mastering interface definition is foundational before moving to more complex patterns like actions, which combine goal/result/feedback interfaces with service-like and topic-like semantics simultaneously.

---

## 9. Comparison Table — Interface Definition Approaches

| Feature | Flat `.msg` (primitives only) | Nested `.msg` (custom type field) | Array of Nested `.msg` |
|---|---|---|---|
| Complexity | Low | Medium | Medium–High |
| Example | `Pose.msg` with `x, y, z` | `AddressBook.msg` with one `Contact contact` | `AddressBook.msg` with `Contact[] address_book` |
| Use case | Simple sensor readings | Single structured record | Collections of structured records (e.g., contact lists) |
| Best used when | Data has no internal grouping | One instance of a compound object is needed | Multiple instances of the same compound object are needed |

---

## 10. Practice Checklist

- [ ] Can I explain the difference between a topic and a service in my own words?
- [ ] Can I write a `.srv` file with a request and response section from scratch?
- [ ] Can I define a `.msg` file that nests one custom type inside another?
- [ ] Can I add an array field of a custom type (e.g., `Contact[] contacts`) to a message?
- [ ] Can I correctly register a new interface in both `CMakeLists.txt` and `package.xml`?
- [ ] Can I explain why `source install/setup.bash` must be re-run after every build?
- [ ] Can I trace a full request/response cycle for `AddTwoInts` and compute the result manually?
- [ ] Can I identify the "decision point" between choosing a `.msg` vs. `.srv` for a new communication need?
