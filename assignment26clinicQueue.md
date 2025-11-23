# Assignment 26: Clinic Patient Assignment System (Queue + Linked List) (In-Depth)
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
Write a program to keep track of patients as they check into a medical clinic, assigning patients to doctors on a **first-come, first-served** basis.

Requirements:
- Patients check in and are placed into a waiting queue (arrival order).
- There are multiple doctors. Patients are assigned from the waiting queue to doctors when requested.
- Each doctor has their own queue (patients assigned to that doctor will be served FIFO).
- The system supports operations to check in patients, assign the next waiting patient to a doctor, mark a patient as served (doctor completes with patient), and display queues.

This program uses linked-list based queues to manage patients.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)
- **Data structures:** Singly linked-list queues for waiting line and per-doctor queues
- **Interface:** Console-based menu
- **Portability:** Should compile with `g++` on Linux and MinGW on Windows
- **Notes:** All identifiers end in `_prh` per user preference

---

## 3. Data Structure & Design

### Patient node
Each patient has:
- `id_prh` — integer (auto-increment)
- `name_prh` — string
- `complaint_prh` — string (optional)
- `next_prh` — pointer to next patient in queue

```cpp
struct Patient_prh {
    int id_prh;
    string name_prh;
    string complaint_prh;
    Patient_prh* next_prh;
    Patient_prh(int id, const string &name, const string &comp)
        : id_prh(id), name_prh(name), complaint_prh(comp), next_prh(nullptr) {}
};
```

### Queue (linked-list) abstraction
A simple linked-list queue with `front_prh` and `rear_prh` pointers and `size_prh`. Supports:
- `enqueue_prh(Patient_prh*)`
- `Patient_prh* dequeue_prh()`
- `peek_prh()`
- `isEmpty_prh()`
- `display_prh()`

### Doctor
Each doctor has:
- `id_prh`, `name_prh`
- `Queue_prh assigned_prh` — queue of patients assigned to that doctor

### Overall system
- `Queue_prh waiting_prh` — global waiting queue (FCFS)
- `vector<Doctor_prh>` doctors_prh — list of doctors
- `nextPatientId_prh` — auto-increment patient id

Assignment policy chosen:
- Patients arrive and join the global waiting queue in arrival order.
- When operator chooses "Assign next patient", the system takes the **front** patient from the waiting queue (FCFS) and assigns them to the doctor with the **smallest assigned queue size** (ties broken by doctor id). That preserves first-come service from the waiting line while keeping doctor loads balanced.

Alternate policies (round-robin, specialized doctors) can be added later.

---

## 4. Algorithms (pseudocode + complexity)

### Enqueue (check-in)
```
checkIn(name, complaint):
    create Patient node
    waiting.enqueue(node)
    Time: O(1)
```

### Assign next patient
```
assignNext():
    if waiting.isEmpty(): report no waiting patients
    p = waiting.dequeue()
    choose doctor with minimum queue size (scan doctors O(d))
    doctor.assigned.enqueue(p)
    Time: O(d) where d = number of doctors (plus O(1) queue ops)
```

### Serve next (doctor completes one patient)
```
serve(doctorId):
    if doctor.assigned.isEmpty(): report none
    p = doctor.assigned.dequeue()
    mark served (print details)
    Time: O(1)
```

### Display
- Waiting queue: traverse and print O(n_wait)
- Each doctor's queue: traverse O(n_doctor)

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment26_clinic_queue_prasad_hargude.cpp
// C++11 — Clinic patient check-in and assignment using queues (linked lists)
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <vector>
#include <limits>

using namespace std;

struct Patient_prh {
    int id_prh;
    string name_prh;
    string complaint_prh;
    Patient_prh* next_prh;
    Patient_prh(int id, const string &name, const string &comp)
        : id_prh(id), name_prh(name), complaint_prh(comp), next_prh(nullptr) {}
};

class Queue_prh {
public:
    Patient_prh* front_prh;
    Patient_prh* rear_prh;
    int size_prh;
    Queue_prh(): front_prh(nullptr), rear_prh(nullptr), size_prh(0) {}
    ~Queue_prh() { clear_prh(); }

    void enqueue_prh(Patient_prh* node) {
        if (!node) return;
        node->next_prh = nullptr;
        if (!rear_prh) {
            front_prh = rear_prh = node;
        } else {
            rear_prh->next_prh = node;
            rear_prh = node;
        }
        ++size_prh;
    }

    Patient_prh* dequeue_prh() {
        if (!front_prh) return nullptr;
        Patient_prh* node = front_prh;
        front_prh = front_prh->next_prh;
        if (!front_prh) rear_prh = nullptr;
        node->next_prh = nullptr;
        --size_prh;
        return node;
    }

    Patient_prh* peek_prh() const { return front_prh; }
    bool isEmpty_prh() const { return front_prh == nullptr; }

    void display_prh() const {
        Patient_prh* cur = front_prh;
        if (!cur) { cout << "(empty)"; return; }
        bool first = true;
        while (cur) {
            if (!first) cout << " -> ";
            cout << "#" << cur->id_prh << ":" << cur->name_prh;
            first = false;
            cur = cur->next_prh;
        }
    }

    void clear_prh() {
        while (front_prh) {
            Patient_prh* tmp = front_prh;
            front_prh = front_prh->next_prh;
            delete tmp;
        }
        rear_prh = nullptr;
        size_prh = 0;
    }
};

