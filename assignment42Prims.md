# Assignment 42: Prim’s Algorithm using Adjacency List (Minimum Spanning Tree)
**Author:** Prasad Ramdas Hargude  
**Unit:** V — Graphs  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Graph Representation & Data Structures  
4. Prim’s Algorithm – Concept & Pseudocode  
5. Time Complexity Analysis  
6. Complete C++ Program (Adjacency List + Prim’s MST)  
7. Compilation & Run Instructions  
8. Sample Input/Output (Example Run)  
9. Memory Management & Edge Cases  
10. Possible Enhancements  
11. References  

---

## 1. Problem Statement

Write a **C++ program** to:

- Accept a **connected, weighted, undirected graph** from the user.  
- Represent the graph using an **Adjacency List**.  
- Implement **Prim’s Algorithm** to find the **Minimum Spanning Tree (MST)** of the given graph.  
- Display:
  - The edges selected in the MST  
  - The total minimum cost of the MST  

All important identifiers use the `_prh` suffix.

---

## 2. System Requirements

- **Language:** C++  
- **Compiler:** Any C++11 compatible compiler (e.g., `g++`)  
- **Graph Type:**  
  - Weighted  
  - Undirected  
  - Connected (assumed for MST)  
- **Representation:** Adjacency List (`vector<vector<pair<int,int>>>`)  
- **Input Method:** Console (user enters number of vertices, edges, and each edge’s endpoints + weight)  

---

## 3. Graph Representation & Data Structures

### 3.1 Vertex Labeling

Vertices are labeled as integers:  
`0, 1, 2, ..., n-1`

### 3.2 Adjacency List

For a vertex `u`, `adjList_prh[u]` holds all pairs `(v, w)` such that there is an edge `u --(w)--> v`.

```cpp
class Graph_prh {
    int n_prh; // number of vertices
    vector<vector<pair<int,int>>> adjList_prh; // (neighbor, weight)
};
```

Each undirected edge `(u, v, w)` is stored in **both** adjacency lists:

```cpp
adjList_prh[u].push_back({v, w});
adjList_prh[v].push_back({u, w});
```

---

## 4. Prim’s Algorithm – Concept & Pseudocode

### 4.1 Idea

Prim’s algorithm builds the MST **incrementally**:

1. Start from any vertex (say vertex `0`).  
2. Repeatedly pick the **minimum-weight edge** that connects a vertex already in the MST to a vertex **not yet included**.  
3. Continue until all vertices are included.

We use a **min-heap / priority queue** to always extract the minimum edge efficiently.


### 4.2 Pseudocode (Adjacency List + Min-Heap)

```text
primMST(start):
    Initialize:
        inMST[0..n-1] = false
        minCost = 0

    priority_queue PQ (min-heap) of (weight, u, v)

    inMST[start] = true

    // push all edges from 'start'
    for each (to, wt) in adj[start]:
        PQ.push( (wt, start, to) )

    while PQ is not empty and MST has < n-1 edges:
        (wt, u, v) = PQ.top()
        PQ.pop()

        if inMST[v] is true: continue

        // select this edge in MST
        add edge (u, v, wt) to MST
        minCost += wt
        inMST[v] = true

        // push all edges from v to non-MST vertices
        for each (to, w2) in adj[v]:
            if not inMST[to]:
                PQ.push( (w2, v, to) )

    print MST edges and minCost
```

---

## 5. Time Complexity Analysis

Let:
- `V = number of vertices`
- `E = number of edges`

Using **Adjacency List + Min-Heap**:

- Each edge is relaxed/pushed at most twice.  
- Heap operations: `O(E log E)` ≈ `O(E log V)`  

**Overall Time Complexity:**  
> `O(E log V)`

**Space Complexity:**  
- Adjacency list: `O(V + E)`  
- Extra arrays/priority queue: `O(V + E)`  

---

## 6. Complete C++ Program (Prim’s MST using Adjacency List)

