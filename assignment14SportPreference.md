# Assignment 14: Sports Preference Set Operations Using Linked Lists (In-Depth)
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
In the Second Year Computer Engineering class there are two sets of students based on their favourite sports:
- **Set A**: students who like *Cricket*.
- **Set B**: students who like *Football*.

Write a C++ program to represent these two sets using linked lists and perform the following operations:

a) Find and display the set of students who like **both** Cricket and Football (Intersection).

b) Find and display the set of students who like **either Cricket or Football, but not both** (Symmetric Difference).

c) Display the **number of students who like neither** Cricket nor Football (complement with respect to total class size).

Initial assumptions: each student is identified by a unique **roll number** (integer).

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)
- **Data structures:** Singly linked lists to represent sets
- **User interactions:** Console-based, menu driven
- **Portability:** Should compile with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design
### Node (Student)
Each node represents a student identified by roll number:
```cpp
struct Node_prh {
    int roll_prh;        // student's roll number
    Node_prh* next_prh;  // pointer to next node
    Node_prh(int r) : roll_prh(r), next_prh(nullptr) {}
};
```

### Set Representation
- **SetList_prh**: linked list wrapper storing head pointer and utility methods.
- Operations provided: insert without duplicates, search (exists), display, count.

Design rationale:
- Linked lists are simple to implement and sufficient for small class sizes.
- Demonstrates set operations implemented with fundamental pointer manipulation.

---

## 4. Algorithms (pseudocode + complexity)
Notation: `n` = |A|, `m` = |B|.

### Insert (no duplicates)
```
insert(list, roll):
    if list.head == NULL:
        list.head = new Node(roll)
        return
    traverse nodes:
        if node.roll == roll: return  // already present
    append new node at end
```
Time complexity: O(n) (traverse)

### Intersection (A ∩ B)
```
result = empty list
for each x in A:
    if exists(x in B): insert(result, x)
```
Time complexity: O(n * m) (naive). With hashing you can do O(n + m).

### Symmetric Difference (A ⊕ B)
```
result = empty list
for each x in A:
    if not exists(x in B): insert(result, x)
for each x in B:
    if not exists(x in A): insert(result, x)
```
Time complexity: O(n * m) (naive).

### Count Neither
```
intersection_size = |A ∩ B|
union_size = |A| + |B| - intersection_size
neither = total_students - union_size
```
Time complexity: dominated by intersection computation O(n * m).

---

## 5. C++ Implementation (complete, production-ready)
The following single-file C++ program implements the requested features. Variable names end with `_prh` as requested.

