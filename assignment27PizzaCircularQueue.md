# Assignment 27: Pizza Parlour Order Simulation using Circular Queue (In-Depth)
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
Simulate a pizza parlour order management system that accepts at most **n** orders. Orders are served on a **first-come, first-served (FCFS)** basis. Once an order is placed it **cannot be cancelled**.

Requirements:
- Use a **circular queue** to hold up to `n` orders.
- Implement operations:
  - `placeOrder(orderDetails)` — enqueue new order (if space)
  - `serveOrder()` — dequeue and return the next order (FCFS)
  - `displayOrders()` — show queued orders from front to rear
  - `isFull()` / `isEmpty()` checks
- Console-based menu-driven program.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Data structure:** Circular queue implemented using array  
- **Interface:** Console-based; menu-driven  
- **Portability:** Compiles with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

Use a fixed-size array `Order_prh arr_prh[n_prh]` to store orders, with integer indices `front_prh` and `rear_prh` and a `count_prh` to track number of elements.

Order representation:
```cpp
struct Order_prh {
    int id_prh;            // unique order id
    std::string name_prh;  // customer name
    std::string items_prh; // brief order description
};
```

Circular queue state:
- `front_prh` points to the index of the current front element (valid if count_prh > 0)
- `rear_prh` points to index where last element is stored
- `count_prh` stores number of elements currently in queue

We use `count_prh` to differentiate full and empty states:
- Empty: `count_prh == 0`
- Full: `count_prh == capacity_prh`

Enqueue (place order):
1. If `count_prh == capacity_prh` → overflow (no more orders)
2. `rear_prh = (rear_prh + 1) % capacity_prh`
3. `arr_prh[rear_prh] = order`
4. `++count_prh`

Dequeue (serve order):
1. If `count_prh == 0` → underflow (no orders)
2. `order = arr_prh[front_prh]`
3. `front_prh = (front_prh + 1) % capacity_prh`
4. `--count_prh`
5. return `order`

This has O(1) time for enqueue/dequeue.

---

## 4. Algorithms (pseudocode + complexity)

### placeOrder(order)
```
if count == capacity: overflow
rear = (rear + 1) mod capacity
arr[rear] = order
count = count + 1
```
Time: O(1)

### serveOrder()
```
if count == 0: underflow
order = arr[front]
front = (front + 1) mod capacity
count = count - 1
return order
```
Time: O(1)

### displayOrders()
```
i = 0
idx = front
while i < count:
    print arr[idx]
    idx = (idx + 1) mod capacity
    i = i + 1
```
Time: O(count)

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment27_pizza_circular_queue_prasad_hargude.cpp
// C++11 — Pizza Order Simulation using Circular Queue
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <limits>
#include <vector>

using namespace std;

struct Order_prh {
    int id_prh;
    string name_prh;
    string items_prh;
};

class CircularQueue_prh {
private:
    Order_prh* arr_prh;
    int capacity_prh;
    int front_prh;
    int rear_prh;
    int count_prh;
public:
    CircularQueue_prh(int cap)
        : capacity_prh(cap), front_prh(0), rear_prh(-1), count_prh(0) {
        arr_prh = new Order_prh[capacity_prh];
    }
    ~CircularQueue_prh() { delete[] arr_prh; }

    bool isEmpty_prh() const { return count_prh == 0; }
    bool isFull_prh() const { return count_prh == capacity_prh; }
    int size_prh() const { return count_prh; }

    bool enqueue_prh(const Order_prh &order) {
        if (isFull_prh()) return false;
        rear_prh = (rear_prh + 1) % capacity_prh;
        arr_prh[rear_prh] = order;
        ++count_prh;
        return true;
    }

    bool dequeue_prh(Order_prh &outOrder) {
        if (isEmpty_prh()) return false;
        outOrder = arr_prh[front_prh];
        front_prh = (front_prh + 1) % capacity_prh;
        --count_prh;
        return true;
    }