```cpp
// assignment42_prims_adjlist_prasad_hargude.cpp
// Prim's algorithm using adjacency list to find MST
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <queue>
#include <limits>

using namespace std;

class Graph_prh {
private:
    int n_prh; // number of vertices
    vector<vector<pair<int,int>>> adjList_prh; // (neighbor, weight)

public:
    Graph_prh(int n_prh_val = 0) {
        n_prh = n_prh_val;
        if (n_prh > 0) {
            adjList_prh.assign(n_prh, vector<pair<int,int>>());
        }
    }

    void inputGraph_prh() {
        cout << "Enter number of vertices: ";
        cin >> n_prh;
        if (n_prh <= 0) {
            cout << "Invalid number of vertices.\n";
            return;
        }
        adjList_prh.assign(n_prh, vector<pair<int,int>>());

        int edges_prh;
        cout << "Enter number of edges: ";
        cin >> edges_prh;

        cout << "Enter each edge as: u v weight (0-based vertices)\n";
        for (int i = 0; i < edges_prh; ++i) {
            int u_prh, v_prh, w_prh;
            cout << "Edge " << (i + 1) << ": ";
            cin >> u_prh >> v_prh >> w_prh;
            if (u_prh < 0 || u_prh >= n_prh || v_prh < 0 || v_prh >= n_prh || w_prh < 0) {
                cout << "Invalid edge. Skipping.\n";
                continue;
            }
            addEdge_prh(u_prh, v_prh, w_prh);
        }
    }

    void addEdge_prh(int u_prh, int v_prh, int w_prh) {
        adjList_prh[u_prh].push_back({v_prh, w_prh});
        adjList_prh[v_prh].push_back({u_prh, w_prh}); // undirected
    }

    void displayAdjList_prh() const {
        cout << "\nAdjacency List representation:\n";
        for (int i = 0; i < n_prh; ++i) {
            cout << i << " -> ";
            for (auto &edge_prh : adjList_prh[i]) {
                cout << "(" << edge_prh.first << ", w=" << edge_prh.second << ") ";
            }
            cout << "\n";
        }
    }

    // Prim's algorithm to find MST starting from 'start_prh'
    void primMST_prh(int start_prh = 0) const {
        if (n_prh <= 0) {
            cout << "Graph is empty.\n";
            return;
        }
        if (start_prh < 0 || start_prh >= n_prh) {
            cout << "Invalid start vertex.\n";
            return;
        }

        vector<bool> inMST_prh(n_prh, false);

        // min-heap of (weight, from, to)
        using EdgeInfo_prh = pair<int, pair<int,int>>;
        priority_queue<EdgeInfo_prh, vector<EdgeInfo_prh>, greater<EdgeInfo_prh>> pq_prh;

        int mstEdgesCount_prh = 0;
        int totalCost_prh = 0;

        // start from start_prh
        inMST_prh[start_prh] = true;
        for (auto &e_prh : adjList_prh[start_prh]) {
            pq_prh.push({e_prh.second, {start_prh, e_prh.first}});
        }

        cout << "\nPrim's MST edges (from -> to, weight):\n";

        while (!pq_prh.empty() && mstEdgesCount_prh < n_prh - 1) {
            EdgeInfo_prh cur = pq_prh.top();
            pq_prh.pop();

            int wt_prh = cur.first;
            int u_prh = cur.second.first;
            int v_prh = cur.second.second;

            if (inMST_prh[v_prh]) {
                continue; // already included
            }

            // Select this edge
            inMST_prh[v_prh] = true;
            mstEdgesCount_prh++;
            totalCost_prh += wt_prh;

            cout << u_prh << " -> " << v_prh << " (w=" << wt_prh << ")\n";

            // Add all edges from v_prh
            for (auto &e_prh : adjList_prh[v_prh]) {
                if (!inMST_prh[e_prh.first]) {
                    pq_prh.push({e_prh.second, {v_prh, e_prh.first}});
                }
            }
        }

        if (mstEdgesCount_prh != n_prh - 1) {
            cout << "Graph may be disconnected. MST not possible for all vertices.\n";
        }
        cout << "Total minimum cost of MST = " << totalCost_prh << "\n";
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
        cout << "\n--- Prim's MST Menu (Adjacency List) ---\n";
        cout << "1. Input graph\n";
        cout << "2. Display adjacency list\n";
        cout << "3. Run Prim's algorithm (MST)\n";
        cout << "4. Exit\n";
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
            cout << "Enter start vertex for Prim's MST (0-based): ";
            cin >> start_prh;
            graph_prh.primMST_prh(start_prh);
        } else if (choice_prh == 4) {
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

## 7. Compilation & Run Instructions

### Compile

```bash
g++ -std=c++11 assignment42_prims_adjlist_prasad_hargude.cpp -o prims42
```

### Run

```bash
./prims42
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment42_prims_adjlist_prasad_hargude.cpp -o prims42.exe
prims42.exe
```

---

## 8. Sample Input/Output (Example Run)

### Example Graph

Vertices: `4` (0,1,2,3)  
Edges: `5`  

Edges:

- `0 1 10`  
- `0 2 6`  
- `0 3 5`  
- `1 3 15`  
- `2 3 4`  

### Sample Session

```text
--- Prim's MST Menu (Adjacency List) ---
1. Input graph
2. Display adjacency list
3. Run Prim's algorithm (MST)
4. Exit
Enter your choice: 1
Enter number of vertices: 4
Enter number of edges: 5
Enter each edge as: u v weight (0-based vertices)
Edge 1: 0 1 10
Edge 2: 0 2 6
Edge 3: 0 3 5
Edge 4: 1 3 15
Edge 5: 2 3 4

Enter your choice: 2

Adjacency List representation:
0 -> (1, w=10) (2, w=6) (3, w=5)
1 -> (0, w=10) (3, w=15)
2 -> (0, w=6) (3, w=4)
3 -> (0, w=5) (1, w=15) (2, w=4)

Enter your choice: 3
Enter start vertex for Prim's MST (0-based): 0

Prim's MST edges (from -> to, weight):
0 -> 3 (w=5)
3 -> 2 (w=4)
0 -> 1 (w=10)
Total minimum cost of MST = 19
```

---

## 9. Memory Management & Edge Cases

- Uses `vector` for adjacency list; memory is automatically managed (no manual `new/delete`).  
- Handles invalid vertices and negative weights gracefully by skipping invalid edges.  
- If the graph is disconnected, Prim’s algorithm may not be able to include all vertices — code prints a warning.  

---

## 10. Possible Enhancements

- Support **directed** graphs (though MST is mainly for undirected).  
- Add **Kruskal’s Algorithm** for comparison with Prim’s.  
- Add input validation loops and better error messages.  
- Add functionality to print MST as a tree structure.  

---

## 11. References

- Standard Data Structures and Algorithms textbooks (Minimum Spanning Trees)  
- Prim’s Algorithm (Adjacency List + Priority Queue)  
- C++ STL — `vector`, `priority_queue`

---

_End of Assignment 42 — Prim’s MST using Adjacency List_
