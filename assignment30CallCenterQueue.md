# Assignment 30: Call Center Queue Management System Using Linked List (In-Depth)
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
You are required to implement a **Call Center Queue System** where customer calls are handled on a **First-Come, First-Served (FCFS)** basis.

Behavior:
- Each incoming customer call is **enqueued**.
- Customer service agents **dequeue** calls one by one to assist customers.
- If there are **no calls**, the system displays a waiting message.

The system must be implemented using a **singly linked list** queue.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Data structure:** Linked-list based queue  
- **Operations supported:**  
  - Enqueue (add new call)  
  - Dequeue (agent handles call)  
  - Display next call  
  - Check queue empty  
- **Menu-driven** user interface

---

## 3. Data Structure & Design

### Node Structure
Each customer call contains:
- `id_prh` — Auto-increment call ID  
- `name_prh` — Customer name  
- `issue_prh` — Issue description  
- `next_prh` — Pointer to next node  

```cpp
struct Call_prh {
    int id_prh;
    string name_prh;
    string issue_prh;
    Call_prh* next_prh;
};
```

### Queue Structure
The queue maintains:
- `front_prh` — first call  
- `rear_prh` — last call  
- `count_prh` — number of pending calls  

Operations run in **O(1)** time.

---

## 4. Algorithms (pseudocode + complexity)

### Enqueue (add new call)
```
create new node
if queue empty:
    front = rear = node
else:
    rear.next = node
    rear = node
count++
```
Time: **O(1)**

### Dequeue (agent takes next call)
```
if queue empty:
    print "No calls, waiting..."
else:
    temp = front
    front = front.next
    delete temp
    count--
```
Time: **O(1)**

### Peek (show next call)
```
if empty: print "No calls"
else print front details
```
Time: **O(1)**

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment30_call_center_queue_prasad_hargude.cpp
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <limits>
using namespace std;

struct Call_prh {
    int id_prh;
    string name_prh;
    string issue_prh;
    Call_prh* next_prh;
    Call_prh(int id, const string &n, const string &i)
        : id_prh(id), name_prh(n), issue_prh(i), next_prh(nullptr) {}
};

class CallQueue_prh {
private:
    Call_prh* front_prh;
    Call_prh* rear_prh;
    int count_prh;

public:
    CallQueue_prh() : front_prh(nullptr), rear_prh(nullptr), count_prh(0) {}
    ~CallQueue_prh() { clear_prh(); }

    bool isEmpty_prh() const {
        return front_prh == nullptr;
    }

    void enqueue_prh(const string &name, const string &issue, int id) {
        Call_prh* node = new Call_prh(id, name, issue);
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
        Call_prh* temp = front_prh;
        front_prh = front_prh->next_prh;
        delete temp;
        count_prh--;
        if (!front_prh) rear_prh = nullptr;
        return true;
    }

    Call_prh* peek_prh() const {
        return front_prh;
    }

    int size_prh() const {
        return count_prh;
    }

    void clear_prh() {
        while (!isEmpty_prh()) dequeue_prh();
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 30: Call Center Queue System\n";

    CallQueue_prh queue;
    int nextId = 1;

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Receive new customer call\n";
        cout << "2. Agent handles next call\n";
        cout << "3. Show next call in queue\n";
        cout << "4. Exit\n";
        cout << "Enter choice: ";

        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            string name, issue;
            cout << "Customer name: "; getline(cin, name);
            cout << "Issue description: "; getline(cin, issue);

            if (name.empty()) { cout << "Name required.\n"; continue; }

            queue.enqueue_prh(name, issue, nextId++);
            cout << "Call received and added to queue.\n";

        } else if (ch == 2) {
            if (!queue.dequeue_prh())
                cout << "No calls! Waiting for customers...\n";
            else
                cout << "Agent handled next customer call.\n";

        } else if (ch == 3) {
            Call_prh* c = queue.peek_prh();
            if (!c)
                cout << "No pending calls.\n";
            else
                cout << "Next call -> #" << c->id_prh << " | " 
                     << c->name_prh << " | Issue: " << c->issue_prh << "\n";

        } else if (ch == 4) {
            cout << "Total calls left in queue: " << queue.size_prh() << "\n";
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
g++ -std=c++11 assignment30_call_center_queue_prasad_hargude.cpp -o assignment30_queue
./assignment30_queue
```

---

## 7. Example Runs and Test Cases

### Example 1
```
Receive call: John
Receive call: Alice
Show next -> John
Handle -> John removed
Show next -> Alice
Exit -> Calls left: 1
```

### Example 2 — No calls situation
```
Agent handles next -> No calls! Waiting for customers...
```

### Example 3 — View when empty
```
Show next -> No pending calls.
```

---

## 8. Memory Management & Edge Cases
- Linked list nodes deleted when served or when program ends  
- Names required for valid call  
- Queue handles unlimited calls (no overflow)  
- Graceful fallback when empty  

---

## 9. Extensions and Enhancements
- Add **priority handling** (VIP customers)  
- Add multiple agents with separate queues  
- Track timestamps for wait-time reporting  
- Generate reports for daily call volume  

---

## 10. References
- FCFS queue model  
- Linked list queue implementation  
- Real-world call center workflow  

---

