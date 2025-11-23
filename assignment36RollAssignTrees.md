# Assignment 36: Assign Roll Numbers Using Trees (Topper = Roll No. 1)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready, menu-driven)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References

---

## 1. Problem Statement
Write a program that assigns roll numbers to students based on their previous year's results (marks). The student with the highest marks (topper) should receive **roll number 1**, the next highest roll number 2, and so on.

Requirements:
- Use a **tree** (BST) to store students keyed by marks.
- Handle duplicate marks (tie) deterministically — e.g., by sorting names alphabetically within the same score.
- Provide menu-driven operations to add students, assign roll numbers, display assigned roll list, search for a student, and clear data.

All identifiers use the `_prh` suffix as requested.

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)  
- **Data structure:** Binary Search Tree keyed by integer marks; each node holds a list of student names with that mark.  
- **Interface:** Console-based, menu-driven program  
- **Portability:** Compiles with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

### Node
Each BST node represents a particular marks value and stores:
- `marks_prh` — integer marks (key)
- `names_prh` — `std::vector<std::string>` of student names who obtained that marks (kept sorted)
- `left_prh`, `right_prh` — child pointers

```cpp
struct Node_prh {
    int marks_prh;
    std::vector<std::string> names_prh;
    Node_prh* left_prh;
    Node_prh* right_prh;
    Node_prh(int m, const std::string &name): marks_prh(m), left_prh(nullptr), right_prh(nullptr) {
        names_prh.push_back(name);
    }
};
```

### Why BST?
- BST keyed by marks allows efficient insertion and traversal.
- To generate roll numbers with topper as 1, perform **reverse-inorder traversal** (visit right, node, left) so that higher marks are processed first.
- For duplicate marks, the vector `names_prh` stores multiple students; we sort those names alphabetically to break ties deterministically.

---

## 4. Algorithms (pseudocode + complexity)

### Insert student
```
insert(node, marks, name):
    if node == NULL: return new Node(marks, name)
    if marks < node.marks: node.left = insert(node.left, marks, name)
    else if marks > node.marks: node.right = insert(node.right, marks, name)
    else: // same marks
        insert name into node.names in sorted order (keep vector sorted)
    return node
```
Time: average O(h) where h is tree height. Worst-case O(n) if tree is skewed. Insertion into names vector is O(k) for k students with same marks.

### Assign roll numbers
```
roll = 1
reverseInorder(node):
    if node == NULL: return
    reverseInorder(node.right)
    for each name in node.names (sorted):
        assign roll_no_map[name] = roll
        roll = roll + 1
    reverseInorder(node.left)
```
Time: O(n + total_names_sorting). Space: O(n) for map.

### Search student
We can search by name by storing an auxiliary unordered_map from name to marks or by searching whole tree (O(n)). We will maintain a map `name_to_marks_prh` to allow O(1) search & lookup of assigned roll later.

---

## 5. C++ Implementation (menu-driven)

The code implements:
- Add student (name + marks)
- Assign roll numbers (generates map name → roll)
- Display assigned roll list (roll order from 1..N)
- Search a student (show marks and assigned roll if assigned)
- Display students in descending marks order (without assigning)
- Clear data / Exit

