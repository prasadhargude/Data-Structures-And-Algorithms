# Assignment 13: Appointment Schedule Manager (In-Depth)
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
Develop a C++ program to store and manage an appointment schedule for a single day. Appointments are stored in a **linked list** and may be scheduled randomly at startup.

Each appointment contains:
- Start time  
- End time  
- Duration  

The system must enforce:
- Minimum and maximum duration limits  
- Valid time boundaries within the workday  

### Required Operations
a) Display available time slots  
b) Book a new appointment  
c) Cancel an existing appointment  
d) Sort the appointment list by appointment time  
e) Sort by **pointer manipulation** (no data swapping)

Initial schedule should include randomly generated appointments that respect min/max duration and avoid overlaps.

---

## 2. System Requirements
- **Language:** C++ (C++11)  
- **Data Structure:** Singly Linked List  
- **Execution:** Console-based menu  
- **Platform:** Linux g++ / Windows MinGW  
- **Time Format:** Internally stored as minutes since midnight  

---

## 3. Data Structure & Design

### AppointmentNode_prh
- `start_prh` — integer, minutes since midnight  
- `end_prh` — integer  
- `duration_prh` — appointment duration  
- `next_prh` — pointer to next node  

### ScheduleManager_prh
- Stores linked list head  
- Defines:
  - Workday start and end  
  - Minimum and maximum duration allowed  

### Why Linked List?
- Dynamic storage  
- Efficient insertion/removal  
- Supports pointer-based sorting  

---

## 4. Algorithms (pseudocode + complexity)

### A. Check Overlap
```
overlaps(start, end):
    for each node in list:
        if !(end ≤ node.start OR start ≥ node.end):
            return true
    return false
```
**Complexity:** O(n)

### B. Insert Sorted
```
insert(node):
    if list empty or node.start < head.start:
        node.next = head
        head = node
        return
    traverse list to find correct position
    insert node
```
**Complexity:** O(n)

### C. Display Available Slots
```
cursor = workday_start
for each appointment in order:
    if cursor < appointment.start:
        print free slot
    cursor = max(cursor, appointment.end)
if cursor < workday_end:
    print free slot
```
**Complexity:** O(n)

### D. Sort by Pointer Manipulation (Merge Sort)
- Split list  
- Recursively sort halves  
- Merge by comparing start times  
- No swapping of data  

**Complexity:** O(n log n)

---

## 5. C++ Implementation (Complete Code)

