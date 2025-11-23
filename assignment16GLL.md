# Assignment 16: Set Implementation Using a Generalized Linked List (GLL) (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement
2. System Requirements
3. Data Structure & Design
4. Algorithms (pseudocode + complexity)
5. C++ Implementation (complete, production-ready)
6. Compilation & Run Instructions
7. Example Runs and Test Cases
8. Memory Management & Edge Cases
9. Extensions and Enhancements
10. References

---

## 1. Problem Statement
Write a C++ program to implement a **Set** using a **Generalized Linked List (GLL)**.  
Store and display nested sets (sets that contain atoms and sub-sets) using a GLL.

**Example:**
Let  
S = { p, q, { r, s, t, {}, { u, v }, w, x, { y, z }, a1, b1 } }

Store this structure using a Generalized Linked List and display the elements in correct set-notation format.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)
- **Data structure:** Generalized Linked List (GLL) implemented with nodes that can be either atom or sublist
- **Input/Output:** Console-based; program builds the example structure and can also accept simple manual construction in code
- **Portability:** Compiles with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

### Node Types
A GLL node represents either:
- an **atom** (a string value like "p", "q", "a1"), or
- a **sublist** (pointer to another list)

We implement using "first-child / next-sibling" representation commonly used for generalized lists:

```cpp
struct GNode_prh {
    bool isAtom_prh;        // true => atom, false => sublist
    std::string atom_prh;   // valid if isAtom_prh == true
    GNode_prh* child_prh;   // pointer to first element if it's a sublist (NULL for empty sublist)
    GNode_prh* next_prh;    // pointer to next element in the same list level
    GNode_prh(bool atom, const std::string &val)
      : isAtom_prh(atom), atom_prh(val), child_prh(nullptr), next_prh(nullptr) {}
};
```

Representation details:
- A list is represented by a pointer to its first element (head).
- Each element is either an atom node (`isAtom_prh == true`) with `atom_prh` set, or a sublist node (`isAtom_prh == false`) with `child_prh` pointing to the head of the nested list.
- `next_prh` links siblings in the same enclosing list.

---

## 4. Algorithms (pseudocode + complexity)

### A. Build Example (manual construction)
We will create nodes and link them to form the nested structure for S explicitly. This is O(N) in number of nodes.

### B. Display (print in set notation)
Recursive procedure to print a list:
```
printList(head):
    print "{"
    cur = head
    first = true
    while cur:
        if not first: print ", "
        if cur.isAtom: print cur.atom
        else: printList(cur.child)
        cur = cur.next
        first = false
    print "}"
```
Time complexity: O(N) where N is total number of nodes.

### C. Free memory (destructor)
Recursively traverse and `delete` nodes. Time O(N).

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment16_GLL_prh.cpp
// C++11 — Generalized Linked List implementation for nested sets
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <vector>
#include <memory>

struct GNode_prh {
    bool isAtom_prh;           // true -> atom, false -> sublist
    std::string atom_prh;      // valid if isAtom_prh
    GNode_prh* child_prh;      // head of nested list (if sublist)
    GNode_prh* next_prh;       // next sibling in same list
    GNode_prh(bool atom = true, const std::string &val = "")
        : isAtom_prh(atom), atom_prh(val), child_prh(nullptr), next_prh(nullptr) {}
};

// Utility: create atom node
GNode_prh* makeAtom_prh(const std::string &val) {
    return new GNode_prh(true, val);
}

// Utility: create empty sublist node (child_prh == nullptr)
GNode_prh* makeSublist_prh(GNode_prh* child = nullptr) {
    GNode_prh* node = new GNode_prh(false, "");
    node->child_prh = child;
    return node;
}

// Append a node to the end of a list pointed to by headPtr (may be null)
void appendNode_prh(GNode_prh*& headPtr, GNode_prh* node) {
    if (!headPtr) {
        headPtr = node;
        return;
    }
    GNode_prh* cur = headPtr;
    while (cur->next_prh) cur = cur->next_prh;
    cur->next_prh = node;
}

