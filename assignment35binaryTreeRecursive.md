# Assignment 35: Binary Tree — Recursive Operations (Inorder, Preorder, Leaf Count, Mirror)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready, recursive)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References

---

## 1. Problem Statement
Write a C++ program to create a **binary tree** and perform the following **recursive** operations:

a. Inorder Traversal (recursive)  
b. Preorder Traversal (recursive)  
c. Display the number of leaf nodes (recursive)  
d. Mirror image of the tree (recursive, in-place)

The program must be **menu-driven** and use the `_prh` naming convention.

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)  
- **Data structure:** Binary tree using dynamic nodes (pointers)  
- **Traversals & operations:** Use recursive algorithms for the listed operations.  
- **Interface:** Console-based, menu-driven

---

## 3. Data Structure & Design

### Node
```cpp
struct Node_prh {
    int data_prh;
    Node_prh* left_prh;
    Node_prh* right_prh;
    Node_prh(int v): data_prh(v), left_prh(nullptr), right_prh(nullptr) {}
};
```

### Tree operations
- Build tree by inserting nodes level-order (first available spot) to create a general binary tree.
- Recursive inorder and preorder traversals.
- Leaf count implemented recursively.
- Mirror implemented recursively by swapping children at each node.

---

## 4. Algorithms (pseudocode + complexity)

### Insert (level-order)
```
insert(root, value):
    if root == NULL: root = new Node(value); return
    use queue:
    push root
    while queue:
        node = pop
        if node.left == NULL: node.left = new Node(value); return
        else push node.left
        if node.right == NULL: node.right = new Node(value); return
        else push node.right
```
Time: O(n) worst-case to find spot.

### Recursive Inorder
```
inorder(node):
    if node == NULL: return
    inorder(node.left)
    visit node
    inorder(node.right)
```
Time: O(n), Space: O(h)

### Recursive Preorder
```
preorder(node):
    if node == NULL: return
    visit node
    preorder(node.left)
    preorder(node.right)
```
Time: O(n), Space: O(h)

### Recursive Leaf Count
```
countLeaves(node):
    if node == NULL: return 0
    if node.left == NULL and node.right == NULL: return 1
    return countLeaves(node.left) + countLeaves(node.right)
```
Time: O(n)

### Recursive Mirror (in-place)
```
mirror(node):
    if node == NULL: return
    swap(node.left, node.right)
    mirror(node.left)
    mirror(node.right)
```
Time: O(n)

---