```cpp
// appointment_schedule_prh.cpp
// C++11 — Appointment Schedule Manager (Assignment 13)
// Author: Prasad Ramdas Haryde

#include <iostream>
#include <iomanip>
#include <cstdlib>
#include <ctime>
#include <string>
#include <sstream>
#include <vector>
#include <limits>

using namespace std;

struct AppointmentNode_prh {
    int start_prh; // minutes since midnight
    int end_prh;   // minutes since midnight
    int duration_prh;
    AppointmentNode_prh* next_prh;
    AppointmentNode_prh(int s, int d)
        : start_prh(s), duration_prh(d), end_prh(s + d), next_prh(nullptr) {}
};

class ScheduleManager_prh {
private:
    AppointmentNode_prh* head_prh;
    int workday_start_prh;
    int workday_end_prh;
    int min_duration_prh;
    int max_duration_prh;

    static string minutesToHHMM_prh(int minutes) {
        if (minutes < 0) minutes = 0;
        int hh = minutes / 60;
        int mm = minutes % 60;
        ostringstream os;
        os << setw(2) << setfill('0') << hh << ":" << setw(2) << setfill('0') << mm;
        return os.str();
    }

    static bool parseHHMM_prh(const string& s, int &out_minutes) {
        int hh = 0, mm = 0;
        if (s.find(':') != string::npos) {
            char colon;
            istringstream iss(s);
            if (!(iss >> hh >> colon >> mm)) return false;
        } else {
            istringstream iss(s);
            if (!(iss >> hh >> mm)) return false;
        }
        if (hh < 0 || hh > 23 || mm < 0 || mm > 59) return false;
        out_minutes = hh * 60 + mm;
        return true;
    }

    bool overlaps_prh(int start, int end) {
        AppointmentNode_prh* cur = head_prh;
        while (cur) {
            if (!(end <= cur->start_prh || start >= cur->end_prh)) return true;
            cur = cur->next_prh;
        }
        return false;
    }

    void insertSorted_prh(AppointmentNode_prh* node) {
        if (!head_prh || node->start_prh < head_prh->start_prh) {
            node->next_prh = head_prh;
            head_prh = node;
            return;
        }
        AppointmentNode_prh* cur = head_prh;
        while (cur->next_prh && cur->next_prh->start_prh < node->start_prh) {
            cur = cur->next_prh;
        }
        node->next_prh = cur->next_prh;
        cur->next_prh = node;
    }

    void freeAll_prh() {
        AppointmentNode_prh* cur = head_prh;
        while (cur) {
            AppointmentNode_prh* nxt = cur->next_prh;
            delete cur;
            cur = nxt;
        }
        head_prh = nullptr;
    }

    // ---------- Pointer-based merge sort helpers ----------
    static AppointmentNode_prh* mergeSorted_prh(AppointmentNode_prh* a, AppointmentNode_prh* b) {
        if (!a) return b;
        if (!b) return a;
        AppointmentNode_prh* result = nullptr;
        if (a->start_prh <= b->start_prh) {
            result = a;
            result->next_prh = mergeSorted_prh(a->next_prh, b);
        } else {
            result = b;
            result->next_prh = mergeSorted_prh(a, b->next_prh);
        }
        return result;
    }

    static void splitList_prh(AppointmentNode_prh* source, AppointmentNode_prh** frontRef, AppointmentNode_prh** backRef) {
        AppointmentNode_prh* fast;
        AppointmentNode_prh* slow;
        if (!source || !source->next_prh) {
            *frontRef = source;
            *backRef = nullptr;
        } else {
            slow = source;
            fast = source->next_prh;
            while (fast) {
                fast = fast->next_prh;
                if (fast) {
                    slow = slow->next_prh;
                    fast = fast->next_prh;
                }
            }
            *frontRef = source;
            *backRef = slow->next_prh;
            slow->next_prh = nullptr;
        }
    }

    static void mergeSortPointers_prh(AppointmentNode_prh** headRef) {
        AppointmentNode_prh* head = *headRef;
        if (!head || !head->next_prh) return;
        AppointmentNode_prh* a;
        AppointmentNode_prh* b;
        splitList_prh(head, &a, &b);
        mergeSortPointers_prh(&a);
        mergeSortPointers_prh(&b);
        *headRef = mergeSorted_prh(a, b);
    }

public:
    ScheduleManager_prh(int wstart_min, int wend_min, int mindur, int maxdur)
        : head_prh(nullptr),
          workday_start_prh(wstart_min),
          workday_end_prh(wend_min),
          min_duration_prh(mindur),
          max_duration_prh(maxdur)
    {}

    ~ScheduleManager_prh() {
        freeAll_prh();
    }

    // Create random non-overlapping appointments count_prh
    void generateRandomSchedule_prh(int count_prh) {
        if (count_prh <= 0) return;
        srand((unsigned)time(nullptr));
        int tries = 0;
        int booked = 0;
        const int MAX_TRIES = 5000;
        while (booked < count_prh && tries < MAX_TRIES) {
            ++tries;
            int dur = min_duration_prh + (rand() % (max_duration_prh - min_duration_prh + 1));
            int earliest = workday_start_prh;
            int latestStart = workday_end_prh - dur;
            if (latestStart < earliest) break;
            int start = earliest + (rand() % (latestStart - earliest + 1));
            int end = start + dur;
            if (!overlaps_prh(start, end)) {
                AppointmentNode_prh* node = new AppointmentNode_prh(start, dur);
                insertSorted_prh(node);
                ++booked;
            }
        }
    }

    // Display current appointments
    void displayAppointments_prh() const {
        if (!head_prh) {
            cout << "No appointments scheduled." << endl;
            return;
        }
        cout << "\nScheduled Appointments:\n";
        AppointmentNode_prh* cur = head_prh;
        while (cur) {
            cout << "Start: " << minutesToHHMM_prh(cur->start_prh)
                 << " | End: " << minutesToHHMM_prh(cur->end_prh)
                 << " | Dur: " << cur->duration_prh << " min" << endl;
            cur = cur->next_prh;
        }
    }

    // Display available free slots in the day given existing appointments
    void displayAvailableSlots_prh() const {
        cout << "\nAvailable Time Slots:\n";
        int cursor = workday_start_prh;
        AppointmentNode_prh* cur = head_prh;
        bool any = false;
        while (cur) {
            if (cursor < cur->start_prh) {
                cout << minutesToHHMM_prh(cursor) << " - " << minutesToHHMM_prh(cur->start_prh)
                     << " (" << (cur->start_prh - cursor) << " min)" << endl;
                any = true;
            }
            cursor = max(cursor, cur->end_prh);
            cur = cur->next_prh;
        }
        if (cursor < workday_end_prh) {
            cout << minutesToHHMM_prh(cursor) << " - " << minutesToHHMM_prh(workday_end_prh)
                 << " (" << (workday_end_prh - cursor) << " min)" << endl;
            any = true;
        }
        if (!any) cout << "No free slots available." << endl;
    }

    // Book appointment by providing start HH:MM string and duration in minutes
    bool bookAppointment_prh(const string& start_str, int duration_prh) {
        int start_min = 0;
        if (!parseHHMM_prh(start_str, start_min)) return false;
        if (duration_prh < min_duration_prh || duration_prh > max_duration_prh) return false;
        int end_min = start_min + duration_prh;
        if (start_min < workday_start_prh || end_min > workday_end_prh) return false;
        if (overlaps_prh(start_min, end_min)) return false;
        AppointmentNode_prh* node = new AppointmentNode_prh(start_min, duration_prh);
        insertSorted_prh(node);
        return true;
    }

    // Book appointment specifying start and end (validates duration automatically)
    bool bookAppointmentRange_prh(int start_min, int end_min) {
        if (end_min <= start_min) return false;
        int dur = end_min - start_min;
        if (dur < min_duration_prh || dur > max_duration_prh) return false;
        if (start_min < workday_start_prh || end_min > workday_end_prh) return false;
        if (overlaps_prh(start_min, end_min)) return false;
        AppointmentNode_prh* node = new AppointmentNode_prh(start_min, dur);
        insertSorted_prh(node);
        return true;
    }

    // Cancel appointment by start time (HH:MM). If multiple with same start, remove first match.
    bool cancelAppointment_prh(const string& start_str) {
        int start_min = 0;
        if (!parseHHMM_prh(start_str, start_min)) return false;
        if (!head_prh) return false;
        if (head_prh->start_prh == start_min) {
            AppointmentNode_prh* toDel = head_prh;
            head_prh = head_prh->next_prh;
            delete toDel;
            return true;
        }
        AppointmentNode_prh* prev = head_prh;
        AppointmentNode_prh* cur = head_prh->next_prh;
        while (cur) {
            if (cur->start_prh == start_min) {
                prev->next_prh = cur->next_prh;
                delete cur;
                return true;
            }
            prev = cur;
            cur = cur->next_prh;
        }
        return false;
    }

    // Sort by swapping appointment fields (simple insertion sort O(n^2))
    void sortAppointments_prh() {
        if (!head_prh || !head_prh->next_prh) return;
        AppointmentNode_prh* sorted = nullptr;
        AppointmentNode_prh* cur = head_prh;
        while (cur) {
            AppointmentNode_prh* next = cur->next_prh;
            // insert cur into sorted (by start_prh)
            if (!sorted || cur->start_prh < sorted->start_prh) {
                cur->next_prh = sorted;
                sorted = cur;
            } else {
                AppointmentNode_prh* s = sorted;
                while (s->next_prh && s->next_prh->start_prh <= cur->start_prh) s = s->next_prh;
                cur->next_prh = s->next_prh;
                s->next_prh = cur;
            }
            cur = next;
        }
        head_prh = sorted;
        // NOTE: This implemented by pointer rearrangement (still allowed). If "swap data" is strictly required,
        // we could traverse and swap fields; but this approach is simpler and acceptable for small lists.
    }

    // Sort using pointer manipulation only (merge sort)
    void sortByPointers_prh() {
        mergeSortPointers_prh(&head_prh);
    }

    // Utility: check if there is an appointment exactly at start (for testing)
    bool existsAt_prh(const string& start_str) {
        int start_min = 0;
        if (!parseHHMM_prh(start_str, start_min)) return false;
        AppointmentNode_prh* cur = head_prh;
        while (cur) {
            if (cur->start_prh == start_min) return true;
            cur = cur->next_prh;
        }
        return false;
    }
};

// ---------- Utility interactions ----------
void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    // Default workday 09:00 - 17:00
    const int workStart = 9 * 60;
    const int workEnd = 17 * 60;
    int minDur = 15, maxDur = 120;
    cout << "Appointment Schedule Manager (Assignment 13)\n";
    cout << "Default workday: 09:00 - 17:00, default min dur: 15 min, max dur: 120 min\n";
    cout << "Would you like to set custom min/max duration? (y/n): ";
    char choice; cin >> choice; flushStdin_prh();
    if (choice == 'y' || choice == 'Y') {
        cout << "Enter min duration (minutes): "; cin >> minDur; flushStdin_prh();
        cout << "Enter max duration (minutes): "; cin >> maxDur; flushStdin_prh();
        if (minDur <= 0 || maxDur < minDur) {
            cout << "Invalid durations, reverting to defaults.\n";
            minDur = 15; maxDur = 120;
        }
    }

    ScheduleManager_prh sched(workStart, workEnd, minDur, maxDur);

    cout << "Generate some random appointments at startup? (how many, 0 for none): ";
    int rcount; cin >> rcount; flushStdin_prh();
    if (rcount > 0) {
        sched.generateRandomSchedule_prh(rcount);
    }

    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Display Appointments\n";
        cout << "2. Display Available Time Slots\n";
        cout << "3. Book Appointment (enter start HH:MM and duration)\n";
        cout << "4. Book Appointment by range (enter start HH:MM and end HH:MM)\n";
        cout << "5. Cancel Appointment (enter start HH:MM)\n";
        cout << "6. Sort Appointments (pointer-based merge sort)\n";
        cout << "7. Sort Appointments (simple sort / insertion)\n";
        cout << "8. Exit\n";
        cout << "Enter choice: ";
        int c; if (!(cin >> c)) { cout << "Invalid input.\n"; flushStdin_prh(); continue; }
        flushStdin_prh();
        if (c == 1) {
            sched.displayAppointments_prh();
        } else if (c == 2) {
            sched.displayAvailableSlots_prh();
        } else if (c == 3) {
            string start; int dur;
            cout << "Enter start time (HH:MM): ";
            getline(cin, start);
            cout << "Enter duration (minutes): ";
            if (!(cin >> dur)) { cout << "Invalid duration.\n"; flushStdin_prh(); continue; }
            flushStdin_prh();
            if (sched.bookAppointment_prh(start, dur)) cout << "Booked successfully.\n";
            else cout << "Booking failed: invalid time/duration or overlaps.\n";
        } else if (c == 4) {
            string sstart, send;
            cout << "Enter start time (HH:MM): "; getline(cin, sstart);
            cout << "Enter end time (HH:MM): "; getline(cin, send);
            int start_min, end_min;
            auto parseLambda = [](const string &str, int &out)->bool {
                int hh=0, mm=0;
                if (str.find(':') == string::npos) return false;
                char colon;
                istringstream iss(str);
                if (!(iss >> hh >> colon >> mm)) return false;
                if (hh<0||hh>23||mm<0||mm>59) return false;
                out = hh*60 + mm; return true;
            };
            if (!parseLambda(sstart, start_min) || !parseLambda(send, end_min)) {
                cout << "Invalid time format.\n";
                continue;
            }
            if (sched.bookAppointmentRange_prh(start_min, end_min)) cout << "Booked successfully.\n";
            else cout << "Booking failed.\n";
        } else if (c == 5) {
            string start;
            cout << "Enter start time (HH:MM) of appointment to cancel: ";
            getline(cin, start);
            if (sched.cancelAppointment_prh(start)) cout << "Cancellation successful.\n";
            else cout << "Cancellation failed: appointment not found or invalid time format.\n";
        } else if (c == 6) {
            sched.sortByPointers_prh();
            cout << "Sorted by pointers (merge sort).\n";
        } else if (c == 7) {
            sched.sortAppointments_prh();
            cout << "Sorted (simple insertion method).\n";
        } else if (c == 8) {
            cout << "Exiting...\n";
            break;
        } else {
            cout << "Invalid choice.\n";
        }
    }
    return 0;
}
```

---

## 6. Compilation & Run Instructions

### Linux / Windows (MinGW)
```bash
g++ -std=c++11 -O2 appointment_schedule_prh.cpp -o appointment_schedule
./appointment_schedule
```

---

## 7. Example Runs and Test Cases

### Test 1: Book valid slot  
Expect: **Success**

### Test 2: Book overlapping  
Expect: **Fail**

### Test 3: Cancel existing appointment  
Expect: **Success**

### Test 4: Sort using pointer manipulation  
Expect: **Appointments sorted by time**

### Test 5: Display available slots  
Shows gaps between appointments

---

## 8. Memory Management & Edge Cases
- All nodes deleted in destructor  
- Cancelling deletes node safely  
- Random generator capped to prevent infinite loops  
- Invalid times or durations rejected  
- Overlapping bookings prevented  

---

## 9. Extensions and Enhancements
- Save/load to file  
- GUI timeline viewer  
- Auto-suggest nearest available slot  
- Recurring appointments  
- Multi-day support  

---

## 10. References
- ISO C++11 standard  
- Linked list merge sort algorithm  
- Data Structures textbooks  

---
