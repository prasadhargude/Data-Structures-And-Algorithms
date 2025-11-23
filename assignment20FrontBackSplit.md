# Assignment 20: Front-Back Split of a Singly Linked List (In-Depth)

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

Given a singly linked list, split it into two sublists — one for the front half and one for the back half.
If the number of elements is odd, the extra element should go in the front list.

Examples:

* Input: {2, 3, 5, 7, 11} → Front: {2, 3, 5}, Back: {7, 11}
* Input: {1, 2, 3, 4} → Front: {1, 2}, Back: {3, 4}
* Input: {1} → Front: {1}, Back: {}

You must handle all edge cases, including lists of length 0, 1, 2, 3, etc.

---

## 2. System Requirements

* **Language:** C++ (C++11 compatible)
* **Data structure:** Singly linked list
* **Input/Output:** Console-based; menu-driven demo and test harness
* **Portability:** Compiles with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

### Node

```cpp
struct Node_prh {
    int data_prh;
    Node_prh* next_prh;
    Node_prh(int d): data_prh(d), next_prh(nullptr) {}
};
```

### List Utilities

We implement:

* `pushBack_prh(int)` — append to list
* `display_prh()` — print list
* `frontBackSplit_prh(Node_prh* source, Node_prh*& frontRef, Node_prh*& backRef)` — split function (works in O(n) time and O(1) extra space)

Design rationale:

* Use the fast/slow pointer technique (tortoise-hare) to find midpoint:

  * `slow` advances one node at a time; `fast` advances two.
  * When `fast` reaches end (or null), `slow` is at midpoint.
  * For odd lengths, front gets the extra node.

---

## 4. Algorithms (pseudocode + complexity)

### FrontBackSplit (fast/slow pointers)

```
FrontBackSplit(source):
    if source == NULL:
        front = NULL; back = NULL; return
    if source.next == NULL:
        front = source; back = NULL; return

    slow = source
    fast = source.next

    while fast != NULL:
        fast = fast.next
        if fast != NULL:
            slow = slow.next
            fast = fast.next

    // slow is end of front list
    front = source
    back = slow.next
    slow.next = NULL
```

**Complexity:** O(n) time, O(1) extra space.

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment20_frontbacksplit_prasad_hargude.cpp
// C++11 — Front-Back Split of a Singly Linked List
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <limits>
#include <sstream>

using namespace std;

struct Node_prh {
    int data_prh;
    Node_prh* next_prh;
    Node_prh(int d): data_prh(d), next_prh(nullptr) {}
};

class SList_prh {
public:
    Node_prh* head_prh;
    SList_prh(): head_prh(nullptr) {}
    ~SList_prh() { clear_prh(); }

    void clear_prh() {
        Node_prh* cur = head_prh;
        while (cur) {
            Node_prh* nxt = cur->next_prh;
            delete cur;
            cur = nxt;
        }
        head_prh = nullptr;
    }

    void pushBack_prh(int val) {
        Node_prh* node = new Node_prh(val);
        if (!head_prh) { head_prh = node; return; }
        Node_prh* cur = head_prh;
        while (cur->next_prh) cur = cur->next_prh;
        cur->next_prh = node;
    }

    void display_prh() const {
        Node_prh* cur = head_prh;
        cout << "{";
        bool first = true;
        while (cur) {
            if (!first) cout << ", ";
            cout << cur->data_prh;
            first = false;
            cur = cur->next_prh;
        }
        cout << "}";
    }

