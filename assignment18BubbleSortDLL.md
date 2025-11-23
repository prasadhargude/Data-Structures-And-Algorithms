# Assignment 18: Bubble Sort Using Doubly Linked List (In-Depth)
**Author:** Prasad Ramdas Hargude**

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
Write a C++ program to **implement Bubble Sort using a Doubly Linked List (DLL)**.  
The program must:

- Create a doubly linked list of integers  
- Display the list before and after sorting  
- Perform bubble sort only through **pointer manipulations** (preferable), or swapping data  
- Allow insertion of elements from user input  

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Data structure:** Doubly Linked List  
- **Operations:** Insert, Display, Bubble Sort  
- **Compilation:** g++ on Linux / Windows  

---

## 3. Data Structure & Design

### Node Structure
```cpp
struct Node_prh {
    int data_prh;
    Node_prh* prev_prh;
    Node_prh* next_prh;
    Node_prh(int d) : data_prh(d), prev_prh(nullptr), next_prh(nullptr) {}
};
```

### DLL Structure
```cpp
class DLL_prh {
    Node_prh* head_prh;
    Node_prh* tail_prh;
};
```

Functions:
- `insertEnd_prh(int)` → Insert at end  
- `display_prh()` → Print list  
- `bubbleSort_prh()` → Sort list  

Design Choices:
- Swapping **data only** for simplicity  
- Still demonstrates adjacency-based DLL traversal  

---

## 4. Algorithms (pseudocode + complexity)

### Bubble Sort on DLL
```
repeat
    swapped = false
    ptr = head
    while ptr and ptr.next exist:
        if ptr.data > ptr.next.data:
            swap(ptr.data, ptr.next.data)
            swapped = true
        ptr = ptr.next
until swapped == false
```

### Complexity
- **Time:** O(n²) worst and average  
- **Space:** O(1)  

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment18_bubblesort_dll.cpp
// Bubble Sort using Doubly Linked List
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <limits>
using namespace std;

struct Node_prh {
    int data_prh;
    Node_prh* prev_prh;
    Node_prh* next_prh;
    Node_prh(int d) : data_prh(d), prev_prh(nullptr), next_prh(nullptr) {}
};

class DLL_prh {
public:
    Node_prh* head_prh;
    Node_prh* tail_prh;

    DLL_prh() : head_prh(nullptr), tail_prh(nullptr) {}

    ~DLL_prh() { clear_prh(); }

    void clear_prh() {
        Node_prh* cur = head_prh;
        while (cur) {
            Node_prh* nxt = cur->next_prh;
            delete cur;
            cur = nxt;
        }
        head_prh = tail_prh = nullptr;
    }

    void insertEnd_prh(int val) {
        Node_prh* node = new Node_prh(val);
        if (!head_prh) {
            head_prh = tail_prh = node;
        } else {
            tail_prh->next_prh = node;
            node->prev_prh = tail_prh;
            tail_prh = node;
        }
    }

    void display_prh() {
        Node_prh* cur = head_prh;
        while (cur) {
            cout << cur->data_prh;
            if (cur->next_prh) cout << " <-> ";
            cur = cur->next_prh;
        }
        cout << endl;
    }

    void bubbleSort_prh() {
        if (!head_prh || !head_prh->next_prh) return;

        bool swapped;
        do {
            swapped = false;
            Node_prh* cur = head_prh;

            while (cur->next_prh) {
                if (cur->data_prh > cur->next_prh->data_prh) {
                    // Swap data (pointer swap also possible)
                    int temp = cur->data_prh;
                    cur->data_prh = cur->next_prh->data_prh;
                    cur->next_prh->data_prh = temp;
                    swapped = true;
                }
                cur = cur->next_prh;
            }
        } while (swapped);
    }
};

int main() {
    cout << "Assignment 18: Bubble Sort Using Doubly Linked List\n";

    DLL_prh list;
    int n;
    cout << "How many elements do you want to insert? ";
    cin >> n;

    cout << "Enter " << n << " integers: ";
    for (int i = 0; i < n; i++) {
        int x;
        cin >> x;
        list.insertEnd_prh(x);
    }

    cout << "\nBefore Sorting:\n";
    list.display_prh();

    list.bubbleSort_prh();

    cout << "\nAfter Sorting:\n";
    list.display_prh();

    return 0;
}
```

---

## 6. Compilation & Run Instructions

```bash
g++ -std=c++11 -O2 assignment18_bubblesort_dll.cpp -o assignment18
./assignment18
```

---

## 7. Example Runs and Test Cases

### Test Case 1
Input:
```
5
34 12 89 1 4
```

Expected Output:
```
Before Sorting:
34 <-> 12 <-> 89 <-> 1 <-> 4

After Sorting:
1 <-> 4 <-> 12 <-> 34 <-> 89
```

### Test Case 2
Input:
```
4
5 4 3 2
```

Output:
```
Before Sorting:
5 <-> 4 <-> 3 <-> 2
After Sorting:
2 <-> 3 <-> 4 <-> 5
```

### Test Case 3 — Already Sorted
```
1 2 3 4
```

Output:
```
Before Sorting:
1 <-> 2 <-> 3 <-> 4
After Sorting:
1 <-> 2 <-> 3 <-> 4
```

---

## 8. Memory Management & Edge Cases
- Destructor frees all nodes to prevent leaks  
- Sorting handles:
  - Empty list  
  - Single-element list  
  - Repeated elements  
- Swaps data only → safer, avoids pointer manipulation errors  

---

## 9. Extensions and Enhancements
- Implement **bidirectional bubble sort** (Cocktail shaker sort)  
- Implement a version with **pointer swapping instead of data swap**  
- Allow deletion and insertion at any position  
- Visualize sorting steps  

---

## 10. References
- Standard C++ documentation  
- Data Structures textbooks (Doubly Linked List section)  
- Bubble Sort algorithm references  

---
