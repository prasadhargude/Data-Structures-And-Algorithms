# Assignment 43: Kruskal’s Algorithm using Adjacency List (Minimum Spanning Tree)
**Author:** Prasad Ramdas Hargude  
**Unit:** V — Graphs  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Graph Representation & Data Structures  
4. Kruskal’s Algorithm – Concept & Pseudocode  
5. Disjoint Set (Union–Find) Design  
6. Time & Space Complexity  
7. Complete C++ Program (Adjacency List + Kruskal MST)  
8. Compilation & Run Instructions  
9. Sample Input/Output (Example Run)  
10. Memory Management & Edge Cases  
11. Possible Enhancements  
12. References  

---

## 1. Problem Statement

Write a C++ program to:

- Accept a **connected, weighted, undirected graph** from the user.  
- Represent the graph using an **Adjacency List**.  
- Implement **Kruskal’s Algorithm** to find the **Minimum Spanning Tree (MST)** of the given graph.  
- Display:
  - All edges selected in the MST  
  - The total minimum cost of the MST  

All important identifiers use the `_prh` suffix.

---

## 2. System Requirements

- **Language:** C++ (C++11 or later)  
- **Compiler:** `g++` or compatible  
- **Graph Type:** Weighted, undirected, preferably connected  
- **Graph Representation:**  
  - Adjacency List to store graph  
  - Separate edge list extracted from adjacency list for Kruskal  
- **Input Method:**  
  - Number of vertices `V`  
  - Number of edges `E`  
  - For each edge: `u v weight` (0-based vertices)  

---

## 3. Graph Representation & Data Structures

### 3.1 Vertex Labeling

Vertices are labeled as:

```text
0, 1, 2, ..., n_prh - 1
```

### 3.2 Adjacency List

We represent the graph as:

```cpp
vector<vector<pair<int,int>>> adjList_prh;
// adjList_prh[u] contains (v, weight) pairs.
```

Since the graph is **undirected**, when the user adds an edge `(u, v, w)`:

```cpp
adjList_prh[u].push_back({v, w});
adjList_prh[v].push_back({u, w});
```

### 3.3 Edge List for Kruskal

Kruskal’s algorithm operates on a **global list of all edges**:

```cpp
struct Edge_prh {
    int u_prh, v_prh;
    int w_prh;
};
vector<Edge_prh> edges_prh;
```

---

## 4. Kruskal’s Algorithm – Concept & Pseudocode

### 4.1 Idea

Kruskal’s algorithm builds the MST by:

1. Sorting all edges in **non-decreasing** order of their weights.  
2. Considering each edge `(u, v, w)` in this order:  
   - If adding it **does not create a cycle**, include it in the MST.  
   - Otherwise, skip it.  
3. Cycle detection is done efficiently using a **Disjoint Set (Union–Find)** structure.

### 4.2 Pseudocode

```text
KRUSKAL(G):
    MST = empty set
    sort all edges by weight (ascending)
    create Disjoint Set DS for all vertices

    for each edge (u, v, w) in sorted order:
        if DS.find(u) != DS.find(v):
            include edge (u, v, w) in MST
            DS.union(u, v)

    return MST
```

**Stopping condition:**  
Once MST has `V - 1` edges, we can stop.

---

## 5. Disjoint Set (Union–Find) Design

We use **Union–Find with path compression** and **union by rank**.

```cpp
class DisjointSet_prh {
    vector<int> parent_prh;
    vector<int> rank_prh;
public:
    DisjointSet_prh(int n_prh);
    int find_prh(int x_prh);
    void unite_prh(int x_prh, int y_prh);
};
```

- `find_prh(x)`  
  - Returns representative (root) of the set containing `x`.  
  - Uses **path compression** for efficiency.  
- `unite_prh(x, y)`  
  - Merges the sets containing `x` and `y`.  
  - Uses **rank** to keep tree shallow.

---

## 6. Time & Space Complexity

Let:
- `V` = number of vertices  
- `E` = number of edges  

### Time

- Sorting edges: `O(E log E)`  
- Union–Find operations: almost `O(1)` amortized ⇒ `O(E α(V))` where α is inverse Ackermann (nearly constant)  

**Overall Time Complexity:**  
> `O(E log E)` (dominant sorting step)

### Space

- Adjacency list: `O(V + E)`  
- Edge list: `O(E)`  
- Union–Find arrays: `O(V)`  

---

## 7. Complete C++ Program (Adjacency List + Kruskal MST)

