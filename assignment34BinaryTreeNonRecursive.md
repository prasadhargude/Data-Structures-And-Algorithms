# Assignment 34: Binary Tree — Non-Recursive Operations (Inorder, Preorder, Leaf Count, Mirror Image)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready, **non-recursive** where requested)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References

---

## 1. Problem Statement
Write a C++ program to create a **binary tree** and perform the following **non-recursive** operations:

a. Inorder Traversal (non-recursive)  
b. Preorder Traversal (non-recursive)  
c. Display the number of leaf nodes (non-recursive)  
d. Mirror image of the tree (non-recursive, in-place)

The program must be **menu-driven** and use the `_prh` naming convention.

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)  
- **Data structure:** Binary tree using dynamic nodes (pointers)  
- **Traversals & operations:** Use iterative algorithms (stack or queue) — **no recursion** for the listed operations.  
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
- Build tree by inserting nodes (as a binary tree, not necessarily BST). For simplicity the `insert_prh` will insert nodes level-order (first available spot) to create a general binary tree.
- Non-recursive inorder and preorder use `std::stack`.
- Leaf count uses iterative traversal (BFS or stack DFS).
- Mirror uses iterative traversal (stack or queue) swapping left/right pointers.

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

### Non-recursive Inorder
```
inorderIter(root):
    stack = empty
    curr = root
    while curr != NULL or stack not empty:
        while curr != NULL:
            stack.push(curr)
            curr = curr.left
        curr = stack.top(); stack.pop()
        visit curr
        curr = curr.right
```
Time: O(n), Space: O(h)

### Non-recursive Preorder
```
preorderIter(root):
    if root == NULL: return
    stack.push(root)
    while stack not empty:
        node = stack.top(); stack.pop()
        visit node
        if node.right: stack.push(node.right)
        if node.left: stack.push(node.left)
```
Time: O(n), Space: O(h)

### Leaf Count (iterative)
Use BFS or stack:
```
countLeaves(root):
    if root==NULL return 0
    cnt=0
    queue.push(root)
    while queue:
        node = pop
        if node.left==NULL and node.right==NULL: cnt++
        if node.left: push
        if node.right: push
    return cnt
```
Time: O(n)

### Mirror Image (iterative, in-place)
Use stack/queue:
```
mirror(root):
    if root==NULL return
    stack.push(root)
    while stack:
        node = stack.pop()
        swap(node.left, node.right)
        if node.left: push node.left
        if node.right: push node.right
```
Time: O(n)

---

