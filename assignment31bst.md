# Assignment 31: Binary Search Tree (BST) Operations — Menu Driven (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready, menu-driven)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases (with expected outputs)  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References

---

## 1. Problem Statement
Write a C++ program to implement a **Binary Search Tree (BST)** with the following operations (menu-driven):

- Create a BST (start empty)
- Insert a key
- Delete a key
- Search a key
- Inorder / Preorder / Postorder traversal
- Level-wise display (breadth-first)
- Find minimum / maximum
- Clear tree (delete all nodes)
- Exit

All identifiers use `_prh` suffix as requested.

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)  
- **Data structure:** Binary Search Tree implemented with dynamic nodes (pointers)  
- **Interface:** Console-based, menu-driven program  
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

### BST class
Provides:
- `insert_prh(int key)`
- `delete_prh(int key)`
- `search_prh(int key)` -> bool
- `inorder_prh()`, `preorder_prh()`, `postorder_prh()`
- `levelOrder_prh()` (level-wise display)
- `findMin_prh()`, `findMax_prh()`
- `clear_prh()` (destructor helper)

Design notes:
- `insert` and `search` are standard BST ops.
- `delete` handles three cases:
  1. Node is leaf: remove it.
  2. Node has one child: replace node with child.
  3. Node has two children: find in-order successor (minimum in right subtree), replace node's key with successor's key, and delete successor node.
- `levelOrder` uses a queue (std::queue) to print nodes level by level.

---

## 4. Algorithms (pseudocode + complexity)

### Insert (recursive)
```
insert(node, key):
    if node == NULL: return new Node(key)
    if key < node.key: node.left = insert(node.left, key)
    else if key > node.key: node.right = insert(node.right, key)
    else: duplicate -> ignore or handle
    return node
```
Time: O(h) average O(log n), worst O(n)

### Search
```
search(node, key):
    if node == NULL: return false
    if key == node.key: return true
    if key < node.key: return search(node.left, key)
    else: return search(node.right, key)
```
Time: O(h)

### Delete
```
delete(node, key):
    if node == NULL: return NULL
    if key < node.key: node.left = delete(node.left, key)
    else if key > node.key: node.right = delete(node.right, key)
    else:
        // node found
        if node.left == NULL and node.right == NULL: delete node; return NULL
        else if node.left == NULL: tmp = node.right; delete node; return tmp
        else if node.right == NULL: tmp = node.left; delete node; return tmp
        else:
            succ = findMin(node.right)
            node.key = succ.key
            node.right = delete(node.right, succ.key)
    return node
```
Time: O(h)

### Level-order (BFS)
Use queue, push root, while queue not empty: pop, print, push children. Use a sentinel or track level size to print per level.

---

## 5. C++ Implementation (complete, production-ready, menu-driven)