    void display_prh() const {
        if (isEmpty_prh()) { cout << "(no pending orders)\n"; return; }
        cout << "Orders (front -> rear):\n";
        int idx = front_prh;
        for (int i = 0; i < count_prh; ++i) {
            const Order_prh &o = arr_prh[idx];
            cout << "#" << o.id_prh << " | " << o.name_prh << " | " << o.items_prh << "\n";
            idx = (idx + 1) % capacity_prh;
        }
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 27: Pizza Parlour Circular Queue Simulation\n";
    int n;
    cout << "Enter maximum number of orders (capacity n): ";
    while (!(cin >> n) || n <= 0) {
        cout << "Invalid capacity. Enter positive integer: ";
        flushStdin_prh();
    }
    flushStdin_prh();

    CircularQueue_prh q(n);
    int nextOrderId_prh = 1;

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Place Order\n";
        cout << "2. Serve Order (FCFS)\n";
        cout << "3. Display Pending Orders\n";
        cout << "4. Check isFull / isEmpty\n";
        cout << "5. Exit\n";
        cout << "Enter choice: ";
        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            if (q.isFull_prh()) {
                cout << "Cannot accept new order: capacity full.\n";
                continue;
            }
            Order_prh ord;
            ord.id_prh = nextOrderId_prh++;
            cout << "Customer name: "; getline(cin, ord.name_prh);
            if (ord.name_prh.empty()) { cout << "Name required. Aborting.\n"; continue; }
            cout << "Order items (brief): "; getline(cin, ord.items_prh);
            bool ok = q.enqueue_prh(ord);
            if (ok) cout << "Order placed: #" << ord.id_prh << "\n";
            else cout << "Failed to place order (unexpected).\n";
        } else if (ch == 2) {
            Order_prh served;
            if (!q.dequeue_prh(served)) {
                cout << "No orders to serve.\n";
            } else {
                cout << "Serving Order #" << served.id_prh << " — " << served.name_prh << " : " << served.items_prh << "\n";
            }
        } else if (ch == 3) {
            q.display_prh();
        } else if (ch == 4) {
            cout << "isEmpty: " << (q.isEmpty_prh() ? "true" : "false") << " | isFull: " << (q.isFull_prh() ? "true" : "false") << " | size: " << q.size_prh() << "\n";
        } else if (ch == 5) {
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
g++ -std=c++11 -O2 assignment27_pizza_circular_queue_prasad_hargude.cpp -o assignment27_pizza
./assignment27_pizza
```

---

## 7. Example Runs and Test Cases

### Example 1 — Basic enqueue/dequeue
Capacity `n = 3`

```
Place Order: #1 Alice | Margherita
Place Order: #2 Bob   | Pepperoni
Place Order: #3 Carol | Veggie
Place Order: -> rejected (full)
Display -> shows #1, #2, #3
Serve -> serves #1 (Alice)
Place Order -> now accepts #4 (Dave)
Display -> shows #2, #3, #4
```

### Example 2 — Edge cases
- Serve when queue empty -> message "No orders to serve."
- Place orders until exactly capacity -> last accepted; one more is rejected.
- Names must be non-empty.

---

## 8. Memory Management & Edge Cases
- The circular queue uses a fixed-size dynamic array allocated with `new[]` and freed in destructor.
- Inputs validated for capacity and required fields.
- Orders cannot be cancelled once placed (per requirement).
- All operations are O(1) time.

---

## 9. Extensions and Enhancements
- Persist orders to disk for recovery after crash.
- Use priority queue for VIP customers.
- Track estimated wait times based on average service time.
- Multi-threaded server: accept orders and serve concurrently (requires synchronization).
- Web or GUI front-end for real-world simulation.

---

## 10. References
- Queue data structure: circular buffer implementation
- Standard C++ documentation for I/O and dynamic memory
