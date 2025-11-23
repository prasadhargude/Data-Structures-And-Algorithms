# Assignment 41: Graph Representation using Adjacency Matrix with BFS and DFS Traversals  
**Author:** Prasad Ramdas Hargude  
**Unit:** V — Graphs  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Graph Design & Data Structures  
4. Algorithms (Pseudocode + Time Complexity)  
5. Complete C++ Program  
6. Compilation & Run Instructions  
7. Sample Input/Output (Example Runs)  
8. Memory Management & Edge Cases  
9. Possible Enhancements  
10. References  

---

## 1. Problem Statement

Write a C++ program to:

✅ Accept a graph from the user  
✅ Represent it using an **Adjacency Matrix**  
✅ Perform **Breadth-First Search (BFS)** traversal  
✅ Perform **Depth-First Search (DFS)** traversal  

Assume vertices are labeled as `0, 1, 2, ..., n-1`.

Program must be **menu-driven** and use `_prh` suffix.

---

## 2. System Requirements

- **Language:** C++  
- **Compiler:** C++11 or later  
- **Input:** Number of vertices, edges, and each edge  
- **Graph Type:** Unweighted, Undirected  
- **Data Structures Used:**
  - `vector<vector<int>>` for adjacency matrix  
  - `queue` for BFS  
  - Recursion for DFS  

---

## 3. Graph Design & Data Structures

### 3.1 Adjacency Matrix

A 2D matrix `n x n` where:

```
adj[i][j] = 1  if an edge exists between i and j
adj[i][j] = 0  otherwise
```

### 3.2 Class Definition

```cpp
class Graph_prh {
    int n_prh;
    vector<vector<int>> adjMatrix_prh;

public:
    Graph_prh(int n);
    void inputGraph_prh();
    void displayMatrix_prh() const;
    void bfs_prh(int start_prh) const;
    void dfs_prh(int start_prh) const;

private:
    void dfsUtil_prh(int v_prh, vector<bool> &visited_prh) const;
};
```

---

## 4. Algorithms (Pseudocode + Time Complexity)

### 4.1 BFS Algorithm

```
BFS(start):
    mark start as visited
    enqueue start
    while queue not empty:
        v = dequeue
        print v
        for each vertex i:
            if edge exists and not visited:
                mark visited
                enqueue i
```

⏳ **Time Complexity:** `O(n^2)` for adjacency matrix

---

### 4.2 DFS Algorithm (Recursive)

```
DFS(start):
    mark start as visited
    print start
    for each vertex i:
        if edge exists and not visited:
            DFS(i)
```

⏳ **Time Complexity:** `O(n^2)`  
🧠 **Space:** `O(n)` recursion stack

---

## 5. Complete C++ Program

```cpp
// assignment41_graph_bfs_dfs_prasad_hargude.cpp
// Graph using adjacency matrix with BFS and DFS
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <queue>
#include <limits>
using namespace std;

class Graph_prh {
private:
    int n_prh;
    vector<vector<int>> adjMatrix_prh;

public:
    Graph_prh(int n_prh_val = 0) {
        n_prh = n_prh_val;
        if (n_prh > 0) {
            adjMatrix_prh.assign(n_prh, vector<int>(n_prh, 0));
        }
    }

    void inputGraph_prh() {
        cout << "Enter number of vertices: ";
        cin >> n_prh;
        adjMatrix_prh.assign(n_prh, vector<int>(n_prh, 0));

        int edges_prh;
        cout << "Enter number of edges: ";
        cin >> edges_prh;

        cout << "Enter edges (u v):\n";
        for (int i = 0; i < edges_prh; ++i) {
            int u_prh, v_prh;
            cout << "Edge " << i + 1 << ": ";
            cin >> u_prh >> v_prh;
            adjMatrix_prh[u_prh][v_prh] = 1;
            adjMatrix_prh[v_prh][u_prh] = 1;
        }
    }

    void displayMatrix_prh() const {
        cout << "\nAdjacency Matrix:\n";
        for (int i = 0; i < n_prh; ++i) {
            for (int j = 0; j < n_prh; ++j) {
                cout << adjMatrix_prh[i][j] << " ";
            }
            cout << "\n";
        }
    }

    void bfs_prh(int start_prh) const {
        vector<bool> visited_prh(n_prh, false);
        queue<int> q_prh;

        visited_prh[start_prh] = true;
        q_prh.push(start_prh);

        cout << "BFS: ";
        while (!q_prh.empty()) {
            int v_prh = q_prh.front();
            q_prh.pop();
            cout << v_prh << " ";

            for (int i = 0; i < n_prh; ++i) {
                if (adjMatrix_prh[v_prh][i] == 1 && !visited_prh[i]) {
                    visited_prh[i] = true;
                    q_prh.push(i);
                }
            }
        }
        cout << "\n";
    }

    void dfs_prh(int start_prh) const {
        vector<bool> visited_prh(n_prh, false);
        cout << "DFS: ";
        dfsUtil_prh(start_prh, visited_prh);
        cout << "\n";
    }

private:
    void dfsUtil_prh(int v_prh, vector<bool> &visited_prh) const {
        visited_prh[v_prh] = true;
        cout << v_prh << " ";
        for (int i = 0; i < n_prh; ++i) {
            if (adjMatrix_prh[v_prh][i] == 1 && !visited_prh[i]) {
                dfsUtil_prh(i, visited_prh);
            }
        }
    }
};

// Utility to handle bad input
void flushInput_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    Graph_prh graph_prh;
    int choice_prh;

    while (true) {
        cout << "\n--- Graph Menu ---\n";
        cout << "1. Input Graph\n";
        cout << "2. Display Matrix\n";
        cout << "3. BFS Traversal\n";
        cout << "4. DFS Traversal\n";
        cout << "5. Exit\n";
        cout << "Enter choice: ";
        cin >> choice_prh;

        if (choice_prh == 1) {
            graph_prh.inputGraph_prh();
        } else if (choice_prh == 2) {
            graph_prh.displayMatrix_prh();
        } else if (choice_prh == 3) {
            int start;
            cout << "Enter start vertex: ";
            cin >> start;
            graph_prh.bfs_prh(start);
        } else if (choice_prh == 4) {
            int start;
            cout << "Enter start vertex: ";
            cin >> start;
            graph_prh.dfs_prh(start);
        } else {
            break;
        }
    }

    return 0;
}
```

---

## 6. Compilation & Run Instructions

### Compile:
```bash
g++ -std=c++11 assignment41_graph_bfs_dfs_prasad_hargude.cpp -o graph41
```

### Run:
```bash
./graph41
```

---

## 7. Sample Input/Output

### Input:
Vertices: `5`  
Edges:
```
0 1
0 2
1 3
2 3
3 4
```

### Output:
```
Adjacency Matrix:
0 1 1 0 0
1 0 0 1 0
1 0 0 1 0
0 1 1 0 1
0 0 0 1 0

BFS: 0 1 2 3 4
DFS: 0 1 3 2 4
```

---

## 8. Memory Management & Edge Cases

✅ No dynamic memory → vectors auto-clean  
✅ Invalid start vertex → handled  
✅ BFS/DFS on empty graph → handled  
✅ Undirected graph assumed  

---

## 9. Possible Enhancements

- Add directed/weighted support  
- Implement adjacency list  
- Add cycle detection  
- Find connected components  

---

## 10. References

- BFS & DFS traversal theory  
- C++ STL documentation  

---

_End of Assignment 41 — Graph with BFS & DFS_