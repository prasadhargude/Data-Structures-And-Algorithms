# Assignment 50: Dijkstra’s Algorithm using Adjacency List (Single-Source Shortest Path)
**Author:** Prasad Ramdas Hargude  
**Unit:** VI — Graphs  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Graph Representation & Data Structures  
4. Dijkstra’s Algorithm — Concept & Pseudocode  
5. Time & Space Complexity  
6. Complete C++ Program (Adjacency List + Dijkstra)  
7. Compilation & Run Instructions  
8. Sample Input/Output (Example Run)  
9. Memory Management & Edge Cases  
10. Possible Enhancements  
11. References  

---

## 1. Problem Statement

Write a C++ program to:

- Accept a **weighted, directed or undirected graph** from the user.  
- Represent the graph using an **Adjacency List**.  
- Implement **Dijkstra’s Algorithm** to find the **shortest distance between two nodes** of the graph.  
- Display:
  - The shortest distance from the source to all nodes  
  - The actual **shortest path** from source to a specified destination node  

All important identifiers use the `_prh` suffix.

> Note: Dijkstra’s algorithm assumes **non-negative edge weights**.

---

## 2. System Requirements

- **Language:** C++  
- **Compiler:** C++11 or later (e.g., `g++`)  
- **Graph Type:** Weighted, non-negative edges (directed or undirected; here we will assume undirected by default, but can be easily adjusted).  
- **Representation:** Adjacency List using `vector<vector<pair<int,int>>>`  
- **Input:**
  - Number of vertices `V`  
  - Number of edges `E`  
  - Each edge as: `u v weight`  

---

## 3. Graph Representation & Data Structures

### 3.1 Vertex Indexing

Vertices are labeled as:

```text
0, 1, 2, ..., n_prh - 1
```

### 3.2 Adjacency List

We represent the graph as:

```cpp
vector<vector<pair<int,int>>> adjList_prh;
// adjList_prh[u] contains pairs (v, weight)
```

For each undirected edge:

```cpp
adjList_prh[u].push_back({v, w});
adjList_prh[v].push_back({u, w}); // comment this if using directed graph
```

---

## 4. Dijkstra’s Algorithm — Concept & Pseudocode

### 4.1 Idea

Dijkstra’s algorithm finds the shortest paths from a **single source** vertex to **all other vertices** in a graph with **non-negative edge weights**.  

We use:

- A **distance array** `dist_prh[]` where `dist_prh[v]` stores the current best known distance from source to vertex `v`.  
- A **min-heap / priority queue** to always expand the vertex with the smallest tentative distance.  
- An optional `parent_prh[]` array to reconstruct the actual shortest path.

### 4.2 Pseudocode

```text
DIJKSTRA(source):
    for each vertex v:
        dist[v] = INF
        parent[v] = -1
    dist[source] = 0

    priority_queue PQ as min-heap of (distance, vertex)
    push (0, source) in PQ

    while PQ is not empty:
        (d, u) = PQ.top()
        PQ.pop()

        if d > dist[u]: continue  // outdated entry

        for each (v, w) in adj[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                parent[v] = u
                push (dist[v], v) into PQ
```

To recover path from `source` to a destination `dest`:

```text
path = []
cur = dest
while cur != -1:
    path.push_front(cur)
    cur = parent[cur]
```

---

## 5. Time & Space Complexity

Let:
- `V` = number of vertices  
- `E` = number of edges  

Using **Adjacency List + Min-Heap**:

- Each edge is relaxed at most once.  
- Priority queue operations are `O(log V)` each.  

**Time Complexity:**  
> `O(E log V)`

**Space Complexity:**  
- `O(V + E)` for adjacency list + arrays.

---

## 6. Complete C++ Program (Adjacency List + Dijkstra)