```cpp
// assignment31_bst_prasad_hargude.cpp
// C++11 — Binary Search Tree with Create/Insert/Delete/Search/Traversals/Levelwise display
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <queue>
#include <limits>

using namespace std;

struct Node_prh {
    int key_prh;
    Node_prh* left_prh;
    Node_prh* right_prh;
    Node_prh(int k): key_prh(k), left_prh(nullptr), right_prh(nullptr) {}
};

class BST_prh {
private:
    Node_prh* root_prh;

    Node_prh* insertRec_prh(Node_prh* node, int key) {
        if (!node) return new Node_prh(key);
        if (key < node->key_prh) node->left_prh = insertRec_prh(node->left_prh, key);
        else if (key > node->key_prh) node->right_prh = insertRec_prh(node->right_prh, key);
        else cout << "Key " << key << " already exists (duplicates ignored)\n";
        return node;
    }

    Node_prh* findMinNode_prh(Node_prh* node) {
        if (!node) return nullptr;
        while (node->left_prh) node = node->left_prh;
        return node;
    }

    Node_prh* deleteRec_prh(Node_prh* node, int key) {
        if (!node) return nullptr;
        if (key < node->key_prh) node->left_prh = deleteRec_prh(node->left_prh, key);
        else if (key > node->key_prh) node->right_prh = deleteRec_prh(node->right_prh, key);
        else {
            // node found
            if (!node->left_prh && !node->right_prh) {
                delete node;
                return nullptr;
            } else if (!node->left_prh) {
                Node_prh* tmp = node->right_prh;
                delete node;
                return tmp;
            } else if (!node->right_prh) {
                Node_prh* tmp = node->left_prh;
                delete node;
                return tmp;
            } else {
                Node_prh* succ = findMinNode_prh(node->right_prh);
                node->key_prh = succ->key_prh;
                node->right_prh = deleteRec_prh(node->right_prh, succ->key_prh);
            }
        }
        return node;
    }

    bool searchRec_prh(Node_prh* node, int key) {
        if (!node) return false;
        if (key == node->key_prh) return true;
        if (key < node->key_prh) return searchRec_prh(node->left_prh, key);
        return searchRec_prh(node->right_prh, key);
    }

    void inorderRec_prh(Node_prh* node) {
        if (!node) return;
        inorderRec_prh(node->left_prh);
        cout << node->key_prh << " ";
        inorderRec_prh(node->right_prh);
    }

    void preorderRec_prh(Node_prh* node) {
        if (!node) return;
        cout << node->key_prh << " ";
        preorderRec_prh(node->left_prh);
        preorderRec_prh(node->right_prh);
    }

    void postorderRec_prh(Node_prh* node) {
        if (!node) return;
        postorderRec_prh(node->left_prh);
        postorderRec_prh(node->right_prh);
        cout << node->key_prh << " ";
    }

    void clearRec_prh(Node_prh* node) {
        if (!node) return;
        clearRec_prh(node->left_prh);
        clearRec_prh(node->right_prh);
        delete node;
    }

public:
    BST_prh(): root_prh(nullptr) {}
    ~BST_prh() { clear_prh(); }

    void insert_prh(int key) { root_prh = insertRec_prh(root_prh, key); }

    void delete_prh(int key) {
        if (!searchRec_prh(root_prh, key)) {
            cout << "Key " << key << " not found.\n";
            return;
        }
        root_prh = deleteRec_prh(root_prh, key);
        cout << "Deleted key " << key << " if existed.\n";
    }

    bool search_prh(int key) { return searchRec_prh(root_prh, key); }

    void inorder_prh() {
        inorderRec_prh(root_prh);
        cout << "\n";
    }
    void preorder_prh() {
        preorderRec_prh(root_prh);
        cout << "\n";
    }
    void postorder_prh() {
        postorderRec_prh(root_prh);
        cout << "\n";
    }

    void levelOrder_prh() {
        if (!root_prh) { cout << "(empty tree)\n"; return; }
        queue<Node_prh*> q;
        q.push(root_prh);
        int level = 0;
        while (!q.empty()) {
            int sz = (int)q.size();
            cout << "Level " << level << ": ";
            for (int i = 0; i < sz; ++i) {
                Node_prh* cur = q.front(); q.pop();
                cout << cur->key_prh << " ";
                if (cur->left_prh) q.push(cur->left_prh);
                if (cur->right_prh) q.push(cur->right_prh);
            }
            cout << "\n";
            ++level;
        }
    }

    int findMin_prh() {
        Node_prh* mn = findMinNode_prh(root_prh);
        if (!mn) { throw runtime_error("Tree is empty"); }
        return mn->key_prh;
    }

    int findMax_prh() {
        if (!root_prh) throw runtime_error("Tree is empty");
        Node_prh* node = root_prh;
        while (node->right_prh) node = node->right_prh;
        return node->key_prh;
    }

    void clear_prh() {
        clearRec_prh(root_prh);
        root_prh = nullptr;
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 31: Binary Search Tree (BST) Operations - Menu Driven\n";
    BST_prh bst;
    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Insert key\n";
        cout << "2. Delete key\n";
        cout << "3. Search key\n";
        cout << "4. Inorder traversal\n";
        cout << "5. Preorder traversal\n";
        cout << "6. Postorder traversal\n";
        cout << "7. Level-wise display\n";
        cout << "8. Find minimum key\n";
        cout << "9. Find maximum key\n";
        cout << "10. Clear tree\n";
        cout << "11. Exit\n";
        cout << "Enter choice: ";
        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            int k; cout << "Enter key to insert: "; cin >> k;
            bst.insert_prh(k);
            cout << "Inserted " << k << "\n";
        } else if (ch == 2) {
            int k; cout << "Enter key to delete: "; cin >> k;
            bst.delete_prh(k);
        } else if (ch == 3) {
            int k; cout << "Enter key to search: "; cin >> k;
            bool found = bst.search_prh(k);
            cout << (found ? "Key found\n" : "Key not found\n");
        } else if (ch == 4) {
            cout << "Inorder: "; bst.inorder_prh();
        } else if (ch == 5) {
            cout << "Preorder: "; bst.preorder_prh();
        } else if (ch == 6) {
            cout << "Postorder: "; bst.postorder_prh();
        } else if (ch == 7) {
            cout << "Level-wise display:\n"; bst.levelOrder_prh();
        } else if (ch == 8) {
            try { cout << "Minimum key: " << bst.findMin_prh() << "\n"; }
            catch (const exception &e) { cout << "Error: " << e.what() << "\n"; }
        } else if (ch == 9) {
            try { cout << "Maximum key: " << bst.findMax_prh() << "\n"; }
            catch (const exception &e) { cout << "Error: " << e.what() << "\n"; }
        } else if (ch == 10) {
            bst.clear_prh();
            cout << "Tree cleared.\n";
        } else if (ch == 11) {
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
g++ -std=c++11 -O2 assignment31_bst_prasad_hargude.cpp -o assignment31_bst
./assignment31_bst
```

---

## 7. Example Runs and Test Cases (with expected outputs)

Sample sequence:
```
Insert: 50, 30, 70, 20, 40, 60, 80
Level-wise display:
Level 0: 50
Level 1: 30 70
Level 2: 20 40 60 80

Inorder: 20 30 40 50 60 70 80
Search 60 -> found
Delete 70
Level-wise:
Level 0: 50
Level 1: 30 80
Level 2: 20 40 60
```

Edge cases:
- Deleting a non-existent key prints message.
- Traversals on empty tree print nothing or (empty tree) for level-order.

---

## 8. Memory Management & Edge Cases
- `clear_prh()` recursively deletes all nodes to avoid memory leaks.
- All operations validate empty tree where appropriate.
- Recursive depth may reach O(n) in worst-case (skewed tree).

---

## 9. Extensions and Enhancements
- Self-balancing trees (AVL, Red-Black) to guarantee logarithmic height.
- Iterative versions of traversals to avoid recursion limits.
- Support duplicates (e.g., storing count in node) if needed.
- Augment nodes with subtree sizes, heights, or parent pointers.

---

## 10. References
- Standard BST textbooks and resources
- C++ STL and basic algorithms