## 5. C++ Implementation (menu-driven, recursive)
```cpp
// assignment35_bintree_rec_prasad_hargude.cpp
// C++11 — Binary tree recursive inorder/preorder, leaf count, mirror
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <queue>
#include <limits>

using namespace std;

struct Node_prh {
    int data_prh;
    Node_prh* left_prh;
    Node_prh* right_prh;
    Node_prh(int v): data_prh(v), left_prh(nullptr), right_prh(nullptr) {}
};

class BinaryTreeRec_prh {
private:
    Node_prh* root_prh;

    // recursive traversals & ops
    void inorderRec_prh(Node_prh* node) const {
        if (!node) return;
        inorderRec_prh(node->left_prh);
        cout << node->data_prh << " ";
        inorderRec_prh(node->right_prh);
    }

    void preorderRec_prh(Node_prh* node) const {
        if (!node) return;
        cout << node->data_prh << " ";
        preorderRec_prh(node->left_prh);
        preorderRec_prh(node->right_prh);
    }

    int countLeavesRec_prh(Node_prh* node) const {
        if (!node) return 0;
        if (!node->left_prh && !node->right_prh) return 1;
        return countLeavesRec_prh(node->left_prh) + countLeavesRec_prh(node->right_prh);
    }

    void mirrorRec_prh(Node_prh* node) {
        if (!node) return;
        Node_prh* tmp = node->left_prh;
        node->left_prh = node->right_prh;
        node->right_prh = tmp;
        mirrorRec_prh(node->left_prh);
        mirrorRec_prh(node->right_prh);
    }

    void clearRec_prh(Node_prh* node) {
        if (!node) return;
        clearRec_prh(node->left_prh);
        clearRec_prh(node->right_prh);
        delete node;
    }

public:
    BinaryTreeRec_prh(): root_prh(nullptr) {}
    ~BinaryTreeRec_prh() { clear_prh(); }

    // level-order insertion to build a general binary tree
    void insert_prh(int value) {
        Node_prh* node = new Node_prh(value);
        if (!root_prh) { root_prh = node; return; }
        queue<Node_prh*> q;
        q.push(root_prh);
        while (!q.empty()) {
            Node_prh* cur = q.front(); q.pop();
            if (!cur->left_prh) { cur->left_prh = node; return; }
            else q.push(cur->left_prh);
            if (!cur->right_prh) { cur->right_prh = node; return; }
            else q.push(cur->right_prh);
        }
    }

    void inorder_prh() const {
        inorderRec_prh(root_prh);
        cout << "\n";
    }

    void preorder_prh() const {
        preorderRec_prh(root_prh);
        cout << "\n";
    }

    int countLeaves_prh() const {
        return countLeavesRec_prh(root_prh);
    }

    void mirror_prh() {
        mirrorRec_prh(root_prh);
    }

    void levelOrder_prh() const {
        if (!root_prh) { cout << "(empty tree)\n"; return; }
        queue<Node_prh*> q;
        q.push(root_prh);
        while (!q.empty()) {
            Node_prh* cur = q.front(); q.pop();
            cout << cur->data_prh << " ";
            if (cur->left_prh) q.push(cur->left_prh);
            if (cur->right_prh) q.push(cur->right_prh);
        }
        cout << "\n";
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
    cout << "Assignment 35: Binary Tree Recursive Operations (Menu Driven)\n";
    BinaryTreeRec_prh tree;
    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Insert node (level-order insertion)\n";
        cout << "2. Recursive Inorder traversal\n";
        cout << "3. Recursive Preorder traversal\n";
        cout << "4. Display number of leaf nodes (recursive)\n";
        cout << "5. Mirror image (recursive, in-place)\n";
        cout << "6. Level-order display\n";
        cout << "7. Clear tree\n";
        cout << "8. Exit\n";
        cout << "Enter choice: ";
        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            int v; cout << "Enter integer value to insert: "; cin >> v;
            tree.insert_prh(v);
            cout << "Inserted " << v << "\n";
        } else if (ch == 2) {
            cout << "Inorder (recursive): "; tree.inorder_prh();
        } else if (ch == 3) {
            cout << "Preorder (recursive): "; tree.preorder_prh();
        } else if (ch == 4) {
            cout << "Leaf nodes count: " << tree.countLeaves_prh() << "\n";
        } else if (ch == 5) {
            tree.mirror_prh();
            cout << "Tree mirrored in-place.\n";
        } else if (ch == 6) {
            cout << "Level-order: "; tree.levelOrder_prh();
        } else if (ch == 7) {
            tree.clear_prh();
            cout << "Tree cleared.\n";
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
g++ -std=c++11 -O2 assignment35_bintree_rec_prasad_hargude.cpp -o assignment35_bintree_rec
./assignment35_bintree_rec
```

---

## 7. Example Runs and Test Cases

Sample session:
```
Insert: 10 20 30 40 50
Level-order -> 10 20 30 40 50
Inorder (rec) -> 40 20 50 10 30  (depending on insertion order)
Preorder (rec) -> 10 20 40 50 30
Leaf count -> 3
Mirror -> performed
Level-order after mirror -> 10 30 20 50 40
```

---

## 8. Memory Management & Edge Cases
- All nodes freed by `clear_prh()`; destructor calls it on program exit.
- Recursive functions use O(h) stack frames; deep skewed trees may risk stack overflow.
- Insertions use level-order; tree is not a BST by default.

---

## 9. Extensions and Enhancements
- Add deletion by value, searching, or building tree from traversals.
- Provide iterative alternatives as an exercise.
- Visualize tree using a simple text layout or GUI.

---

## 10. References
- Standard recursive tree traversal algorithms
- C++ reference for queue and I/O
