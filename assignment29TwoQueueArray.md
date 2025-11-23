# Assignment 29: Two Queues Using a Single Array (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases (with expected outputs)  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References  

---

## 1. Problem Statement
Write a C++ program to implement **multiple queues** — specifically **two queues** — using a single array. The program must support the following operations on each queue:

A. Add to queue (enqueue)  
B. Delete from queue (dequeue)  
C. Display queue contents

Constraints & behavior:
- Use an array as the underlying storage.
- Implement two logical queues that share the same array space.
- You may choose a simple fixed-division strategy (each queue gets a partition) or a flexible approach. This implementation uses **fixed partitioning** for clarity: the user provides the total capacity `n` and split point `k`, where queue0 uses indices `[0..k-1]` and queue1 uses indices `[k..n-1]`.
- Both queues are circular within their respective partitions (wrap-around).

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)  
- **Data structure:** Array-based circular queues (two partitions)  
- **Interface:** Console-based, menu-driven program supporting operations for both queues  
- **Portability:** Compiles with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

### Partitioned array approach
- Array `arr_prh[n_prh]` holds values for both queues.
- Split index `split_prh` determines partition sizes:
  - Queue 0 capacity: `cap0_prh = split_prh`
  - Queue 1 capacity: `cap1_prh = n_prh - split_prh`
- For each queue `q` maintain:
  - `front_prh[q]` — index of front element (within partition, -1 if empty)
  - `rear_prh[q]` — index of rear element (within partition, -1 if empty)
  - `size_prh[q]` — current number of elements
- Map logical index within partition to physical array index:
  - For queue 0: physical = `base0 + idx`, where `base0 = 0`
  - For queue 1: physical = `base1 + idx`, where `base1 = split_prh`

### Circular behavior per partition
- Increment index within partition using modulo its capacity:
  - `next = (cur + 1) % cap_q`
- Store values in physical location computed above.

Design rationale:
- Fixed partitioning is simple and predictable.
- Circular indices allow O(1) enqueue/dequeue operations.
- The program checks for overflow if the partition is full and underflow if empty.

---

## 4. Algorithms (pseudocode + complexity)

### Enqueue(queueId, value)
```
if size_prh[queueId] == cap_prh[queueId]: overflow
if size_prh[queueId] == 0:
    front_prh[queueId] = rear_prh[queueId] = 0
else:
    rear_prh[queueId] = (rear_prh[queueId] + 1) % cap_prh[queueId]
store value at arr_prh[ base_prh[queueId] + rear_prh[queueId] ]
size_prh[queueId]++
```
Time: O(1)

### Dequeue(queueId)
```
if size_prh[queueId] == 0: underflow
value = arr_prh[ base_prh[queueId] + front_prh[queueId] ]
if size_prh[queueId] == 1:
    front_prh[queueId] = rear_prh[queueId] = -1
else:
    front_prh[queueId] = (front_prh[queueId] + 1) % cap_prh[queueId]
size_prh[queueId]--
return value
```
Time: O(1)

### Display(queueId)
```
if size_prh[queueId] == 0: print empty
idx = front_prh[queueId]
for i in 0..size_prh[queueId]-1:
    print arr_prh[ base + idx ]
    idx = (idx + 1) % cap_prh[queueId]
```
Time: O(size)

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment29_two_queues_array_prasad_hargude.cpp
// C++11 — Two queues using one array (fixed partition circular queues)
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <limits>
#include <string>

using namespace std;

class TwoQueuesArray_prh {
private:
    int n_prh;                // total array size
    int split_prh;            // split index (first partition size)
    vector<int> arr_prh;      // underlying array

    int cap_prh[2];           // capacities for queues 0 and 1
    int base_prh[2];          // base physical index for each partition
    int front_prh[2];         // front index within partition (-1 if empty)
    int rear_prh[2];          // rear index within partition (-1 if empty)
    int size_prh[2];          // current sizes

public:
    TwoQueuesArray_prh(int n, int split)
        : n_prh(n), split_prh(split), arr_prh(n_prh, 0) {
        if (split_prh < 1 || split_prh >= n_prh) {
            throw invalid_argument("split must be in range [1, n-1]");
        }
        cap_prh[0] = split_prh;
        cap_prh[1] = n_prh - split_prh;
        base_prh[0] = 0;
        base_prh[1] = split_prh;
        front_prh[0] = rear_prh[0] = -1;
        front_prh[1] = rear_prh[1] = -1;
        size_prh[0] = size_prh[1] = 0;
    }

    bool isFull_prh(int qid) const { return size_prh[qid] == cap_prh[qid]; }
    bool isEmpty_prh(int qid) const { return size_prh[qid] == 0; }

    // enqueue value into queue qid (0 or 1)
    void enqueue_prh(int qid, int value) {
        validate_qid_prh(qid);
        if (isFull_prh(qid)) {
            throw overflow_error("Queue overflow: partition is full");
        }
        if (size_prh[qid] == 0) {
            front_prh[qid] = rear_prh[qid] = 0;
        } else {
            rear_prh[qid] = (rear_prh[qid] + 1) % cap_prh[qid];
        }
        int phys = base_prh[qid] + rear_prh[qid];
        arr_prh[phys] = value;
        ++size_prh[qid];
    }