```cpp
// assignment14SportPreference.cpp
// C++11 — Sports Preference Set Operations using Linked Lists
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <limits>
using namespace std;

struct Node_prh {
    int roll_prh;
    Node_prh* next_prh;
    Node_prh(int r) : roll_prh(r), next_prh(nullptr) {}
};

class SetList_prh {
public:
    Node_prh* head_prh;
    SetList_prh() : head_prh(nullptr) {}

    // Insert without duplicates
    void insert_prh(int roll_prh) {
        if (!head_prh) { head_prh = new Node_prh(roll_prh); return; }
        Node_prh* cur = head_prh;
        while (cur) {
            if (cur->roll_prh == roll_prh) return; // already present
            if (!cur->next_prh) break;
            cur = cur->next_prh;
        }
        cur->next_prh = new Node_prh(roll_prh);
    }

    bool exists_prh(int roll_prh) const {
        Node_prh* cur = head_prh;
        while (cur) { if (cur->roll_prh == roll_prh) return true; cur = cur->next_prh; }
        return false;
    }

    int count_prh() const {
        int c = 0; Node_prh* cur = head_prh; while (cur) { ++c; cur = cur->next_prh; } return c;
    }

    void display_prh() const {
        if (!head_prh) { cout << "(empty)"; return; }
        Node_prh* cur = head_prh; while (cur) { cout << cur->roll_prh; if (cur->next_prh) cout << ", "; cur = cur->next_prh; }
    }

    // Free memory
    void clear_prh() {
        Node_prh* cur = head_prh;
        while (cur) { Node_prh* nxt = cur->next_prh; delete cur; cur = nxt; }
        head_prh = nullptr;
    }
};

SetList_prh intersection_prh(const SetList_prh &A, const SetList_prh &B) {
    SetList_prh R;
    Node_prh* cur = A.head_prh;
    while (cur) {
        if (B.exists_prh(cur->roll_prh)) R.insert_prh(cur->roll_prh);
        cur = cur->next_prh;
    }
    return R;
}

SetList_prh symmetricDiff_prh(const SetList_prh &A, const SetList_prh &B) {
    SetList_prh R;
    Node_prh* cur = A.head_prh;
    while (cur) { if (!B.exists_prh(cur->roll_prh)) R.insert_prh(cur->roll_prh); cur = cur->next_prh; }
    cur = B.head_prh;
    while (cur) { if (!A.exists_prh(cur->roll_prh)) R.insert_prh(cur->roll_prh); cur = cur->next_prh; }
    return R;
}

int countNeither_prh(const SetList_prh &A, const SetList_prh &B, int total_students) {
    SetList_prh I = intersection_prh(A, B);
    int unionSize = A.count_prh() + B.count_prh() - I.count_prh();
    int neither = total_students - unionSize;
    if (neither < 0) neither = 0; // safety
    return neither;
}

// Helper to read roll numbers
void readRolls_prh(SetList_prh &S, int count) {
    for (int i = 0; i < count; ++i) {
        int r; cin >> r; S.insert_prh(r);
    }
}

int main() {
    cout << "Assignment 14: Sports Preference Set Operations\n";
    SetList_prh A, B;
    int total_students = 0;
    cout << "Enter total number of students in class: ";
    while (!(cin >> total_students) || total_students < 0) { cout << "Invalid. Enter positive integer: "; cin.clear(); cin.ignore(numeric_limits<streamsize>::max(), '\n'); }
    int nA = 0; cout << "Enter number of students who like Cricket (Set A): "; while (!(cin >> nA) || nA < 0) { cout << "Invalid: "; cin.clear(); cin.ignore(numeric_limits<streamsize>::max(), '\n'); }
    if (nA > 0) { cout << "Enter their roll numbers (space or newline separated): "; readRolls_prh(A, nA); }
    int nB = 0; cout << "Enter number of students who like Football (Set B): "; while (!(cin >> nB) || nB < 0) { cout << "Invalid: "; cin.clear(); cin.ignore(numeric_limits<streamsize>::max(), '\n'); }
    if (nB > 0) { cout << "Enter their roll numbers (space or newline separated): "; readRolls_prh(B, nB); }

    cout << "\n--- Results ---\n";
    cout << "Students who like BOTH Cricket and Football:\n";
    SetList_prh I = intersection_prh(A, B);
    I.display_prh(); cout << "\n";
    cout << "Students who like EITHER Cricket or Football but NOT both:\n";
    SetList_prh S = symmetricDiff_prh(A, B);
    S.display_prh(); cout << "\n";
    int neither = countNeither_prh(A, B, total_students);
    cout << "Number of students who like NEITHER Cricket nor Football: " << neither << "\n";

    // cleanup
    A.clear_prh(); B.clear_prh(); I.clear_prh(); S.clear_prh();
    return 0;
}
```

---

## 6. Compilation & Run Instructions
Compile with g++ (C++11):
```bash
g++ -std=c++11 -O2 assignment14SportPreference.cpp -o assignment14SportPreference
./assignment14SportPreference
```

---

## 7. Example Runs and Test Cases
### Example 1 — Basic
Input:
```
Total students: 10
Set A (Cricket) count: 4
Rolls: 1 2 3 4
Set B (Football) count: 3
Rolls: 3 4 5
```
Expected Output:
```
Students who like BOTH Cricket and Football:
3, 4
Students who like EITHER Cricket or Football but NOT both:
1, 2, 5
Number of students who like NEITHER Cricket nor Football: 5
```

### Example 2 — Edge cases (empty sets)
Input:
```
Total students: 5
Set A count: 0
Set B count: 0
```
Expected Output:
```
Both: (empty)
Either but not both: (empty)
Neither: 5
```

---

## 8. Memory Management & Edge Cases
- All nodes allocated with `new` are deleted in `clear_prh()` to prevent memory leaks.
- Duplicate roll numbers in input are ignored by `insert_prh`.
- If `total_students` < actual union size, function clamps `neither` to 0.
- The naive approach uses O(n*m) time; for large classes consider using a hash set to reduce to O(n+m).

---

## 9. Extensions and Enhancements
- Implement union and difference operations.
- Use unordered_set<int> for faster lookups.
- Store student names and additional attributes.
- Provide a menu-driven interactive program with save/load to CSV.

---

## 10. References
- C++11 Standard Library
- Common Data Structures and Algorithms textbooks
- Linked list and set operation references