## 5. C++ Implementation (menu-driven, non-recursive)
```cpp
// assignment34_bintree_nonrec_prasad_hargude.cpp
// C++11 — Binary tree non-recursive inorder/preorder, leaf count, mirror
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <queue>
#include <stack>
#include <limits>

using namespace std;

struct Node_prh {
    int data_prh;
    Node_prh* left_prh;
    Node_prh* right_prh;
    Node_prh(int v): data_prh(v), left_prh(nullptr), right_prh(nullptr) {}
};

class BinaryTree_prh {
private:
    Node_prh* root_prh;

public:
    BinaryTree_prh(): root_prh(nullptr) {}
    ~BinaryTree_prh() { clear_prh(); }

    // Insert level-order (first available spot) so tree remains roughly balanced
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

    // Non-recursive inorder traversal
    void inorderIter_prh() const {
        stack<Node_prh*> st;
        Node_prh* cur = root_prh;
        cout << "(Inorder) ";
        while (cur || !st.empty()) {
            while (cur) {
                st.push(cur);
                cur = cur->left_prh;
            }
            cur = st.top(); st.pop();
            cout << cur->data_prh << " ";
            cur = cur->right_prh;
        }
        cout << "\n";
    }

    // Non-recursive preorder traversal
    void preorderIter_prh() const {
        if (!root_prh) { cout << "(Preorder) (empty)\n"; return; }
        stack<Node_prh*> st;
        st.push(root_prh);
        cout << "(Preorder) ";
        while (!st.empty()) {
            Node_prh* cur = st.top(); st.pop();
            cout << cur->data_prh << " ";
            if (cur->right_prh) st.push(cur->right_prh);
            if (cur->left_prh) st.push(cur->left_prh);
        }
        cout << "\n";
    }

    // Count leaf nodes using iterative BFS
    int countLeaves_prh() const {
        if (!root_prh) return 0;
        int cnt = 0;
        queue<Node_prh*> q;
        q.push(root_prh);
        while (!q.empty()) {
            Node_prh* cur = q.front(); q.pop();
            if (!cur->left_prh && !cur->right_prh) ++cnt;
            if (cur->left_prh) q.push(cur->left_prh);
            if (cur->right_prh) q.push(cur->right_prh);
        }
        return cnt;
    }

    // Mirror the tree in-place using iterative stack
    void mirror_prh() {
        if (!root_prh) return;
        stack<Node_prh*> st;
        st.push(root_prh);
        while (!st.empty()) {
            Node_prh* cur = st.top(); st.pop();
            // swap children
            Node_prh* tmp = cur->left_prh;
            cur->left_prh = cur->right_prh;
            cur->right_prh = tmp;
            if (cur->left_prh) st.push(cur->left_prh);
            if (cur->right_prh) st.push(cur->right_prh);
        }
    }

    // Level-order display (for user verification)
    void levelOrder_prh() const {
        if (!root_prh) { cout << "(empty tree)\n"; return; }
        queue<Node_prh*> q;
        q.push(root_prh);
        cout << "(Level-order) ";
        while (!q.empty()) {
            Node_prh* cur = q.front(); q.pop();
            cout << cur->data_prh << " ";
            if (cur->left_prh) q.push(cur->left_prh);
            if (cur->right_prh) q.push(cur->right_prh);
        }
        cout << "\n";
    }

    void clear_prh() {
        if (!root_prh) return;
        // iterative post-order deletion using two stacks
        stack<Node_prh*> s1, s2;
        s1.push(root_prh);
        while (!s1.empty()) {
            Node_prh* n = s1.top(); s1.pop();
            s2.push(n);
            if (n->left_prh) s1.push(n->left_prh);
            if (n->right_prh) s1.push(n->right_prh);
        }
        while (!s2.empty()) {
            delete s2.top(); s2.pop();
        }
        root_prh = nullptr;
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 34: Binary Tree Non-Recursive Operations (Menu Driven)\n";
    BinaryTree_prh tree;
    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Insert node (level-order insertion)\n";
        cout << "2. Non-recursive Inorder traversal\n";
        cout << "3. Non-recursive Preorder traversal\n";
        cout << "4. Display number of leaf nodes (non-recursive)\n";
        cout << "5. Mirror image (non-recursive, in-place)\n";
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
            tree.inorderIter_prh();
        } else if (ch == 3) {
            tree.preorderIter_prh();
        } else if (ch == 4) {
            cout << "Leaf nodes count: " << tree.countLeaves_prh() << "\n";
        } else if (ch == 5) {
            tree.mirror_prh();
            cout << "Tree mirrored in-place.\n";
        } else if (ch == 6) {
            tree.levelOrder_prh();
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
g++ -std=c++11 -O2 assignment34_bintree_nonrec_prasad_hargude.cpp -o assignment34_bintree_nonrec
./assignment34_bintree_nonrec
```

---

## 7. Example Runs and Test Cases

Sample session:
```
Insert: 10 20 30 40 50
Level-order -> 10 20 30 40 50
Inorder (iter) -> 40 20 50 10 30  (depending on insertion order)
Preorder (iter) -> 10 20 40 50 30
Leaf count -> 3
Mirror -> performed
Level-order after mirror -> 10 30 20 50 40
```

Notes: level-order insertion yields a complete tree structure; traversals depend on exact insert sequence.

---

## 8. Memory Management & Edge Cases
- All dynamic nodes are deleted by `clear_prh()` using iterative postorder deletion to avoid recursion.
- Insertions use level-order; this produces a general binary tree (not BST).
- Non-recursive algorithms avoid recursion depth issues.

---

## 9. Extensions and Enhancements
- Allow deletion of arbitrary node or deletion by value.
- Build tree from user-provided list or from preorder/inorder sequences.
- Provide a GUI or visualization for the tree and mirror operation.

---

## 10. References
- Iterative (stack/queue) tree traversal algorithms
- Standard C++ documentation for stack and queue
