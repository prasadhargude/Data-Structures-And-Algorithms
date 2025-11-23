# Assignment 23: Multiple Stacks Using a Single Array (In-Depth)
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
Write a C++ program that implements **multiple stacks** (more than two) using a **single array**. Provide the following operations for each stack:

A. `push(stackId, value)` — push value on given stack  
B. `pop(stackId)` — pop and return top value from given stack  
C. `handle Stack Overflow` — when array is full, report overflow for push  
D. `handle Stack Underflow` — when a stack is empty and pop is requested  
E. `display(stackId)` — print contents of a given stack (top to bottom or bottom to top)

You should support `k` stacks using a single array of size `n` (user-specified or pre-defined), using efficient bookkeeping so multiple stacks share the array space.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Implementation approaches supported:**  
  - Fixed division (n/k slots per stack) — simple, but inefficient when stacks are unbalanced.  
  - Flexible / dynamic (efficient) approach using additional arrays for next indices and free list — recommended.  
- **Data structures used:** arrays and integer indices (no STL containers required for core logic)  
- **Input/Output:** Console-based, menu-driven program  
- **Portability:** Compiles with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

We implement the **flexible** approach (also known as k stacks in single array with free list), which supports dynamic growth of any stack until the total array is exhausted.

Arrays used:
- `int arr_prh[n]` — storage for actual values
- `int top_prh[k]` — index of top element for each stack (initially -1)
- `int next_prh[n]` — for each index, holds next index in the stack or index of next free slot
- `int free_prh` — beginning index of free list (initially 0)

Initialization:
- `for i in 0..n-2: next_prh[i] = i+1`
- `next_prh[n-1] = -1`
- `free_prh = 0`
- All `top_prh[i] = -1`

Push operation (stackId, value):
1. If `free_prh == -1` → Overflow (array full)
2. `i = free_prh` (take free slot)
3. `free_prh = next_prh[i]` (update free list)
4. `arr_prh[i] = value`
5. `next_prh[i] = top_prh[stackId]` (link new element to previous top)
6. `top_prh[stackId] = i`

Pop operation (stackId):
1. If `top_prh[stackId] == -1` → Underflow (stack empty)
2. `i = top_prh[stackId]`
3. `top_prh[stackId] = next_prh[i]`
4. `next_prh[i] = free_prh` (add this slot to free list)
5. `free_prh = i`
6. return `arr_prh[i]`

Display(stackId):
- Traverse from `top_prh[stackId]` following `next_prh` and print values.

Design rationale:
- This method allows any stack to grow as long as there is free space in the array.
- All operations are O(1).

---

## 4. Algorithms (pseudocode + complexity)

### Initialization (O(n + k))
```
for i=0..k-1: top[i] = -1
for i=0..n-2: next[i] = i+1
next[n-1] = -1
free = 0
```

### push(stackId, value) — O(1)
```
if free == -1: overflow
i = free
free = next[i]
arr[i] = value
next[i] = top[stackId]
top[stackId] = i
```

### pop(stackId) — O(1)
```
if top[stackId] == -1: underflow
i = top[stackId]
top[stackId] = next[i]
next[i] = free
free = i
return arr[i]
```

### display(stackId) — O(size of stack)
```
i = top[stackId]
while i != -1:
    print arr[i]
    i = next[i]
```

Space: O(n + k) for arrays.

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment23_multiple_stacks_prasad_hargude.cpp
// C++11 — K stacks in a single array (flexible method with free list)
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <limits>
#include <stdexcept>

using namespace std;

class KStacks_prh {
private:
    int n_prh; // size of array
    int k_prh; // number of stacks
    int *arr_prh;   // values
    int *top_prh;   // top indices for k stacks
    int *next_prh;  // next indices for free list and stack links
    int free_prh;   // beginning index of free list

public:
    KStacks_prh(int k, int n): n_prh(n), k_prh(k) {
        arr_prh = new int[n_prh];
        top_prh = new int[k_prh];
        next_prh = new int[n_prh];

        // initialize all stacks as empty
        for (int i = 0; i < k_prh; ++i) top_prh[i] = -1;

        // initialize free list
        for (int i = 0; i < n_prh - 1; ++i) next_prh[i] = i + 1;
        next_prh[n_prh - 1] = -1;
        free_prh = 0;
    }

    ~KStacks_prh() {
        delete[] arr_prh;
        delete[] top_prh;
        delete[] next_prh;
    }

    bool isFull_prh() const { return free_prh == -1; }
    bool isEmpty_prh(int stackId) const {
        validateStackId_prh(stackId);
        return top_prh[stackId] == -1;
    }

    void validateStackId_prh(int stackId) const {
        if (stackId < 0 || stackId >= k_prh) throw out_of_range("Invalid stackId");
    }

    // Push value onto stack stackId (0-based)
    void push_prh(int stackId, int value) {
        validateStackId_prh(stackId);
        if (isFull_prh()) {
            throw overflow_error("Stack overflow: no space available to push");
        }
        int i = free_prh;          // index to insert
        free_prh = next_prh[i];    // update free to next free slot
        arr_prh[i] = value;        // store value
        next_prh[i] = top_prh[stackId]; // link to previous top
        top_prh[stackId] = i;      // update top
    }

