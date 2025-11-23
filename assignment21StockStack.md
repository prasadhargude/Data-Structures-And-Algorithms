# Assignment 21: Stock Price Tracker using Stack (Linked List) (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement
2. System Requirements
3. Data Structure & Design
4. Algorithms (pseudocode + complexity)
5. C++ Implementation (complete, production-ready)
6. Compilation & Run Instructions
7. Example Runs and Test Cases (with expected outputs)
8. Memory Management & Edge Cases
9. Extensions and Enhancements
10. References

---

## 1. Problem Statement
Build a simple **stock price tracker** that keeps a history of daily stock prices entered by the user.  
To allow users to go back and view or remove the most recent price, implement a **stack** using a **linked list** to store integer prices.

The stack must support:
1. `record(price)` — push a new stock price onto the stack.
2. `remove()` — pop and return the most recent price.
3. `latest()` — peek at the most recent price without removing it.
4. `isEmpty()` — check whether the stack is empty.

All code should follow the `_prh` variable naming convention.

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)  
- **Data structure:** Singly linked list implementing a stack (LIFO)  
- **Input/Output:** Console-based, menu-driven program  
- **Portability:** Compiles with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

### Node
Each node stores a single integer price and pointer to the next node:
```cpp
struct Node_prh {
    int price_prh;
    Node_prh* next_prh;
    Node_prh(int p) : price_prh(p), next_prh(nullptr) {}
};
```

### StockStack_prh
A simple stack wrapper that stores a pointer to the top node (`top_prh`) and offers:

- `void record_prh(int price)` — push price
- `int remove_prh()` — pop and return top price (throws/returns sentinel on empty)
- `int latest_prh()` — peek top price
- `bool isEmpty_prh()` — true if stack empty
- `void display_prh()` — utility to show stack contents (top -> bottom)
- `void clear_prh()` — free memory

Design rationale:
- Using singly linked list gives O(1) push/pop operations.
- `top_prh` points to most recent price (stack top).

---

## 4. Algorithms (pseudocode + complexity)

### record_prh(price)
```
node = new Node(price)
node.next = top
top = node
Time: O(1)
```

### remove_prh()
```
if top == NULL: report empty / return sentinel
val = top.price
tmp = top
top = top.next
delete tmp
return val
Time: O(1)
```

### latest_prh()
```
if top == NULL: report empty / return sentinel
return top.price
Time: O(1)
```

### isEmpty_prh()
```
return top == NULL
Time: O(1)
```

Space: O(n) where n = number of recorded prices.

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment21_stock_stack_prasad_hargude.cpp
// C++11 — Stock Price Tracker using Stack implemented with linked list
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <limits>
#include <stdexcept>

using namespace std;

struct Node_prh {
    int price_prh;
    Node_prh* next_prh;
    Node_prh(int p) : price_prh(p), next_prh(nullptr) {}
};

class StockStack_prh {
private:
    Node_prh* top_prh;

public:
    StockStack_prh() : top_prh(nullptr) {}
    ~StockStack_prh() { clear_prh(); }

    // Push a new price
    void record_prh(int price_prh) {
        Node_prh* node = new Node_prh(price_prh);
        node->next_prh = top_prh;
        top_prh = node;
    }

    // Pop and return top price. Throws runtime_error if empty.
    int remove_prh() {
        if (!top_prh) throw runtime_error("Stack is empty");
        Node_prh* tmp = top_prh;
        int val = tmp->price_prh;
        top_prh = top_prh->next_prh;
        delete tmp;
        return val;
    }

    // Peek top price without removing. Throws runtime_error if empty.
    int latest_prh() const {
        if (!top_prh) throw runtime_error("Stack is empty");
        return top_prh->price_prh;
    }

    bool isEmpty_prh() const {
        return top_prh == nullptr;
    }

    // Display stack content from top to bottom (for debugging / output)
    void display_prh() const {
        if (!top_prh) { cout << "(empty)"; return; }
        Node_prh* cur = top_prh;
        cout << "Top -> ";
        while (cur) {
            cout << cur->price_prh;
            if (cur->next_prh) cout << " -> ";
            cur = cur->next_prh;
        }
    }

    // Free nodes
    void clear_prh() {
        Node_prh* cur = top_prh;
        while (cur) {
            Node_prh* nxt = cur->next_prh;
            delete cur;
            cur = nxt;
        }
        top_prh = nullptr;
    }
};

// Helper to clear stdin on invalid input
static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 21: Stock Price Tracker using Stack (Linked List)\n";
    StockStack_prh tracker;
    while (true) {
        cout << "\nMenu:\n";
        cout << "1. record(price)  — add new price\n";
        cout << "2. remove()       — remove and return latest price\n";
        cout << "3. latest()       — view latest price\n";
        cout << "4. isEmpty()      — check if empty\n";
        cout << "5. Display stack  — show all recorded prices\n";
        cout << "6. Exit\n";
        cout << "Enter choice: ";
        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        try {
            if (ch == 1) {
                int p; cout << "Enter price (integer): "; cin >> p;
                tracker.record_prh(p);
                cout << "Recorded " << p << "\n";
            } else if (ch == 2) {
                int v = tracker.remove_prh();
                cout << "Removed latest price: " << v << "\n";
            } else if (ch == 3) {
                int v = tracker.latest_prh();
                cout << "Latest price: " << v << "\n";
            } else if (ch == 4) {
                cout << (tracker.isEmpty_prh() ? "Stack is empty\n" : "Stack is not empty\n");
            } else if (ch == 5) {
                tracker.display_prh(); cout << "\n";
            } else if (ch == 6) {
                cout << "Exiting...\n"; break;
            } else {
                cout << "Invalid choice\n";
            }
        } catch (const exception &e) {
            cout << "Error: " << e.what() << "\n";
        }
    }
    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile with g++ (C++11):

```bash
g++ -std=c++11 -O2 assignment21_stock_stack_prasad_hargude.cpp -o assignment21_stock_stack
./assignment21_stock_stack
```

---

## 7. Example Runs and Test Cases (with expected outputs)

### Example 1 — Basic sequence
User actions:
```
record(100)
record(105)
record(102)
latest() -> expect 102
remove() -> returns 102
latest() -> expect 105
isEmpty() -> false
```

Console trace (expected):
```
Recorded 100
Recorded 105
Recorded 102
Latest price: 102
Removed latest price: 102
Latest price: 105
Stack is not empty
Top -> 105 -> 100
```

### Example 2 — Empty stack error handling
Actions:
```
remove()  // on empty
latest()  // on empty
```
Expected:
```
Error: Stack is empty
Error: Stack is empty
```

### Example 3 — Single element
Actions:
```
record(200)
latest() -> 200
remove() -> 200
isEmpty() -> true
```

---

## 8. Memory Management & Edge Cases
- `clear_prh()` frees all nodes in destructor to avoid memory leaks.
- All stack operations are O(1).
- `remove_prh` and `latest_prh` throw `runtime_error` when called on empty stack — the main loop catches and reports the error.
- Input validation: invalid non-integer menu inputs are handled.

---

## 9. Extensions and Enhancements
- Persist stack to file to retain history between runs.
- Add timestamps with prices (store struct with price + date).
- Implement bounded stack with capacity (discard oldest when full).
- Add undo/redo features or visualization.

---

## 10. References
- Standard C++ (std::exception, iostream)
- Data structure textbooks: Stack implemented with linked list
