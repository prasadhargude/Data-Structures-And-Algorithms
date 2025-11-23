# Assignment 32: BST Operations — Count Nodes, Height, Mirror Image (Menu-Driven)
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
Write a C++ program to perform the following operations on a Binary Search Tree (BST):

- Count the total number of nodes in the BST.
- Compute the height of the BST.
- Generate the mirror image of the BST (i.e., convert left children to right and vice versa).
- Provide menu-driven options to create/insert nodes, display traversals, and the above operations.

All identifiers use the `_prh` suffix as requested.

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)  
- **Data structure:** Binary Search Tree with dynamic nodes (pointers)  
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

### BST operations provided
- `insert_prh(int key)`
- `countNodes_prh()` — returns total nodes (recursive)
- `height_prh()` — returns height (number of levels; empty tree height = 0)
- `mirror_prh()` — transforms tree into its mirror (in-place)
- Traversals: inorder, preorder, postorder
- `clear_prh()` to free memory

Design notes:
- Height definition: number of nodes along the longest path from root to leaf. (Alternatively some definitions use edges; here we use nodes so single-node tree has height 1.)
- Mirror operation is implemented by recursively swapping left and right child pointers.

---

## 4. Algorithms (pseudocode + complexity)

### Count nodes (recursive)
```
countNodes(node):
    if node == NULL: return 0
    return 1 + countNodes(node.left) + countNodes(node.right)
```
Time: O(n), Space: O(h) recursion

### Height (recursive)
```
height(node):
    if node == NULL: return 0
    return 1 + max(height(node.left), height(node.right))
```
Time: O(n), Space: O(h)

### Mirror (recursive in-place)
```
mirror(node):
    if node == NULL: return
    swap(node.left, node.right)
    mirror(node.left)
    mirror(node.right)
```
Time: O(n), Space: O(h)

---

## 5. C++ Implementation (complete, production-ready, menu-driven)

```cpp
// assignment32_bst_ops_prasad_hargude.cpp
// C++11 — BST operations: count nodes, compute height, mirror image; menu-driven
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

class BSTOps_prh {
private:
    Node_prh* root_prh;

    Node_prh* insertRec_prh(Node_prh* node, int key) {
        if (!node) return new Node_prh(key);
        if (key < node->key_prh) node->left_prh = insertRec_prh(node->left_prh, key);
        else if (key > node->key_prh) node->right_prh = insertRec_prh(node->right_prh, key);
        else cout << "Key " << key << " already exists; duplicates ignored\n";
        return node;
    }

    int countRec_prh(Node_prh* node) const {
        if (!node) return 0;
        return 1 + countRec_prh(node->left_prh) + countRec_prh(node->right_prh);
    }

    int heightRec_prh(Node_prh* node) const {
        if (!node) return 0;
        int hl = heightRec_prh(node->left_prh);
        int hr = heightRec_prh(node->right_prh);
        return 1 + (hl > hr ? hl : hr);
    }

    void mirrorRec_prh(Node_prh* node) {
        if (!node) return;
        Node_prh* tmp = node->left_prh;
        node->left_prh = node->right_prh;
        node->right_prh = tmp;
        mirrorRec_prh(node->left_prh);
        mirrorRec_prh(node->right_prh);
    }

    void inorderRec_prh(Node_prh* node) const {
        if (!node) return;
        inorderRec_prh(node->left_prh);
        cout << node->key_prh << " ";
        inorderRec_prh(node->right_prh);
    }

    void preorderRec_prh(Node_prh* node) const {
        if (!node) return;
        cout << node->key_prh << " ";
        preorderRec_prh(node->left_prh);
        preorderRec_prh(node->right_prh);
    }

    void postorderRec_prh(Node_prh* node) const {
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
    BSTOps_prh(): root_prh(nullptr) {}
    ~BSTOps_prh() { clear_prh(); }

    void insert_prh(int key) { root_prh = insertRec_prh(root_prh, key); }

    int countNodes_prh() const { return countRec_prh(root_prh); }

    int height_prh() const { return heightRec_prh(root_prh); }

    void mirror_prh() { mirrorRec_prh(root_prh); }

    void inorder_prh() const { inorderRec_prh(root_prh); cout << "\n"; }
    void preorder_prh() const { preorderRec_prh(root_prh); cout << "\n"; }
    void postorder_prh() const { postorderRec_prh(root_prh); cout << "\n"; }

    void clear_prh() { clearRec_prh(root_prh); root_prh = nullptr; }

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
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 32: BST Ops — Count Nodes, Height, Mirror (Menu Driven)\n";
    BSTOps_prh bst;

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Insert key\n";
        cout << "2. Inorder traversal\n";
        cout << "3. Preorder traversal\n";
        cout << "4. Postorder traversal\n";
        cout << "5. Level-order traversal\n";
        cout << "6. Count total nodes\n";
        cout << "7. Compute height of BST\n";
        cout << "8. Mirror the BST (in-place)\n";
        cout << "9. Clear tree\n";
        cout << "10. Exit\n";
        cout << "Enter choice: ";
        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            int k; cout << "Enter key to insert: "; cin >> k;
            bst.insert_prh(k);
            cout << "Inserted " << k << "\n";
        } else if (ch == 2) {
            cout << "Inorder: "; bst.inorder_prh();
        } else if (ch == 3) {
            cout << "Preorder: "; bst.preorder_prh();
        } else if (ch == 4) {
            cout << "Postorder: "; bst.postorder_prh();
        } else if (ch == 5) {
            cout << "Level-order: "; bst.levelOrder_prh();
        } else if (ch == 6) {
            cout << "Total nodes: " << bst.countNodes_prh() << "\n";
        } else if (ch == 7) {
            cout << "Height of BST: " << bst.height_prh() << "\n";
        } else if (ch == 8) {
            bst.mirror_prh();
            cout << "BST mirrored (left/right children swapped).\n";
        } else if (ch == 9) {
            bst.clear_prh();
            cout << "Tree cleared.\n";
        } else if (ch == 10) {
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
g++ -std=c++11 -O2 assignment32_bst_ops_prasad_hargude.cpp -o assignment32_bst_ops
./assignment32_bst_ops
```

---

## 7. Example Runs and Test Cases (with expected outputs)

Sample session:
```
Insert: 50 30 70 20 40 60 80
Inorder: 20 30 40 50 60 70 80
Count total nodes -> 7
Height -> 3
Mirror -> performed
Inorder after mirror -> 80 70 60 50 40 30 20
Height remains -> 3
Clear -> tree empty
```

Edge cases:
- Count and height on empty tree return 0.
- Mirroring empty tree is a no-op.

---

## 8. Memory Management & Edge Cases
- `clear_prh()` frees all nodes to avoid memory leaks.
- Recursive functions use O(h) stack frames; worst-case O(n) for skewed tree.
- Input validation limited to integer keys (non-integers are rejected by `cin`).

---

## 9. Extensions and Enhancements
- Provide a function to return a mirrored copy rather than in-place mutation.
- Implement iterative versions of these operations.
- Balance the BST (AVL or Red-Black) to guarantee logarithmic height.
- Add node deletion and other utilities.

---

## 10. References
- Standard BST algorithms and textbooks
- C++ reference for std::queue and I/O