    // Pop from stack stackId and return value
    int pop_prh(int stackId) {
        validateStackId_prh(stackId);
        if (isEmpty_prh(stackId)) {
            throw underflow_error("Stack underflow: stack is empty");
        }
        int i = top_prh[stackId];
        top_prh[stackId] = next_prh[i]; // move top to next
        next_prh[i] = free_prh;         // add this slot to free list
        free_prh = i;
        return arr_prh[i];
    }

    // Display stack contents from top to bottom
    void display_prh(int stackId) const {
        validateStackId_prh(stackId);
        if (isEmpty_prh(stackId)) {
            cout << "(empty)\n";
            return;
        }
        int i = top_prh[stackId];
        cout << "Top -> ";
        while (i != -1) {
            cout << arr_prh[i];
            if (next_prh[i] != -1) cout << " -> ";
            i = next_prh[i];
        }
        cout << "\n";
    }

    // For debugging: display internal arrays (arr, next, top, free)
    void debug_prh() const {
        cout << "arr:  "; for (int i = 0; i < n_prh; ++i) cout << arr_prh[i] << " "; cout << "\n";
        cout << "next: "; for (int i = 0; i < n_prh; ++i) cout << next_prh[i] << " "; cout << "\n";
        cout << "top:  "; for (int i = 0; i < k_prh; ++i) cout << top_prh[i] << " "; cout << "\n";
        cout << "free: " << free_prh << "\n";
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 23: Multiple Stacks using a Single Array\n";
    int k, n;
    cout << "Enter number of stacks (k): "; cin >> k;
    cout << "Enter total array size (n): "; cin >> n;
    if (k <= 0 || n <= 0) { cout << "Invalid sizes. Exiting.\n"; return 0; }

    KStacks_prh ks(k, n);
    while (true) {
        cout << "\nMenu:\n";
        cout << "1. push(stackId, value)\n";
        cout << "2. pop(stackId)\n";
        cout << "3. display(stackId)\n";
        cout << "4. isEmpty(stackId)\n";
        cout << "5. isFull()\n";
        cout << "6. debug (internal arrays)\n";
        cout << "7. Exit\n";
        cout << "Enter choice: "; int ch; if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }

        try {
            if (ch == 1) {
                int id, val; cout << "stackId (0-based): "; cin >> id;
                cout << "value: "; cin >> val;
                ks.push_prh(id, val);
                cout << "Pushed " << val << " on stack " << id << "\n";
            } else if (ch == 2) {
                int id; cout << "stackId (0-based): "; cin >> id;
                int v = ks.pop_prh(id);
                cout << "Popped " << v << " from stack " << id << "\n";
            } else if (ch == 3) {
                int id; cout << "stackId (0-based): "; cin >> id;
                cout << "Stack " << id << ": "; ks.display_prh(id);
            } else if (ch == 4) {
                int id; cout << "stackId (0-based): "; cin >> id;
                cout << (ks.isEmpty_prh(id) ? "Empty\n" : "Not empty\n");
            } else if (ch == 5) {
                cout << (ks.isFull_prh() ? "Array is full (global overflow)\n" : "Space available\n");
            } else if (ch == 6) {
                ks.debug_prh();
            } else if (ch == 7) {
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
g++ -std=c++11 -O2 assignment23_multiple_stacks_prasad_hargude.cpp -o assignment23_multiple_stacks
./assignment23_multiple_stacks
```

---

## 7. Example Runs and Test Cases (with expected outputs)

### Setup
```
k = 3 (three stacks)
n = 6 (array size)
```
Initial: free = 0, next = [1,2,3,4,5,-1], top = [-1,-1,-1]

### Sequence
```
push(0, 10)
push(1, 20)
push(2, 30)
push(0, 11)
push(1, 21)
push(2, 31)
```
Now array full. Any further push -> overflow.

Display stacks:
```
Stack 0: Top -> 11 -> 10
Stack 1: Top -> 21 -> 20
Stack 2: Top -> 31 -> 30
```

Pop operations:
```
pop(1) -> 21
pop(2) -> 31
push(2, 99)  // uses freed slot
```

### Overflow / Underflow
- If we push when `free == -1` → program throws overflow_error and prints "Stack overflow".
- If we pop when `top[stackId] == -1` → program throws underflow_error and prints "Stack underflow".

---

## 8. Memory Management & Edge Cases
- Arrays allocated with `new[]` are deleted in destructor to avoid memory leaks.
- Invalid `stackId` throws `out_of_range`.
- Push/pop are O(1) operations.
- Handles dynamic growth across stacks until global array capacity `n` is reached.

---

## 9. Extensions and Enhancements
- Implement a version that grows the array dynamically when full (reallocating and remapping indices).
- Provide persistence to save stacks to files.
- Implement thread-safe operations with mutexes for concurrent access.
- Offer a fixed division simpler variant (n/k per stack) as alternative.

---

## 10. References
- Data structures textbooks for stack and array-based implementations.
- Standard techniques for multiple stacks in a single array (free-list approach).