struct Doctor_prh {
    int id_prh;
    string name_prh;
    Queue_prh assigned_prh;
    Doctor_prh(int id = 0, const string &name = "") : id_prh(id), name_prh(name) {}
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 26: Clinic Patient Assignment System\n";
    int d;
    cout << "Enter number of doctors: ";
    while (!(cin >> d) || d <= 0) {
        cout << "Invalid number, enter positive integer: ";
        flushStdin_prh();
    }
    flushStdin_prh();

    vector<Doctor_prh> doctors_prh;
    doctors_prh.reserve(d);
    for (int i = 0; i < d; ++i) {
        string docname;
        cout << "Name of doctor " << i << ": ";
        getline(cin, docname);
        if (docname.empty()) docname = "Dr_" + to_string(i);
        doctors_prh.emplace_back(i, docname);
    }

    Queue_prh waiting_prh;
    int nextPatientId_prh = 1;

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Check-in patient\n";
        cout << "2. Assign next waiting patient to a doctor\n";
        cout << "3. Doctor serves next patient (complete)\n";
        cout << "4. Display waiting queue\n";
        cout << "5. Display doctors' queues\n";
        cout << "6. Exit\n";
        cout << "Enter choice: ";
        int ch; if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            string pname, complaint;
            cout << "Patient name: "; getline(cin, pname);
            if (pname.empty()) { cout << "Name required. Aborting check-in.\n"; continue; }
            cout << "Complaint (optional): "; getline(cin, complaint);
            Patient_prh* p = new Patient_prh(nextPatientId_prh++, pname, complaint);
            waiting_prh.enqueue_prh(p);
            cout << "Checked in: #" << p->id_prh << ": " << p->name_prh << "\n";
        } else if (ch == 2) {
            if (waiting_prh.isEmpty_prh()) {
                cout << "No patients waiting.\n"; continue;
            }
            // find doctor with smallest queue size (tie-breaker: smallest id)
            int best = 0;
            int bestSize = doctors_prh[0].assigned_prh.size_prh;
            for (size_t i = 1; i < doctors_prh.size(); ++i) {
                if (doctors_prh[i].assigned_prh.size_prh < bestSize) {
                    best = (int)i;
                    bestSize = doctors_prh[i].assigned_prh.size_prh;
                }
            }
            Patient_prh* nxt = waiting_prh.dequeue_prh();
            doctors_prh[best].assigned_prh.enqueue_prh(nxt);
            cout << "Assigned patient #" << nxt->id_prh << " to " << doctors_prh[best].name_prh << " (Doctor ID: " << doctors_prh[best].id_prh << ")\n";
        } else if (ch == 3) {
            int did;
            cout << "Enter doctor id (0.." << doctors_prh.size()-1 << "): "; 
            if (!(cin >> did)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
            flushStdin_prh();
            if (did < 0 || did >= (int)doctors_prh.size()) { cout << "Invalid doctor id\n"; continue; }
            if (doctors_prh[did].assigned_prh.isEmpty_prh()) {
                cout << doctors_prh[did].name_prh << " has no assigned patients.\n"; continue;
            }
            Patient_prh* served = doctors_prh[did].assigned_prh.dequeue_prh();
            cout << "Doctor " << doctors_prh[did].name_prh << " served patient #" << served->id_prh << ": " << served->name_prh << "\n";
            delete served; // assume served patient leaves system
        } else if (ch == 4) {
            cout << "Waiting queue: "; waiting_prh.display_prh(); cout << "\n";
        } else if (ch == 5) {
            for (const auto &doc : doctors_prh) {
                cout << "Doctor " << doc.id_prh << " (" << doc.name_prh << ") : ";
                doc.assigned_prh.display_prh();
                cout << "  [size=" << doc.assigned_prh.size_prh << "]\n";
            }
        } else if (ch == 6) {
            cout << "Exiting... cleaning up.\n";
            break;
        } else {
            cout << "Invalid choice\n";
        }
    }

    // Cleanup: queues' destructors will delete remaining patients
    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile with g++ (C++11):

```bash
g++ -std=c++11 -O2 assignment26_clinic_queue_prasad_hargude.cpp -o assignment26_clinic_queue
./assignment26_clinic_queue
```

---

## 7. Example Runs and Test Cases

### Example workflow
1. Start program and enter number of doctors = 2
2. Doctors: Dr_A, Dr_B
3. Check-in Alice, Bob, Carol, Dave (in that order)
4. Assign next patient -> Alice assigned to doctor with smallest queue (Dr_A)
5. Assign next -> Bob -> Dr_B
6. Assign next -> Carol -> now Dr_A has 1, Dr_B has 1 -> Carol goes to Dr_A (tie-break by lower id)
7. Doctor 0 serves next -> serves Alice
8. Display queues shows remaining patients in each doctor's queue

Expected console snippets in this flow are included inline while running the program.

---

## 8. Memory Management & Edge Cases
- All dynamically created `Patient_prh` nodes are deleted when served or when queue is cleared in destructor.
- Input validation for doctor id and menu choices included.
- If program exits with patients still waiting or assigned, `Queue_prh` destructor will delete remaining nodes.
- The "assign next" operation is O(d) to scan doctors; this is fine for small numbers of doctors.

---

## 9. Extensions and Enhancements
- Auto-dispatch: when a doctor becomes free, auto-assign from waiting queue.
- Priority patients: support emergency/priority levels (use priority queue).
- Appointment scheduling: mix walk-ins and scheduled patients.
- GUI or web front-end for clinic staff.
- Persist data to file or database.

---

## 10. References
- Classic queue data structure for FCFS scheduling
- C++ documentation for dynamic memory and I/O

---
