# Assignment 38: Employee Records — Efficient Search & Sort by emp_id using Tree (BST)
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
Write a C++ program that stores employee records and allows **efficient search** by `emp_id` using a **Binary Search Tree (BST)**. The program should also **sort/display** all employee data by `emp_id` in ascending order.

Each employee record should include:
- `emp_id` (integer - unique key)
- `name` (string)
- `department` (string)
- `salary` (double)

Operations (menu-driven):
- Insert new employee record
- Search employee by `emp_id` (efficient O(h))
- Display all employees sorted by `emp_id` (inorder traversal)
- Delete employee by `emp_id`
- Show (inorder, preorder, level-order)
- Exit

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Data structure:** BST keyed by `emp_id` (unique integer)  
- **Interface:** Console-based, menu-driven  
- **Portability:** Should compile with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

### Employee record node
```cpp
struct EmpNode_prh {
    int emp_id_prh;
    string name_prh;
    string dept_prh;
    double salary_prh;
    EmpNode_prh* left_prh;
    EmpNode_prh* right_prh;
    EmpNode_prh(int id, const string &n, const string &d, double s)
      : emp_id_prh(id), name_prh(n), dept_prh(d), salary_prh(s), left_prh(nullptr), right_prh(nullptr) {}
};
```

### BST properties
- BST is ordered by `emp_id_prh`.
- Search, insert, delete operate in O(h) time (h = tree height).
- Inorder traversal yields ascending `emp_id` order — used to display sorted list.

---

## 4. Algorithms (pseudocode + complexity)

### Insert (recursive)
```
insert(node, record):
    if node == NULL: return new node
    if emp_id < node.emp_id: node.left = insert(node.left, record)
    else if emp_id > node.emp_id: node.right = insert(node.right, record)
    else: duplicate id -> reject
    return node
```
Time: O(h)

### Search
```
search(node, emp_id):
    if node == NULL: return NULL
    if emp_id == node.emp_id: return node
    if emp_id < node.emp_id: return search(node.left, emp_id)
    else: return search(node.right, emp_id)
```
Time: O(h)

### Delete
Standard BST delete handling 0/1/2 children; use inorder successor for two-child case.

### Display Sorted (inorder)
Inorder traversal prints employees in ascending `emp_id`. Time O(n).

---

## 5. C++ Implementation (menu-driven, production-ready)

