# Assignment 45: Graph Representation using Adjacency List with BFS and DFS Traversals  
**Author:** Prasad Ramdas Hargude  
**Unit:** V — Graphs  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Graph Design & Data Structures  
4. Algorithms (Pseudocode + Time Complexity)  
5. Complete C++ Program (Adjacency List + BFS + DFS)  
6. Compilation & Run Instructions  
7. Sample Input/Output (Example Runs)  
8. Memory Management & Edge Cases  
9. Possible Enhancements  
10. References  

---

## 1. Problem Statement

Write a C++ program to:

- **Accept a graph from the user**  
- **Represent it using an Adjacency List**  
- Perform **Breadth-First Search (BFS)** traversal  
- Perform **Depth-First Search (DFS)** traversal  

Assume vertices are labeled as `0, 1, 2, ..., n-1`.  

The program should be **menu-driven** and use `_prh` suffix in identifiers.

> Note: We assume an **unweighted, undirected** graph. You can extend it to a directed graph by adjusting how edges are added.

---

## 2. System Requirements

- **Language:** C++ (C++11 or later)  
- **Compiler:** g++ / any standard C++ compiler  
- **Graph Type:**  
  - Unweighted  
  - Undirected  
- **Representation:**  
  - Adjacency List: `vector<vector<int>>`  

---

## 3. Graph Design & Data Structures

### 3.1 Vertex Numbering

Vertices are identified as:

```text
0, 1, 2, ..., n_prh - 1
```

### 3.2 Adjacency List Representation

We use:

```cpp
vector<vector<int>> adjList_prh;
```

- `adjList_prh[u_prh]` stores all vertices `v_prh` such that there is an edge `u_prh — v_prh`.  
- Since the graph is **undirected**:

```cpp
adjList_prh[u_prh].push_back(v_prh);
adjList_prh[v_prh].push_back(u_prh);
```

### 3.3 Class Design

```cpp
class Graph_prh {
    int n_prh;                       // number of vertices
    vector<vector<int>> adjList_prh; // adjacency list

public:
    Graph_prh(int n_prh_val = 0);
    void inputGraph_prh();
    void addEdge_prh(int u_prh, int v_prh);
    void displayAdjList_prh() const;
    void bfs_prh(int start_prh) const;
    void dfs_prh(int start_prh) const;

private:
    void dfsUtil_prh(int v_prh, vector<bool> &visited_prh) const;
};
```

---

## 4. Algorithms (Pseudocode + Time Complexity)

### 4.1 BFS (Breadth-First Search)

#### Pseudocode

```text
BFS(start):
    create visited[n] = {false}
    create queue Q
    mark visited[start] = true
    enqueue(start)

    while Q not empty:
        v = dequeue
        print v
        for each neighbor u in adj[v]:
            if not visited[u]:
                visited[u] = true
                enqueue(u)
```

**Time Complexity:**  
- `O(V + E)` using adjacency list  

**Space Complexity:**  
- `O(V)` for visited + queue  

---

### 4.2 DFS (Depth-First Search) – Recursive

#### Pseudocode

```text
DFS(start):
    visited[n] = {false}
    dfsUtil(start, visited)

dfsUtil(v, visited):
    visited[v] = true
    print v
    for each neighbor u in adj[v]:
        if not visited[u]:
            dfsUtil(u, visited)
```

**Time Complexity:**  
- `O(V + E)`  

**Space Complexity:**  
- `O(V)` for visited + recursion stack  

---

## 5. Complete C++ Program (Adjacency List + BFS + DFS)