    // dequeue from queue qid and return value
    int dequeue_prh(int qid) {
        validate_qid_prh(qid);
        if (isEmpty_prh(qid)) {
            throw underflow_error("Queue underflow: partition is empty");
        }
        int phys = base_prh[qid] + front_prh[qid];
        int val = arr_prh[phys];
        if (size_prh[qid] == 1) {
            front_prh[qid] = rear_prh[qid] = -1;
        } else {
            front_prh[qid] = (front_prh[qid] + 1) % cap_prh[qid];
        }
        --size_prh[qid];
        return val;
    }

    // display contents of queue qid
    void display_prh(int qid) const {
        validate_qid_prh(qid);
        if (isEmpty_prh(qid)) {
            cout << "(empty)\n";
            return;
        }
        cout << "Queue " << qid << " (front->rear): ";
        int idx = front_prh[qid];
        for (int i = 0; i < size_prh[qid]; ++i) {
            int phys = base_prh[qid] + idx;
            cout << arr_prh[phys];
            if (i + 1 < size_prh[qid]) cout << " -> ";
            idx = (idx + 1) % cap_prh[qid];
        }
        cout << "\n";
    }

    void debug_prh() const {
        cout << "Internal array: ";
        for (int i = 0; i < n_prh; ++i) cout << arr_prh[i] << " ";
        cout << "\n";
        cout << "cap: " << cap_prh[0] << ", " << cap_prh[1] << " | sizes: " << size_prh[0] << ", " << size_prh[1] << "\n";
    }

private:
    void validate_qid_prh(int qid) const {
        if (qid != 0 && qid != 1) throw out_of_range("queue id must be 0 or 1");
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 29: Two Queues using Single Array (Fixed Partitions)\n";
    int n, split;
    cout << "Enter total array size n: "; 
    while (!(cin >> n) || n <= 1) { cout << "Enter integer n > 1: "; flushStdin_prh(); }
    cout << "Enter split index k (queue0 size = k, queue1 size = n-k): ";
    while (!(cin >> split) || split < 1 || split >= n) { cout << "Enter k in [1, n-1]: "; flushStdin_prh(); }
    flushStdin_prh();

    TwoQueuesArray_prh tq(n, split);

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Enqueue (choose queue 0 or 1)\n";
        cout << "2. Dequeue (choose queue 0 or 1)\n";
        cout << "3. Display queue (choose 0 or 1)\n";
        cout << "4. Debug internal state\n";
        cout << "5. Exit\n";
        cout << "Enter choice: ";
        int ch; if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }

        if (ch == 1) {
            int qid, val;
            cout << "Queue id (0 or 1): "; cin >> qid;
            cout << "Value to enqueue (int): "; cin >> val;
            try {
                tq.enqueue_prh(qid, val);
                cout << "Enqueued " << val << " to queue " << qid << "\n";
            } catch (const exception &e) {
                cout << "Error: " << e.what() << "\n";
            }
        } else if (ch == 2) {
            int qid;
            cout << "Queue id (0 or 1): "; cin >> qid;
            try {
                int v = tq.dequeue_prh(qid);
                cout << "Dequeued " << v << " from queue " << qid << "\n";
            } catch (const exception &e) {
                cout << "Error: " << e.what() << "\n";
            }
        } else if (ch == 3) {
            int qid; cout << "Queue id (0 or 1): "; cin >> qid;
            try {
                tq.display_prh(qid);
            } catch (const exception &e) {
                cout << "Error: " << e.what() << "\n";
            }
        } else if (ch == 4) {
            tq.debug_prh();
        } else if (ch == 5) {
            cout << "Exiting...\n"; break;
        } else {
            cout << "Invalid choice\n";
        }
        flushStdin_prh();
    }

    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile with g++ (C++11):

```bash
g++ -std=c++11 -O2 assignment29_two_queues_array_prasad_hargude.cpp -o assignment29_two_queues
./assignment29_two_queues
```

---

## 7. Example Runs and Test Cases (with expected outputs)

### Setup
```
n = 6, split k = 3
=> queue0 capacity = 3 (indices 0..2)
=> queue1 capacity = 3 (indices 3..5)
```

Sequence:
```
enqueue(0, 10)
enqueue(0, 20)
enqueue(1, 100)
enqueue(1, 200)
display(0) -> 10 -> 20
display(1) -> 100 -> 200
dequeue(0) -> returns 10
enqueue(0, 30)  // wraps within partition
```

Overflow/Underflow:
- enqueue into a full partition throws overflow_error
- dequeue from empty partition throws underflow_error

---

## 8. Memory Management & Edge Cases
- Uses `std::vector<int>` for storage; no manual delete required.
- Fixed partitioning can lead to unused space if one queue is empty while other is full.
- Input validation for `n` and `split` ensures meaningful partitions.
- All operations run in O(1).

---

## 9. Extensions and Enhancements
- Implement flexible/dynamic sharing of array space using next/free arrays (more complex but efficient).
- Support more than two queues with variable partitions.
- Allow automatic resizing when total array is insufficient.
- Provide persistence or GUI.

---

## 10. References
- Circular queue data structure (array implementation)
- Fixed-partition multiple-queue approaches
- Standard C++ documentation