```cpp
// assignment38_employee_bst_prasad_hargude.cpp
// C++11 — Employee records stored in BST (search by emp_id, display sorted by emp_id)
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <queue>
#include <limits>
#include <stdexcept>

using namespace std;

struct EmpNode_prh {
    int emp_id_prh;
    string name_prh;
    string dept_prh;
    double salary_prh;
    EmpNode_prh* left_prh;
    EmpNode_prh* right_prh;
    EmpNode_prh(int id, const string &n, const string &d, double s)
      : emp_id_prh(id), name_prh(n), dept_prh(d), salary_prh(s), left_prh(nullptr), right_prh(nullptr) {}
};

class EmpBST_prh {
private:
    EmpNode_prh* root_prh;

    EmpNode_prh* insertRec_prh(EmpNode_prh* node, EmpNode_prh* rec) {
        if (!node) return rec;
        if (rec->emp_id_prh < node->emp_id_prh) node->left_prh = insertRec_prh(node->left_prh, rec);
        else if (rec->emp_id_prh > node->emp_id_prh) node->right_prh = insertRec_prh(node->right_prh, rec);
        else {
            cout << "Error: emp_id " << rec->emp_id_prh << " already exists. Insertion rejected.\n";
            delete rec;
        }
        return node;
    }

    EmpNode_prh* searchRec_prh(EmpNode_prh* node, int id) const {
        if (!node) return nullptr;
        if (id == node->emp_id_prh) return node;
        if (id < node->emp_id_prh) return searchRec_prh(node->left_prh, id);
        return searchRec_prh(node->right_prh, id);
    }

    EmpNode_prh* findMin_prh(EmpNode_prh* node) const {
        if (!node) return nullptr;
        while (node->left_prh) node = node->left_prh;
        return node;
    }

    EmpNode_prh* deleteRec_prh(EmpNode_prh* node, int id) {
        if (!node) return nullptr;
        if (id < node->emp_id_prh) node->left_prh = deleteRec_prh(node->left_prh, id);
        else if (id > node->emp_id_prh) node->right_prh = deleteRec_prh(node->right_prh, id);
        else {
            // found
            if (!node->left_prh && !node->right_prh) {
                delete node; return nullptr;
            } else if (!node->left_prh) {
                EmpNode_prh* tmp = node->right_prh; delete node; return tmp;
            } else if (!node->right_prh) {
                EmpNode_prh* tmp = node->left_prh; delete node; return tmp;
            } else {
                EmpNode_prh* succ = findMin_prh(node->right_prh);
                node->emp_id_prh = succ->emp_id_prh;
                node->name_prh = succ->name_prh;
                node->dept_prh = succ->dept_prh;
                node->salary_prh = succ->salary_prh;
                node->right_prh = deleteRec_prh(node->right_prh, succ->emp_id_prh);
            }
        }
        return node;
    }

    void inorderRec_prh(EmpNode_prh* node) const {
        if (!node) return;
        inorderRec_prh(node->left_prh);
        cout << "ID: " << node->emp_id_prh << " | " << node->name_prh << " | " << node->dept_prh
             << " | Salary: " << node->salary_prh << "\n";
        inorderRec_prh(node->right_prh);
    }

    void preorderRec_prh(EmpNode_prh* node) const {
        if (!node) return;
        cout << node->emp_id_prh << " ";
        preorderRec_prh(node->left_prh);
        preorderRec_prh(node->right_prh);
    }

    void clearRec_prh(EmpNode_prh* node) {
        if (!node) return;
        clearRec_prh(node->left_prh);
        clearRec_prh(node->right_prh);
        delete node;
    }

public:
    EmpBST_prh(): root_prh(nullptr) {}
    ~EmpBST_prh() { clear_prh(); }

    void insert_prh(int id, const string &name, const string &dept, double salary) {
        EmpNode_prh* rec = new EmpNode_prh(id, name, dept, salary);
        root_prh = insertRec_prh(root_prh, rec);
    }

    EmpNode_prh* search_prh(int id) const { return searchRec_prh(root_prh, id); }

    void remove_prh(int id) { 
        if (!search_prh(id)) { cout << "emp_id " << id << " not found.\n"; return; }
        root_prh = deleteRec_prh(root_prh, id); 
        cout << "Deleted emp_id " << id << "\n";
    }

    void displaySorted_prh() const {
        if (!root_prh) { cout << "(no records)\n"; return; }
        cout << "Employees sorted by emp_id (ascending):\n";
        inorderRec_prh(root_prh);
    }

    void show_prh() const {
        cout << "Inorder (sorted list):\n"; displaySorted_prh();
        cout << "Preorder keys: "; preorderRec_prh(root_prh); cout << "\n";
        cout << "Level-order keys:\n";
        if (!root_prh) { cout << "(empty)\n"; return; }
        queue<EmpNode_prh*> q; q.push(root_prh); 
        int lvl = 0;
        while (!q.empty()) {
            int sz = (int)q.size();
            cout << "Level " << lvl << ": ";
            for (int i = 0; i < sz; ++i) {
                EmpNode_prh* cur = q.front(); q.pop();
                cout << cur->emp_id_prh << " ";
                if (cur->left_prh) q.push(cur->left_prh);
                if (cur->right_prh) q.push(cur->right_prh);
            }
            cout << "\n"; ++lvl;
        }
    }

    void clear_prh() { clearRec_prh(root_prh); root_prh = nullptr; }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 38: Employee Records using BST (Search & Sort by emp_id)\n";
    EmpBST_prh db;
    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Insert employee\n";
        cout << "2. Search by emp_id\n";
        cout << "3. Delete by emp_id\n";
        cout << "4. Display all employees (sorted by emp_id)\n";
        cout << "5. Show (traversals and level-wise)\n";
        cout << "6. Clear all records\n";
        cout << "7. Exit\n";
        cout << "Enter choice: ";
        int ch; if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            int id; string name, dept; double sal;
            cout << "emp_id (int): "; cin >> id; flushStdin_prh();
            cout << "Name: "; getline(cin, name);
            cout << "Department: "; getline(cin, dept);
            cout << "Salary: "; cin >> sal; flushStdin_prh();
            db.insert_prh(id, name, dept, sal);
            cout << "Inserted employee " << name << " (id=" << id << ")\n";
        } else if (ch == 2) {
            int id; cout << "Enter emp_id to search: "; cin >> id; flushStdin_prh();
            EmpNode_prh* e = db.search_prh(id);
            if (!e) cout << "Record not found.\n";
            else {
                cout << "Found: ID=" << e->emp_id_prh << " | " << e->name_prh << " | " << e->dept_prh
                     << " | Salary=" << e->salary_prh << "\n";
            }
        } else if (ch == 3) {
            int id; cout << "Enter emp_id to delete: "; cin >> id; flushStdin_prh();
            db.remove_prh(id);
        } else if (ch == 4) {
            db.displaySorted_prh();
        } else if (ch == 5) {
            db.show_prh();
        } else if (ch == 6) {
            db.clear_prh(); cout << "All records cleared.\n";
        } else if (ch == 7) {
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
g++ -std=c++11 -O2 assignment38_employee_bst_prasad_hargude.cpp -o assignment38_employee_bst
./assignment38_employee_bst
```

---

## 7. Example Runs and Test Cases

Sample session:
```
Insert: (101, "Alice", "HR", 45000)
Insert: (203, "Bob", "Eng", 60000)
Insert: (150, "Carol", "Sales", 50000)
Display sorted -> shows 101,150,203 with corresponding data
Search 150 -> found Carol
Delete 150 -> removed
Display sorted -> 101,203
```

Edge cases:
- Duplicate emp_id insertion is rejected.
- Searching/deleting non-existent emp_id prints message.

---

## 8. Memory Management & Edge Cases
- Nodes deleted via `clear_prh()` on exit to avoid leaks.
- Recursive depth may be O(n) for skewed tree.
- emp_id uniqueness enforced.

---

## 9. Extensions and Enhancements
- Use self-balancing BST (AVL/Red-Black) for guaranteed O(log n) operations.
- Persist records to file (CSV/JSON) and load on startup.
- Support range queries, salary-based search, updates.

---

## 10. References
- BST data structures and algorithms
- C++ STL documentation (queue, i/o)
