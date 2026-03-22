# Libzork - C++ Interactive Fiction Engine

> **Showcase Repository:** To comply with EPITA's anti-plagiarism regulations, the raw source code of this project is kept private. This repository serves as an architectural showcase documenting the engine design, the object-oriented structure, and the core graph implementation.

## 📖 Project Context

**Libzork** is a C++ development project. The goal is to build a modular engine capable of parsing and executing "Choose Your Own Adventure" stories (inspired by the classic text-based game *Zork*). The engine reads story files, builds a graph representation in memory, and executes it through a Command Line Interface (CLI).

## 🏗️ Architectural Overview

The core architecture heavily relies on Modern C++ idioms (smart pointers, move semantics) and strong Object-Oriented principles. The logic is cleanly separated into three main components:

### 1. Graph-Based Story Engine (`Story`, `Node`, `Choice`)
The narrative is represented as a directed graph:
* **Nodes** represent the different scenes or rooms.
* **Choices** represent the directed edges linking the nodes.
The graph is safely managed in memory using `std::unique_ptr` and `std::shared_ptr`. This prevents memory leaks during complex cyclic story paths while clearly defining ownership.

### 2. State & Variable Management (`Store`)
The engine supports dynamic gameplay through a central `Store` component. 
* It uses standard C++ containers (like `std::map<std::string, int>`) to track the player's inventory and triggered flags across the story.
* This allows the game to maintain a persistent state, tracking if a player has acquired a specific item before allowing them to unlock a specific node.

### 3. Interactive Execution (`InteractiveRunner`)
The execution logic is entirely decoupled from the story data. The `InteractiveRunner` is responsible for parsing user inputs via standard input streams (`std::istream`), displaying the current node's description to the standard output (`std::ostream`), and traversing the graph based on the selected valid choices.

## 🚀 Technical Highlights

### Safe Memory Management & Factory Patterns
The project strictly prohibits raw `new`/`delete`. Object creation is encapsulated within factory functions returning unique pointers, ensuring exception safety and clean boundaries:

```cpp
// Example of the Factory pattern used to instantiate the active store
namespace libzork::store {
    std::unique_ptr<Store> make_store() {
        return std::make_unique<StoreImpl>();
    }
}
```

### Decoupled Logic
By decoupling the `Story` (data structure) from the `Runner` (execution context), the engine is highly extensible. Adding a new interface (like a GUI or a Web output) simply requires implementing a new runner class without altering a single line of the core graph logic.

## 🎯 Demonstration

The engine is compiled into a standalone binary `zorkxplorer` using CMake.

### Playing a story (Interactive Mode)
```bash
./zorkxplorer --story path/to/story.yml
```

*Expected behavior: The terminal prints the room description and waits for the user to type the number corresponding to their choice, navigating through the graph until an end node is reached.*