// Print list in set notation { ... }
void printList_prh(GNode_prh* head) {
    std::cout << "{";
    GNode_prh* cur = head;
    bool first = true;
    while (cur) {
        if (!first) std::cout << ", ";
        if (cur->isAtom_prh) {
            std::cout << cur->atom_prh;
        } else {
            // sublist
            printList_prh(cur->child_prh);
        }
        first = false;
        cur = cur->next_prh;
    }
    std::cout << "}";
}

// Recursively delete entire list
void deleteList_prh(GNode_prh* head) {
    GNode_prh* cur = head;
    while (cur) {
        if (!cur->isAtom_prh && cur->child_prh) {
            deleteList_prh(cur->child_prh);
        }
        GNode_prh* nxt = cur->next_prh;
        delete cur;
        cur = nxt;
    }
}

// Build the example:
// S = { p, q, { r, s, t, {}, { u, v }, w, x, { y, z }, a1, b1 } }
GNode_prh* buildExample_prh() {
    // Top-level list head
    GNode_prh* S_head = nullptr;

    // p
    appendNode_prh(S_head, makeAtom_prh("p"));
    // q
    appendNode_prh(S_head, makeAtom_prh("q"));

    // Build the nested sublist for the third element
    GNode_prh* sub_head = nullptr;
    appendNode_prh(sub_head, makeAtom_prh("r"));
    appendNode_prh(sub_head, makeAtom_prh("s"));
    appendNode_prh(sub_head, makeAtom_prh("t"));

    // empty sublist {}
    GNode_prh* empty_sub = makeSublist_prh(nullptr);
    appendNode_prh(sub_head, empty_sub);

    // sub-sublist { u, v }
    GNode_prh* uv = nullptr;
    appendNode_prh(uv, makeAtom_prh("u"));
    appendNode_prh(uv, makeAtom_prh("v"));
    appendNode_prh(sub_head, makeSublist_prh(uv));

    appendNode_prh(sub_head, makeAtom_prh("w"));
    appendNode_prh(sub_head, makeAtom_prh("x"));

    // { y, z }
    GNode_prh* yz = nullptr;
    appendNode_prh(yz, makeAtom_prh("y"));
    appendNode_prh(yz, makeAtom_prh("z"));
    appendNode_prh(sub_head, makeSublist_prh(yz));

    appendNode_prh(sub_head, makeAtom_prh("a1"));
    appendNode_prh(sub_head, makeAtom_prh("b1"));

    // Append the sublist as an element of S
    appendNode_prh(S_head, makeSublist_prh(sub_head));

    return S_head;
}

int main() {
    std::cout << "Assignment 16: Generalized Linked List (GLL) - Set Representation\n";
    GNode_prh* S = buildExample_prh();
    std::cout << "The set S is: ";
    printList_prh(S);
    std::cout << std::endl;

    // Clean up
    deleteList_prh(S);
    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile with g++ (C++11):

```bash
g++ -std=c++11 -O2 assignment16_GLL_prh.cpp -o assignment16_GLL_prh
./assignment16_GLL_prh
```

---

## 7. Example Runs and Test Cases

### Expected output (for the provided example):
```
The set S is: {p, q, {r, s, t, {}, {u, v}, w, x, {y, z}, a1, b1}}
```

### Additional tests:
- Empty top-level set (print `{}`).
- Sublist with nested depth > 2.
- Elements with multi-character atom names (e.g., `a1`, `b1`) — supported.

---

## 8. Memory Management & Edge Cases
- `deleteList_prh` recursively frees nested sublists and siblings.
- The implementation avoids memory leaks; every created node is deleted.
- Empty sublists are represented by sublist node whose `child_prh == nullptr`.
- Atoms may contain alphanumeric characters; whitespace handling is done at construction time in code.

---

## 9. Extensions and Enhancements
- **Parser:** Build a parser to read set notation input like `{p,q,{r,s}}` and construct GLL automatically.
- **Set operations:** Implement union, intersection, difference for generalized sets.
- **Pretty printing:** Pretty-format nested sets with indentation and line breaks.
- **Persistence:** Save/load GLL to/from a file (JSON-like format).

---

## 10. References
- Data structures literature on Generalized Linked Lists and first-child/next-sibling representations.
- C++ reference for memory management and recursion.

---
