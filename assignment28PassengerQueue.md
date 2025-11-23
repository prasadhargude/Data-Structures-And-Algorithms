# Assignment 28: Passenger Queue Management System (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References  

---

## 1. Problem Statement
Write a C++ program that maintains a **queue of passengers** waiting to meet a **ticket agent**.

The system must support:
- **Insert** a new passenger at the **rear** of the queue  
- **Display** the passenger at the **front** of the queue  
- **Remove** the passenger at the **front** of the queue (FCFS)  
- When the program exits, it must display the **number of passengers still left** in the queue.

Use a linked-list or array-based queue implementation.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Data structure:** Singly linked list–based queue  
- **Menu-driven program**  
- Queue operations must all be **O(1)**

---

## 3. Data Structure & Design

### Node structure  
Each passenger stores:
- `id_prh` (auto-increment passenger ID)  
- `name_prh` (passenger name)  
- Pointer to next node  

```cpp
struct Passenger_prh {
    int id_prh;
    string name_prh;
    Passenger_prh* next_prh;
};
```

### Queue structure  
- `front_prh` → first passenger  
- `rear_prh` → last passenger  
- `count_prh` → number of passengers  

Supports:
- `enqueue_prh(name)`  
- `dequeue_prh()`  
- `peek_prh()`  
- `isEmpty_prh()`  

---

## 4. Algorithms (pseudocode + complexity)

### Enqueue (insert at rear)
```
create new node with name
if queue empty:
    front = rear = node
else:
    rear.next = node
    rear = node
count++
```
Time: **O(1)**

### Dequeue (remove front)
```
if queue empty: print "No passengers"
temp = front
front = front.next
delete temp
count--
```
Time: **O(1)**

### Peek (show front)
```
if empty: print message
else print front passenger
```
Time: **O(1)**

### Exit
Print value of `count_prh`.

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment28_passenger_queue_prasad_hargude.cpp
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <limits>
using namespace std;

struct Passenger_prh {
    int id_prh;
    string name_prh;
    Passenger_prh* next_prh;
    Passenger_prh(int id, const string &n) : id_prh(id), name_prh(n), next_prh(nullptr) {}
};

class PassengerQueue_prh {
private:
    Passenger_prh* front_prh;
    Passenger_prh* rear_prh;
    int count_prh;

public:
    PassengerQueue_prh() : front_prh(nullptr), rear_prh(nullptr), count_prh(0) {}
    ~PassengerQueue_prh() { clear_prh(); }

    bool isEmpty_prh() const { return front_prh == nullptr; }
    int size_prh() const { return count_prh; }

    void enqueue_prh(const string &name_prh, int id_prh) {
        Passenger_prh* node = new Passenger_prh(id_prh, name_prh);
        if (isEmpty_prh()) {
            front_prh = rear_prh = node;
        } else {
            rear_prh->next_prh = node;
            rear_prh = node;
        }
        count_prh++;
    }

    bool dequeue_prh() {
        if (isEmpty_prh()) return false;
        Passenger_prh* temp = front_prh;
        front_prh = front_prh->next_prh;
        delete temp;
        count_prh--;
        if (!front_prh) rear_prh = nullptr;
        return true;
    }

    Passenger_prh* peek_prh() const {
        return front_prh;
    }

    void clear_prh() {
        while (!isEmpty_prh()) {
            dequeue_prh();
        }
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 28: Passenger Queue Management System\n";

    PassengerQueue_prh q;
    int nextId = 1;

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Insert passenger into queue\n";
        cout << "2. Display passenger at front\n";
        cout << "3. Remove passenger at front\n";
        cout << "4. Exit\n";
        cout << "Enter choice: ";

        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            string name;
            cout << "Enter passenger name: ";
            getline(cin, name);
            if (name.empty()) { cout << "Name required.\n"; continue; }

            q.enqueue_prh(name, nextId++);
            cout << "Passenger added to queue.\n";

        } else if (ch == 2) {
            Passenger_prh* p = q.peek_prh();
            if (!p) cout << "Queue is empty.\n";
            else cout << "Front passenger: #" << p->id_prh << " - " << p->name_prh << "\n";

        } else if (ch == 3) {
            if (q.dequeue_prh())
                cout << "Passenger removed from front.\n";
            else
                cout << "Queue is empty.\n";

        } else if (ch == 4) {
            cout << "Passengers left in queue: " << q.size_prh() << "\n";
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

```bash
g++ -std=c++11 assignment28_passenger_queue_prasad_hargude.cpp -o assignment28_queue
./assignment28_queue
```

---

## 7. Example Runs and Test Cases

### Example 1
```
Insert: John
Insert: Alice
Display front -> John
Remove -> John removed
Display front -> Alice
Exit -> Passengers left = 1
```

### Example 2 — Removing from empty queue
```
Remove -> Queue is empty
```

### Example 3 — Display on empty queue
```
Front passenger -> Queue is empty
```

---

## 8. Memory Management & Edge Cases
- Destructor frees all remaining nodes using `clear_prh()`
- ID is auto-incremented per passenger
- Input validation for names
- Linked-list avoids queue overflow

---

## 9. Extensions and Enhancements
- Add priority queue (elderly, disability priority)
- Add timestamp for arrival time
- Support multiple ticket agents with separate queues
- Save queue to file and restore later

---

## 10. References
- Queue data structures (linked list implementation)
- FCFS service modeling  