```cpp
// assignment43_kruskal_adjlist_prasad_hargude.cpp
// Kruskal's algorithm using adjacency list (edge list) to find MST
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <algorithm>
#include <limits>
using namespace std;

struct Edge_prh {
    int u_prh, v_prh;
    int w_prh;
};

class DisjointSet_prh {
private:
    vector<int> parent_prh;
    vector<int> rank_prh;

public:
    DisjointSet_prh(int n_prh = 0) {
        parent_prh.resize(n_prh);
        rank_prh.assign(n_prh, 0);
        for (int i = 0; i < n_prh; ++i) parent_prh[i] = i;
    }

    int find_prh(int x_prh) {
        if (parent_prh[x_prh] != x_prh)
            parent_prh[x_prh] = find_prh(parent_prh[x_prh]); // path compression
        return parent_prh[x_prh];
    }

    void unite_prh(int x_prh, int y_prh) {
        int rx_prh = find_prh(x_prh);
        int ry_prh = find_prh(y_prh);
        if (rx_prh == ry_prh) return;
        if (rank_prh[rx_prh] < rank_prh[ry_prh]) {
            parent_prh[rx_prh] = ry_prh;
        } else if (rank_prh[rx_prh] > rank_prh[ry_prh]) {
            parent_prh[ry_prh] = rx_prh;
        } else {
            parent_prh[ry_prh] = rx_prh;
            rank_prh[rx_prh]++;
        }
    }
};

class Graph_prh {
private:
    int n_prh; // vertices
    vector<vector<pair<int,int>>> adjList_prh;
    vector<Edge_prh> edges_prh;

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
        edges_prh.clear();

        int e_prh;
        cout << "Enter number of edges: ";
        cin >> e_prh;

        cout << "Enter each edge as: u v weight (0-based vertices)\n";
        for (int i = 0; i < e_prh; ++i) {
            int u_prh, v_prh, w_prh;
            cout << "Edge " << (i + 1) << ": ";
            cin >> u_prh >> v_prh >> w_prh;
            if (u_prh < 0 || u_prh >= n_prh || v_prh < 0 || v_prh >= n_prh || w_prh < 0) {
                cout << "Invalid edge, skipping.\n";
                continue;
            }
            addEdge_prh(u_prh, v_prh, w_prh);
        }
    }

    void addEdge_prh(int u_prh, int v_prh, int w_prh) {
        // adjacency list (for representation only, not strictly required for Kruskal)
        adjList_prh[u_prh].push_back({v_prh, w_prh});
        adjList_prh[v_prh].push_back({u_prh, w_prh});
        // edge list for Kruskal
        Edge_prh e;
        e.u_prh = u_prh;
        e.v_prh = v_prh;
        e.w_prh = w_prh;
        edges_prh.push_back(e);
    }

    void displayAdjList_prh() const {
        cout << "\nAdjacency List: \n";
        for (int i = 0; i < n_prh; ++i) {
            cout << i << " -> ";
            for (auto &p_prh : adjList_prh[i]) {
                cout << "(" << p_prh.first << ", w=" << p_prh.second << ") ";
            }
            cout << "\n";
        }
    }

    void kruskalMST_prh() const {
        if (n_prh <= 0) {
            cout << "Graph is empty.\n";
            return;
        }
        // Copy edges to local list for sorting
        vector<Edge_prh> sortedEdges_prh = edges_prh;

        // Sort edges by weight
        sort(sortedEdges_prh.begin(), sortedEdges_prh.end(),
             [](const Edge_prh &a, const Edge_prh &b) {
                 return a.w_prh < b.w_prh;
             });

        DisjointSet_prh ds_prh(n_prh);

        int totalCost_prh = 0;
        int edgeCount_prh = 0;

        cout << "\nKruskal's MST edges (u -> v, weight):\n";

        for (const auto &e_prh : sortedEdges_prh) {
            int setU_prh = ds_prh.find_prh(e_prh.u_prh);
            int setV_prh = ds_prh.find_prh(e_prh.v_prh);

            if (setU_prh != setV_prh) {
                // Edge does not form a cycle — include it
                cout << e_prh.u_prh << " -> " << e_prh.v_prh
                     << "  (w=" << e_prh.w_prh << ")\n";
                totalCost_prh += e_prh.w_prh;
                edgeCount_prh++;
                ds_prh.unite_prh(setU_prh, setV_prh);

                if (edgeCount_prh == n_prh - 1) break; // MST completed
            }
        }

        if (edgeCount_prh != n_prh - 1) {
            cout << "Warning: Graph may be disconnected; MST spans only a component.\n";
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
        cout << "\n--- Kruskal MST Menu (Adjacency List) ---\n";
        cout << "1. Input graph\n";
        cout << "2. Display adjacency list\n";
        cout << "3. Run Kruskal's MST algorithm\n";
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
            graph_prh.kruskalMST_prh();
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

## 8. Compilation & Run Instructions

### Compile

```bash
g++ -std=c++11 assignment43_kruskal_adjlist_prasad_hargude.cpp -o kruskal43
```

### Run

```bash
./kruskal43
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment43_kruskal_adjlist_prasad_hargude.cpp -o kruskal43.exe
kruskal43.exe
```

---

## 9. Sample Input/Output (Example Run)

### Example Graph

Vertices: `4`  
Edges: `5`  

Edges:

- `0 1 10`  
- `0 2 6`  
- `0 3 5`  
- `1 3 15`  
- `2 3 4`  

### Example Session

```text
--- Kruskal MST Menu (Adjacency List) ---
1. Input graph
2. Display adjacency list
3. Run Kruskal's MST algorithm
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

Adjacency List: 
0 -> (1, w=10) (2, w=6) (3, w=5) 
1 -> (0, w=10) (3, w=15) 
2 -> (0, w=6) (3, w=4) 
3 -> (0, w=5) (1, w=15) (2, w=4) 

Enter your choice: 3

Kruskal's MST edges (u -> v, weight):
2 -> 3  (w=4)
0 -> 3  (w=5)
0 -> 1  (w=10)
Total minimum cost of MST = 19
```

---

## 10. Memory Management & Edge Cases

- Uses only `vector` for dynamic storage — memory automatically freed when program exits.  
- Invalid edges (negative weights or invalid vertices) are skipped.  
- If graph is **disconnected**, Kruskal will form an MST of a connected component; code prints a warning if fewer than `V - 1` edges were selected.  

---

## 11. Possible Enhancements

- Handle disconnected components specifically and build a **Minimum Spanning Forest**.  
- Add support for reading graph from a file.  
- Compare output with **Prim’s Algorithm** (previous assignment).  
- Add a function to visualize MST edges.  

---

## 12. References

- Standard algorithms books: Kruskal’s Algorithm, Disjoint Set  
- C++ STL (sort, vector)  

---

_End of Assignment 43 — Kruskal’s MST using Adjacency List_
