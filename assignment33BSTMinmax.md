# Assignment 33: Binary Search Tree — Create, Find Minimum & Maximum (Menu-Driven)
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
Write a C++ program to create a **Binary Search Tree (BST)** and implement operations to:

- Insert keys into the BST (create)
- Find the **minimum** key in the BST
- Find the **maximum** key in the BST
- Provide menu-driven interactions for the user

All identifiers use the `_prh` suffix.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Data structure:** BST implemented with dynamic nodes (pointers)  
- **Interface:** Console-based, menu-driven (Insert, Find Min, Find Max, Traversals, Clear, Exit)  
- **Portability:** Compiles with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

### Node
```cpp
struct Node_prh {
    int key_prh;
    Node_prh* left_prh;
    Node_prh* right_prh;
    Node_prh(int k): key_prh(k), left_prh(nullptr), right_prh(nullptr) {}
};
```

### BST operations
- `insert_prh(int key)` — standard BST insert (duplicates ignored)
- `findMin_prh()` — return minimum key (leftmost node)
- `findMax_prh()` — return maximum key (rightmost node)
- Traversals for verification: `inorder_prh()`, `levelOrder_prh()`
- `clear_prh()` — delete all nodes

---

## 4. Algorithms (pseudocode + complexity)

### Insert
```
insert(node, key):
    if node == NULL: return new Node(key)
    if key < node.key: node.left = insert(node.left, key)
    else if key > node.key: node.right = insert(node.right, key)
    else: duplicate -> ignore
    return node
```
Time: O(h) average O(log n), worst O(n) for skewed tree.

### Find Minimum
```
findMin(node):
    if node == NULL: error (empty)
    while node.left != NULL: node = node.left
    return node.key
```
Time: O(h)

### Find Maximum
```
findMax(node):
    if node == NULL: error (empty)
    while node.right != NULL: node = node.right
    return node.key
```
Time: O(h)

---

## 5. C++ Implementation (complete, production-ready, menu-driven)

```cpp
// assignment33_bst_minmax_prasad_hargude.cpp
// C++11 — BST create, find minimum and maximum; menu-driven
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <queue>
#include <limits>
#include <stdexcept>

using namespace std;

struct Node_prh {
    int key_prh;
    Node_prh* left_prh;
    Node_prh* right_prh;
    Node_prh(int k): key_prh(k), left_prh(nullptr), right_prh(nullptr) {}
};

class BSTMinMax_prh {
private:
    Node_prh* root_prh;

    Node_prh* insertRec_prh(Node_prh* node, int key) {
        if (!node) return new Node_prh(key);
        if (key < node->key_prh) node->left_prh = insertRec_prh(node->left_prh, key);
        else if (key > node->key_prh) node->right_prh = insertRec_prh(node->right_prh, key);
        else cout << "Key " << key << " already exists; ignoring duplicate.\n";
        return node;
    }

    Node_prh* findMinNode_prh(Node_prh* node) const {
        if (!node) return nullptr;
        while (node->left_prh) node = node->left_prh;
        return node;
    }

    Node_prh* findMaxNode_prh(Node_prh* node) const {
        if (!node) return nullptr;
        while (node->right_prh) node = node->right_prh;
        return node;
    }

    void inorderRec_prh(Node_prh* node) const {
        if (!node) return;
        inorderRec_prh(node->left_prh);
        cout << node->key_prh << " ";
        inorderRec_prh(node->right_prh);
    }

    void clearRec_prh(Node_prh* node) {
        if (!node) return;
        clearRec_prh(node->left_prh);
        clearRec_prh(node->right_prh);
        delete node;
    }

public:
    BSTMinMax_prh(): root_prh(nullptr) {}
    ~BSTMinMax_prh() { clear_prh(); }

    void insert_prh(int key) { root_prh = insertRec_prh(root_prh, key); }

    int findMin_prh() const {
        Node_prh* mn = findMinNode_prh(root_prh);
        if (!mn) throw runtime_error("Tree is empty");
        return mn->key_prh;
    }

    int findMax_prh() const {
        Node_prh* mx = findMaxNode_prh(root_prh);
        if (!mx) throw runtime_error("Tree is empty");
        return mx->key_prh;
    }

    void inorder_prh() const { inorderRec_prh(root_prh); cout << "\n"; }

    void levelOrder_prh() const {
        if (!root_prh) { cout << "(empty tree)\n"; return; }
        queue<Node_prh*> q;
        q.push(root_prh);
        while (!q.empty()) {
            Node_prh* cur = q.front(); q.pop();
            cout << cur->key_prh << " ";
            if (cur->left_prh) q.push(cur->left_prh);
            if (cur->right_prh) q.push(cur->right_prh);
        }
        cout << "\n";
    }

    void clear_prh() { clearRec_prh(root_prh); root_prh = nullptr; }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 33: BST Create + Find Min/Max (Menu Driven)\n";
    BSTMinMax_prh bst;

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Insert key into BST\n";
        cout << "2. Find minimum key\n";
        cout << "3. Find maximum key\n";
        cout << "4. Inorder traversal\n";
        cout << "5. Level-order traversal\n";
        cout << "6. Clear tree\n";
        cout << "7. Exit\n";
        cout << "Enter choice: ";
        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            int k; cout << "Enter key to insert: "; cin >> k;
            bst.insert_prh(k);
            cout << "Inserted " << k << "\n";
        } else if (ch == 2) {
            try {
                cout << "Minimum key: " << bst.findMin_prh() << "\n";
            } catch (const exception &e) {
                cout << "Error: " << e.what() << "\n";
            }
        } else if (ch == 3) {
            try {
                cout << "Maximum key: " << bst.findMax_prh() << "\n";
            } catch (const exception &e) {
                cout << "Error: " << e.what() << "\n";
            }
        } else if (ch == 4) {
            cout << "Inorder traversal: "; bst.inorder_prh();
        } else if (ch == 5) {
            cout << "Level-order traversal: "; bst.levelOrder_prh();
        } else if (ch == 6) {
            bst.clear_prh();
            cout << "Tree cleared.\n";
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
g++ -std=c++11 -O2 assignment33_bst_minmax_prasad_hargude.cpp -o assignment33_bst_minmax
./assignment33_bst_minmax
```

---

## 7. Example Runs and Test Cases

Sample session:
```
Insert: 50 30 70 20 40 60 80
Inorder: 20 30 40 50 60 70 80
Find Min -> 20
Find Max -> 80
Level-order -> 50 30 70 20 40 60 80
Clear -> empty
```

Edge cases:
- Calling findMin/findMax on empty tree throws an error and is reported.

---

## 8. Memory Management & Edge Cases
- `clear_prh()` frees nodes; destructor calls it to avoid leaks.
- Duplicate keys are ignored.
- Recursive functions may use O(h) stack; worst-case O(n).

---

## 9. Extensions and Enhancements
- Support deletion of keys, search, and balancing (AVL/Red-Black).
- Return min/max nodes (not just keys) or support duplicates.
- Add file I/O to load/save trees.

---

## 10. References
- BST fundamentals from data-structures textbooks
- C++ standard library docs (queue, I/O)