```cpp
// assignment45_graph_adjlist_bfs_dfs_prasad_hargude.cpp
// Graph representation using Adjacency List with BFS and DFS traversals
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <queue>
#include <limits>
using namespace std;

class Graph_prh {
private:
    int n_prh;                         // number of vertices
    vector<vector<int>> adjList_prh;   // adjacency list

public:
    Graph_prh(int n_prh_val = 0) {
        n_prh = n_prh_val;
        if (n_prh > 0) {
            adjList_prh.assign(n_prh, vector<int>());
        }
    }

    void inputGraph_prh() {
        cout << "Enter number of vertices: ";
        cin >> n_prh;
        if (n_prh <= 0) {
            cout << "Invalid number of vertices.\n";
            return;
        }
        adjList_prh.assign(n_prh, vector<int>());

        int edges_prh;
        cout << "Enter number of edges: ";
        cin >> edges_prh;

        cout << "Enter edges as pairs (u v) for an undirected graph (0-based indices):\n";
        for (int i = 0; i < edges_prh; ++i) {
            int u_prh, v_prh;
            cout << "Edge " << i + 1 << ": ";
            cin >> u_prh >> v_prh;
            if (u_prh < 0 || u_prh >= n_prh || v_prh < 0 || v_prh >= n_prh) {
                cout << "Invalid vertices. Skipping this edge.\n";
                continue;
            }
            addEdge_prh(u_prh, v_prh);
        }
    }

    void addEdge_prh(int u_prh, int v_prh) {
        adjList_prh[u_prh].push_back(v_prh);
        adjList_prh[v_prh].push_back(u_prh); // undirected
    }

    void displayAdjList_prh() const {
        if (n_prh <= 0) {
            cout << "Graph is empty.\n";
            return;
        }
        cout << "\nAdjacency List representation:\n";
        for (int i = 0; i < n_prh; ++i) {
            cout << i << " -> ";
            for (int v_prh : adjList_prh[i]) {
                cout << v_prh << " ";
            }
            cout << "\n";
        }
    }

    void bfs_prh(int start_prh) const {
        if (n_prh <= 0) {
            cout << "Graph is empty.\n";
            return;
        }
        if (start_prh < 0 || start_prh >= n_prh) {
            cout << "Invalid start vertex.\n";
            return;
        }

        vector<bool> visited_prh(n_prh, false);
        queue<int> q_prh;

        visited_prh[start_prh] = true;
        q_prh.push(start_prh);

        cout << "BFS traversal starting from " << start_prh << ": ";

        while (!q_prh.empty()) {
            int v_prh = q_prh.front();
            q_prh.pop();
            cout << v_prh << " ";

            for (int neighbor_prh : adjList_prh[v_prh]) {
                if (!visited_prh[neighbor_prh]) {
                    visited_prh[neighbor_prh] = true;
                    q_prh.push(neighbor_prh);
                }
            }
        }
        cout << "\n";
    }

    void dfs_prh(int start_prh) const {
        if (n_prh <= 0) {
            cout << "Graph is empty.\n";
            return;
        }
        if (start_prh < 0 || start_prh >= n_prh) {
            cout << "Invalid start vertex.\n";
            return;
        }

        vector<bool> visited_prh(n_prh, false);
        cout << "DFS traversal starting from " << start_prh << ": ";
        dfsUtil_prh(start_prh, visited_prh);
        cout << "\n";
    }

private:
    void dfsUtil_prh(int v_prh, vector<bool> &visited_prh) const {
        visited_prh[v_prh] = true;
        cout << v_prh << " ";
        for (int neighbor_prh : adjList_prh[v_prh]) {
            if (!visited_prh[neighbor_prh]) {
                dfsUtil_prh(neighbor_prh, visited_prh);
            }
        }
    }
};

void flushInput_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    Graph_prh graph_prh;
    int choice_prh;

    while (true) {
        cout << "\n--- Graph Menu (Adjacency List + BFS/DFS) ---\n";
        cout << "1. Input graph\n";
        cout << "2. Display adjacency list\n";
        cout << "3. BFS traversal\n";
        cout << "4. DFS traversal\n";
        cout << "5. Exit\n";
        cout << "Enter your choice: ";
        if (!(cin >> choice_prh)) {
            cout << "Invalid input. Try again.\n";
            flushInput_prh();
            continue;
        }

        if (choice_prh == 1) {
            graph_prh.inputGraph_prh();
        } else if (choice_prh == 2) {
            graph_prh.displayAdjList_prh();
        } else if (choice_prh == 3) {
            int start_prh;
            cout << "Enter start vertex for BFS: ";
            cin >> start_prh;
            graph_prh.bfs_prh(start_prh);
        } else if (choice_prh == 4) {
            int start_prh;
            cout << "Enter start vertex for DFS: ";
            cin >> start_prh;
            graph_prh.dfs_prh(start_prh);
        } else if (choice_prh == 5) {
            cout << "Exiting...\n";
            break;
        } else {
            cout << "Invalid choice. Try again.\n";
        }
    }

    return 0;
}
```

---

## 6. Compilation & Run Instructions

### Compile

```bash
g++ -std=c++11 assignment45_graph_adjlist_bfs_dfs_prasad_hargude.cpp -o graph45
```

### Run

```bash
./graph45
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment45_graph_adjlist_bfs_dfs_prasad_hargude.cpp -o graph45.exe
graph45.exe
```

---

## 7. Sample Input/Output (Example Runs)

### Example Graph

Vertices: `5` (0,1,2,3,4)  
Edges: `5`  

Edges:

- `0 1`  
- `0 2`  
- `1 3`  
- `2 3`  
- `3 4`  

### Sample Session

```text
--- Graph Menu (Adjacency List + BFS/DFS) ---
1. Input graph
2. Display adjacency list
3. BFS traversal
4. DFS traversal
5. Exit
Enter your choice: 1
Enter number of vertices: 5
Enter number of edges: 5
Enter edges as pairs (u v) for an undirected graph (0-based indices):
Edge 1: 0 1
Edge 2: 0 2
Edge 3: 1 3
Edge 4: 2 3
Edge 5: 3 4

Enter your choice: 2

Adjacency List representation:
0 -> 1 2
1 -> 0 3
2 -> 0 3
3 -> 1 2 4
4 -> 3

Enter your choice: 3
Enter start vertex for BFS: 0
BFS traversal starting from 0: 0 1 2 3 4

Enter your choice: 4
Enter start vertex for DFS: 0
DFS traversal starting from 0: 0 1 3 2 4
```

---

## 8. Memory Management & Edge Cases

- Adjacency list uses `vector`, which automatically manages memory.  
- Edge cases handled:
  - Invalid vertices on edge input → edge skipped.  
  - BFS/DFS on empty graph prints appropriate message.  
  - Start vertex out of range is detected and reported.  

---

## 9. Possible Enhancements

- Add support for **directed graphs**.  
- Extend to **weighted graphs** and apply algorithms like Dijkstra or Prim.  
- Detect **connected components** using DFS/BFS.  
- Add functionality to check if the graph is **bipartite**.

---

## 10. References

- BFS and DFS traversal algorithms (standard DSA references).  
- C++ STL (`vector`, `queue`) documentation.  

---

_End of Assignment 45 — Graph (Adjacency List) with BFS & DFS Traversals_