    // Static split function: splits source into frontRef and backRef
    static void frontBackSplit_prh(Node_prh* source, Node_prh*& frontRef, Node_prh*& backRef) {
        frontRef = nullptr; backRef = nullptr;
        if (!source) return;
        if (!source->next_prh) { frontRef = source; backRef = nullptr; return; }
        Node_prh* slow = source;
        Node_prh* fast = source->next_prh;
        while (fast) {
            fast = fast->next_prh;
            if (fast) {
                slow = slow->next_prh;
                fast = fast->next_prh;
            }
        }
        frontRef = source;
        backRef = slow->next_prh;
        slow->next_prh = nullptr;
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 20: Front-Back Split of a Singly Linked List\n";

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Demo with example {2,3,5,7,11}\n";
        cout << "2. Demo with custom list\n";
        cout << "3. Run test suite (length 0..6)\n";
        cout << "4. Exit\n";
        cout << "Enter choice: ";
        int ch; if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            SList_prh L;
            int arr[] = {2,3,5,7,11};
            for (int v: arr) L.pushBack_prh(v);
            cout << "Original: "; L.display_prh(); cout << "\n";
            Node_prh *front = nullptr, *back = nullptr;
            SList_prh::frontBackSplit_prh(L.head_prh, front, back);
            cout << "Front: "; SList_prh f; f.head_prh = front; f.display_prh(); cout << "\n";
            cout << "Back:  "; SList_prh b; b.head_prh = back; b.display_prh(); cout << "\n";
            f.clear_prh(); b.clear_prh();
        } else if (ch == 2) {
            SList_prh L;
            cout << "Enter numbers separated by space (end with newline):\n";
            string line; getline(cin, line);
            if (line.empty()) { cout << "No input.\n"; continue; }
            istringstream iss(line);
            int x; bool any=false;
            while (iss >> x) { L.pushBack_prh(x); any=true; }
            if (!any) { cout << "No valid integers read.\n"; continue; }
            cout << "Original: "; L.display_prh(); cout << "\n";
            Node_prh *front=nullptr, *back=nullptr;
            SList_prh::frontBackSplit_prh(L.head_prh, front, back);
            cout << "Front: "; SList_prh f; f.head_prh = front; f.display_prh(); cout << "\n";
            cout << "Back:  "; SList_prh b; b.head_prh = back; b.display_prh(); cout << "\n";
            f.clear_prh(); b.clear_prh();
        } else if (ch == 3) {
            for (int len=0; len<=6; ++len) {
                SList_prh L;
                for (int i=1;i<=len;i++) L.pushBack_prh(i);
                Node_prh *front=nullptr, *back=nullptr;
                SList_prh::frontBackSplit_prh(L.head_prh, front, back);
                SList_prh f; f.head_prh = front;
                SList_prh b; b.head_prh = back;
                cout << "len=" << len << " Original: "; L.display_prh();
                cout << " -> Front: "; f.display_prh();
                cout << " | Back: "; b.display_prh();
                cout << "\n";
                f.clear_prh(); b.clear_prh();
            }
        } else if (ch == 4) {
            cout << "Exiting...\n"; break;
        } else {
            cout << "Invalid choice\n";
        }
    }

    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile with g++ (C++11):

```bash
g++ -std=c++11 -O2 assignment20_frontbacksplit_prasad_hargude.cpp -o assignment20_frontbacksplit
./assignment20_frontbacksplit
```

---

## 7. Example Runs and Test Cases (with expected outputs)

### Test: length = 5

Input list: {2,3,5,7,11}
Expected:

```
Front: {2, 3, 5}
Back:  {7, 11}
```

### Test: length = 4

Input list: {1,2,3,4}
Expected:

```
Front: {1, 2}
Back:  {3, 4}
```

### Test: length = 3

Input list: {1,2,3}
Expected:

```
Front: {1, 2}
Back:  {3}
```

### Test: length = 2

Input list: {1,2}
Expected:

```
Front: {1}
Back:  {2}
```

### Test: length = 1

Input list: {1}
Expected:

```
Front: {1}
Back:  {}
```

---

## 8. Memory Management & Edge Cases

* `clear_prh()` frees all nodes to prevent memory leaks.
* Handles empty list (source == NULL) and single-element lists.
* The fast/slow technique ensures the extra element in odd-length lists goes to front.

---

## 9. Extensions and Enhancements

* Implement recursive splitting.
* Provide a function that returns sizes of sublists without modifying original list (non-destructive split).
* Support splitting circular linked lists.

---

## 10. References

* Data structures textbooks: linked lists and tortoise-hare technique.
* Standard C++ documentation.
  ")