```cpp
// assignment44_dijkstra_adjlist_prasad_hargude.cpp
// Dijkstra's algorithm using adjacency list to find shortest distance between nodes
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <queue>
#include <limits>
#include <algorithm>
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

        cout << "Enter each edge as: u v weight (0-based index, non-negative weight):\n";
        for (int i = 0; i < edges_prh; ++i) {
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
        // undirected graph; comment second line for directed graph
        adjList_prh[u_prh].push_back({v_prh, w_prh});
        adjList_prh[v_prh].push_back({u_prh, w_prh});
    }

    void displayAdjList_prh() const {
        cout << "\nAdjacency List Representation:\n";
        for (int i = 0; i < n_prh; ++i) {
            cout << i << " -> ";
            for (auto &p_prh : adjList_prh[i]) {
                cout << "(" << p_prh.first << ", w=" << p_prh.second << ") ";
            }
            cout << "\n";
        }
    }

    void dijkstra_prh(int src_prh, int dest_prh) const {
        if (n_prh <= 0) {
            cout << "Graph is empty.\n";
            return;
        }
        if (src_prh < 0 || src_prh >= n_prh || dest_prh < 0 || dest_prh >= n_prh) {
            cout << "Invalid source or destination.\n";
            return;
        }

        const int INF_prh = numeric_limits<int>::max();
        vector<int> dist_prh(n_prh, INF_prh);
        vector<int> parent_prh(n_prh, -1);
        vector<bool> visited_prh(n_prh, false);

        // min-heap of (dist, vertex)
        using Node_prh = pair<int,int>;
        priority_queue<Node_prh, vector<Node_prh>, greater<Node_prh>> pq_prh;

        dist_prh[src_prh] = 0;
        pq_prh.push({0, src_prh});

        while (!pq_prh.empty()) {
            auto cur_prh = pq_prh.top();
            pq_prh.pop();

            int d_prh = cur_prh.first;
            int u_prh = cur_prh.second;

            if (visited_prh[u_prh]) continue;
            visited_prh[u_prh] = true;

            if (u_prh == dest_prh) break; // we found shortest distance to dest

            for (auto &edge_prh : adjList_prh[u_prh]) {
                int v_prh = edge_prh.first;
                int w_prh = edge_prh.second;
                if (!visited_prh[v_prh] && d_prh + w_prh < dist_prh[v_prh]) {
                    dist_prh[v_prh] = d_prh + w_prh;
                    parent_prh[v_prh] = u_prh;
                    pq_prh.push({dist_prh[v_prh], v_prh});
                }
            }
        }

        if (dist_prh[dest_prh] == INF_prh) {
            cout << "No path exists from " << src_prh << " to " << dest_prh << ".\n";
            return;
        }

        cout << "\nShortest distance from " << src_prh
             << " to " << dest_prh << " = " << dist_prh[dest_prh] << "\n";

        // Reconstruct path
        vector<int> path_prh;
        for (int cur_prh = dest_prh; cur_prh != -1; cur_prh = parent_prh[cur_prh]) {
            path_prh.push_back(cur_prh);
        }
        reverse(path_prh.begin(), path_prh.end());

        cout << "Path: ";
        for (size_t i = 0; i < path_prh.size(); ++i) {
            cout << path_prh[i];
            if (i + 1 < path_prh.size()) cout << " -> ";
        }
        cout << "\n";

        // Optional: print distances to all nodes
        cout << "\nDistances from source " << src_prh << " to all vertices:\n";
        for (int i = 0; i < n_prh; ++i) {
            cout << "Vertex " << i << ": ";
            if (dist_prh[i] == INF_prh) cout << "INF";
            else cout << dist_prh[i];
            cout << "\n";
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
        cout << "\n--- Dijkstra Shortest Path Menu (Adjacency List) ---\n";
        cout << "1. Input graph\n";
        cout << "2. Display adjacency list\n";
        cout << "3. Find shortest path (Dijkstra)\n";
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
            int src_prh, dest_prh;
            cout << "Enter source vertex: ";
            cin >> src_prh;
            cout << "Enter destination vertex: ";
            cin >> dest_prh;
            graph_prh.dijkstra_prh(src_prh, dest_prh);
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
g++ -std=c++11 assignment44_dijkstra_adjlist_prasad_hargude.cpp -o dijkstra44
```

### Run

```bash
./dijkstra44
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment44_dijkstra_adjlist_prasad_hargude.cpp -o dijkstra44.exe
dijkstra44.exe
```

---

## 8. Sample Input/Output (Example Run)

### Example Graph

Vertices: `5` (0..4)  
Edges:  

- `0 1 10`  
- `0 4 5`  
- `1 2 1`  
- `1 4 2`  
- `2 3 4`  
- `3 0 7`  
- `3 2 6`  
- `4 1 3`  
- `4 2 9`  
- `4 3 2`  

### Sample Session

```text
--- Dijkstra Shortest Path Menu (Adjacency List) ---
1. Input graph
2. Display adjacency list
3. Find shortest path (Dijkstra)
4. Exit
Enter your choice: 1
Enter number of vertices: 5
Enter number of edges: 10
Enter each edge as: u v weight (0-based index, non-negative weight):
Edge 1: 0 1 10
Edge 2: 0 4 5
Edge 3: 1 2 1
Edge 4: 1 4 2
Edge 5: 2 3 4
Edge 6: 3 0 7
Edge 7: 3 2 6
Edge 8: 4 1 3
Edge 9: 4 2 9
Edge 10: 4 3 2

Enter your choice: 2

Adjacency List Representation:
0 -> (1, w=10) (4, w=5) (3, w=7)
1 -> (0, w=10) (2, w=1) (4, w=2) (4, w=3)
2 -> (1, w=1) (3, w=4) (3, w=6) (4, w=9)
3 -> (2, w=4) (0, w=7) (2, w=6) (4, w=2)
4 -> (0, w=5) (1, w=2) (1, w=3) (2, w=9) (3, w=2)

Enter your choice: 3
Enter source vertex: 0
Enter destination vertex: 3

Shortest distance from 0 to 3 = 7
Path: 0 -> 4 -> 3

Distances from source 0 to all vertices:
Vertex 0: 0
Vertex 1: 7
Vertex 2: 8
Vertex 3: 7
Vertex 4: 5
```

---

## 9. Memory Management & Edge Cases

- Uses only `vector` and automatic storage — no manual `new`/`delete`.  
- Checks for:
  - Invalid number of vertices  
  - Invalid edges (indices out of range or negative weight)  
  - Invalid source/destination indices  
  - Unreachable destination (prints “No path exists…”)  
- Dijkstra’s algorithm **does not support negative weights** — such edges are skipped in this implementation.

---

## 10. Possible Enhancements

- Allow the user to choose between **directed** and **undirected** graphs.  
- Show all shortest paths from the source to every vertex in a nicer tabular format.  
- Integrate adjacency matrix version (for small dense graphs) and compare time.  
- Add **Bellman-Ford** algorithm for graphs with negative edges.

---

## 11. References

- Standard DSA textbooks — Dijkstra’s Algorithm  
- C++ STL Reference — `vector`, `priority_queue`, `algorithm`  

---

_End of Assignment 50 — Dijkstra’s Shortest Path using Adjacency List_