```cpp
// assignment36_roll_assignment_prasad_hargude.cpp
// C++11 — Assign roll numbers using BST keyed by marks (topper = roll no.1)
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <unordered_map>
#include <algorithm>
#include <limits>

using namespace std;

struct Node_prh {
    int marks_prh;
    vector<string> names_prh;
    Node_prh* left_prh;
    Node_prh* right_prh;
    Node_prh(int m, const string &name) : marks_prh(m), left_prh(nullptr), right_prh(nullptr) {
        names_prh.push_back(name);
    }
};

class RollAssign_prh {
private:
    Node_prh* root_prh;
    // map from name to marks to allow quick searches and avoid duplicate name insertions
    unordered_map<string,int> name_to_marks_prh;
    // after assignment: name -> roll no
    map<int, vector<string>> roll_to_names_prh; // roll -> names (usually single)
    unordered_map<string,int> name_to_roll_prh; // name -> roll

    void insertNameSorted_prh(vector<string> &vec, const string &name) {
        // keep vector sorted lexicographically and avoid duplicates
        auto it = lower_bound(vec.begin(), vec.end(), name);
        if (it == vec.end() || *it != name) vec.insert(it, name);
    }

    Node_prh* insertRec_prh(Node_prh* node, int marks, const string &name) {
        if (!node) return new Node_prh(marks, name);
        if (marks < node->marks_prh) node->left_prh = insertRec_prh(node->left_prh, marks, name);
        else if (marks > node->marks_prh) node->right_prh = insertRec_prh(node->right_prh, marks, name);
        else insertNameSorted_prh(node->names_prh, name);
        return node;
    }

    void reverseInorderAssign_prh(Node_prh* node, int &roll_prh) {
        if (!node) return;
        reverseInorderAssign_prh(node->right_prh, roll_prh);
        // names_prh already sorted; assign in that order
        for (const string &nm : node->names_prh) {
            name_to_roll_prh[nm] = roll_prh;
            roll_to_names_prh[roll_prh].push_back(nm);
            ++roll_prh;
        }
        reverseInorderAssign_prh(node->left_prh, roll_prh);
    }

    void collectDescending_prh(Node_prh* node, vector<pair<int, vector<string>>> &out_prh) const {
        if (!node) return;
        collectDescending_prh(node->right_prh, out_prh);
        out_prh.emplace_back(node->marks_prh, node->names_prh);
        collectDescending_prh(node->left_prh, out_prh);
    }

    void clearRec_prh(Node_prh* node) {
        if (!node) return;
        clearRec_prh(node->left_prh);
        clearRec_prh(node->right_prh);
        delete node;
    }

public:
    RollAssign_prh(): root_prh(nullptr) {}
    ~RollAssign_prh() { clear_prh(); }

    bool addStudent_prh(const string &name, int marks) {
        if (name.empty()) return false;
        // prevent duplicate name insertion
        if (name_to_marks_prh.find(name) != name_to_marks_prh.end()) return false;
        root_prh = insertRec_prh(root_prh, marks, name);
        name_to_marks_prh[name] = marks;
        return true;
    }

    void assignRolls_prh() {
        name_to_roll_prh.clear();
        roll_to_names_prh.clear();
        int roll = 1;
        reverseInorderAssign_prh(root_prh, roll);
        cout << "Assigned roll numbers to " << (roll-1) << " students.\n";
    }

    void displayAssigned_prh() const {
        if (name_to_roll_prh.empty()) {
            cout << "No rolls assigned yet. Use 'Assign roll numbers' first.\n";
            return;
        }
        for (const auto &p : roll_to_names_prh) {
            int r = p.first;
            for (const auto &nm : p.second) {
                cout << "Roll " << r << " : " << nm << "\n";
            }
        }
    }

    void displayDescending_prh() const {
        vector<pair<int, vector<string>>> items;
        collectDescending_prh(root_prh, items);
        if (items.empty()) { cout << "(no students)\n"; return; }
        for (const auto &pr : items) {
            int marks = pr.first;
            for (const string &nm : pr.second) {
                cout << nm << " (marks=" << marks << ")\n";
            }
        }
    }

    bool search_prh(const string &name) const {
        auto it = name_to_marks_prh.find(name);
        if (it == name_to_marks_prh.end()) return false;
        cout << "Student: " << name << " | Marks: " << it->second;
        auto it2 = name_to_roll_prh.find(name);
        if (it2 != name_to_roll_prh.end()) cout << " | Roll: " << it2->second;
        else cout << " | Roll: (not assigned)" ;
        cout << "\n";
        return true;
    }

    void clear_prh() {
        clearRec_prh(root_prh);
        root_prh = nullptr;
        name_to_marks_prh.clear();
        name_to_roll_prh.clear();
        roll_to_names_prh.clear();
    }

    int totalStudents_prh() const { return (int)name_to_marks_prh.size(); }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 36: Assign Roll Numbers Using Trees (Topper = Roll 1)\n";
    RollAssign_prh sys;
    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Add student (name + marks)\n";
        cout << "2. Assign roll numbers (topper = 1)\n";
        cout << "3. Display assigned roll list\n";
        cout << "4. Display students by descending marks (without assigning)\n";
        cout << "5. Search student by name\n";
        cout << "6. Show total students\n";
        cout << "7. Clear all data\n";
        cout << "8. Exit\n";
        cout << "Enter choice: ";
        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            string name;
            int marks;
            cout << "Enter student name: "; getline(cin, name);
            cout << "Enter marks (integer): "; if (!(cin >> marks)) { cout << "Invalid marks\n"; flushStdin_prh(); continue; }
            flushStdin_prh();
            if (sys.addStudent_prh(name, marks)) cout << "Added " << name << " with marks " << marks << "\n";
            else cout << "Failed to add. Name may be empty or already exists.\n";
        } else if (ch == 2) {
            sys.assignRolls_prh();
        } else if (ch == 3) {
            sys.displayAssigned_prh();
        } else if (ch == 4) {
            sys.displayDescending_prh();
        } else if (ch == 5) {
            string name; cout << "Enter name to search: "; getline(cin, name);
            if (!sys.search_prh(name)) cout << "Student not found.\n";
        } else if (ch == 6) {
            cout << "Total students: " << sys.totalStudents_prh() << "\n";
        } else if (ch == 7) {
            sys.clear_prh();
            cout << "All data cleared.\n";
        } else if (ch == 8) {
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

Compile:

```bash
g++ -std=c++11 -O2 assignment36_roll_assignment_prasad_hargude.cpp -o assignment36_roll_assignment
./assignment36_roll_assignment
```

---

## 7. Example Runs and Test Cases

Sample session:
```
Add: Alice 92
Add: Bob 85
Add: Carol 92
Add: Dave 78
Assign rolls
Display assigned:
Roll 1 : Alice
Roll 2 : Carol
Roll 3 : Bob
Roll 4 : Dave

Notes: Alice and Carol had same marks (92). Their names are sorted alphabetically so Alice receives roll 1 and Carol roll 2.
```

Edge cases:
- Duplicate names are rejected.
- No students → assign produces zero assignments.
- After clearing, previous assignments are removed.

---

## 8. Memory Management & Edge Cases
- Dynamically allocated BST nodes are freed on `clear_prh()` and object destruction.
- Duplicate marks handled; duplicates names prevented by map.
- Roll assignment should be re-run after adding/removing students (assignments are not auto-updated).

---

## 9. Extensions and Enhancements
- Allow duplicate names with unique IDs.
- Persist data to file and load later.
- Provide alternate tie-breakers (by attendance, roll of previous year etc.).
- Generate printable roll list with additional student details.

---

## 10. References
- BST traversal and ordering techniques
- C++ STL containers: vector, map, unordered_map
