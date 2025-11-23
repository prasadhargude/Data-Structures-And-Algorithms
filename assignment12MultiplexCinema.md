
# 🎟️Assignemnt 12: Galaxy Multiplex Ticket Reservation System (In-Depth)

## Author
**Prasad Ramdas Haryde**

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
Implement a ticket reservation system for **Galaxy Multiplex** using **C++**. The multiplex has **8 rows** and **8 seats per row** (total 64 seats). Each row must be represented using a **doubly circular linked list (DCLL)**. An array stores head pointers for each row's linked list. The system supports:

- Display current availability.
- Book one or multiple seats.
- Cancel an existing booking.

Initial condition: assume some seats are randomly booked at startup.

---

## 2. System Requirements
- Language: C++ (C++11 compatible or later)
- Data structures: Doubly Circular Linked List for each row
- User interactions: Console-based menu
- Portability: Should compile using `g++` on Linux / MinGW on Windows

---

## 3. Data Structure & Design

### Seat Node
Each node represents one seat and contains:
- `row_prh` — row number (1..8)
- `col_prh` — column/seat number (1..8)
- `booked_prh` — boolean indicating booking status
- `next_prh`, `prev_prh` — pointers to neighbor nodes (circular)

Properties of DCLL used here:
- Circular: tail->next points to head; head->prev points to tail
- Doubly linked: constant-time access to next and prev nodes (useful for deletions if needed)
- We store a head pointer for each row in an array `rows_prh[8]`.

### Why DCLL for rows?
- Efficient traversal in both directions.
- Natural mapping to a circular row of seats.
- Demonstrates linked-list operations required in the assignment.

---

## 4. Algorithms (pseudocode + complexity)

> **Notation:** `rows` is the array of head pointers. Each `row` has exactly 8 nodes.

### A. Initialize Rows (createRow)
```
createRow(row):
    head -> NULL
    last -> NULL
    for col from 1 to 8:
        newSeat -> new Seat(row, col, booked=false)
        if head == NULL:
            head -> last -> newSeat
            head.next -> head
            head.prev -> head
        else:
            newSeat.prev -> last
            newSeat.next -> head
            last.next -> newSeat
            head.prev -> newSeat
            last -> newSeat
    rows[row-1] -> head
```
- Time complexity: O(8) = O(1) per row; overall O(1) for fixed-size.
- Space complexity: O(8) nodes per row.


### B. Display Seats (displaySeats)
```
displaySeats():
    for row from 1 to 8:
        head -> rows[row-1]
        if head == NULL: continue
        temp -> head
        do:
            print seat label (booked ? [X] : [O])
            temp -> temp.next
        while temp != head
```
- Time complexity: O(64) = O(1) fixed.


### C. Get Seat (helper getSeat)
```
getSeat(row, col):
    head -> rows[row-1]
    temp -> head
    for i from 1 to col-1:
        temp -> temp.next
    return temp
```
- Time complexity: O(8) worst-case.


### D. Book Seat(s)
```
bookSeats(list_of_pairs):
    for each (row,col) in list_of_pairs:
        seat -> getSeat(row,col)
        if seat.booked:
            print "Seat already booked" (or return partial failure behavior)
        else:
            seat.booked -> true
            print success
```
- Time complexity: O(k * 8) for k seats requested.

> Note: For atomic multi-seat booking you can first verify all requested seats are free then mark them booked — this is implemented in the code as an optional mode.


### E. Cancel Seat
```
cancelSeat(row,col):
    seat -> getSeat(row,col)
    if seat.booked == false:
        print "Seat not booked"
    else:
        seat.booked -> false
        print "Cancelled"
```
- Time complexity: O(8)


---

## 5. C++ Implementation (complete, production-ready)

> The code below includes:
> - Clean class structure
> - Proper initialization and random pre-booking
> - Destructor to free allocated nodes
> - Input validation
> - Optional atomic booking for multiple seats

**Note:** For readability the variable names are suffixed with `_prh` as requested.

`galaxy_multiplex_reservation_in_depth.cpp` (full source):

```cpp
#include <iostream>
#include <vector>
#include <ctime>
#include <cstdlib>
#include <string>

using namespace std;

class Seat_prh {
public:
    int row_prh, col_prh;
    bool booked_prh;
    Seat_prh* next_prh;
    Seat_prh* prev_prh;

    Seat_prh(int r, int c)
        : row_prh(r), col_prh(c), booked_prh(false), next_prh(nullptr), prev_prh(nullptr) {}
};

class Multiplex_prh {
private:
    Seat_prh* rows_prh[8]; // heads for 8 rows

public:
    Multiplex_prh() {
        for (int i = 0; i < 8; ++i) rows_prh[i] = nullptr;
        for (int i = 0; i < 8; ++i) createRow_prh(i + 1);
        randomPrebook_prh(6); // prebook 6 random seats by default
    }

    ~Multiplex_prh() {
        // Free each row's nodes
        for (int i = 0; i < 8; ++i) {
            Seat_prh* head = rows_prh[i];
            if (!head) continue;
            Seat_prh* cur = head->next_prh;
            while (cur != head) {
                Seat_prh* nxt = cur->next_prh;
                delete cur;
                cur = nxt;
            }
            delete head; // delete head last
            rows_prh[i] = nullptr;
        }
    }

    void createRow_prh(int row) {
        Seat_prh* head = nullptr;
        Seat_prh* last = nullptr;
        for (int c = 1; c <= 8; ++c) {
            Seat_prh* newSeat = new Seat_prh(row, c);
            if (!head) {
                head = last = newSeat;
                head->next_prh = head->prev_prh = head;
            } else {
                newSeat->prev_prh = last;
                newSeat->next_prh = head;
                last->next_prh = newSeat;
                head->prev_prh = newSeat;
                last = newSeat;
            }
        }
        rows_prh[row - 1] = head;
    }

    void displaySeats_prh() {
        cout << "\n--- Current Seat Availability ---\n";
        for (int r = 0; r < 8; ++r) {
            Seat_prh* head = rows_prh[r];
            if (!head) continue;
            Seat_prh* temp = head;
            cout << "Row " << (r + 1) << ": ";
            do {
                cout << (temp->booked_prh ? "[X]" : "[O]") << " ";
                temp = temp->next_prh;
            } while (temp != head);
            cout << "\n";
        }
    }

    // Returns nullptr if invalid
    Seat_prh* getSeat_prh(int row, int col) {
        if (row < 1 || row > 8 || col < 1 || col > 8) return nullptr;
        Seat_prh* head = rows_prh[row - 1];
        Seat_prh* temp = head;
        for (int i = 1; i < col; ++i) temp = temp->next_prh;
        return temp;
    }

    bool bookSeat_prh(int row, int col) {
        Seat_prh* seat = getSeat_prh(row, col);
        if (!seat) return false;
        if (seat->booked_prh) return false;
        seat->booked_prh = true;
        return true;
    }

    // Atomic multi-seat booking: verifies availability first
    bool bookSeatsAtomic_prh(const vector<pair<int,int>>& requests) {
        // Validate all seats exist and are free
        for (auto &p : requests) {
            Seat_prh* s = getSeat_prh(p.first, p.second);
            if (!s || s->booked_prh) return false; // cannot fulfill
        }
        // All free — now mark booked
        for (auto &p : requests) {
            Seat_prh* s = getSeat_prh(p.first, p.second);
            s->booked_prh = true;
        }
        return true;
    }

    bool cancelSeat_prh(int row, int col) {
        Seat_prh* seat = getSeat_prh(row, col);
        if (!seat) return false;
        if (!seat->booked_prh) return false;
        seat->booked_prh = false;
        return true;
    }

    void randomPrebook_prh(int count) {
        srand((unsigned)time(nullptr));
        int tries = 0;
        int booked = 0;
        while (booked < count && tries < 1000) {
            int r = (rand() % 8) + 1;
            int c = (rand() % 8) + 1;
            Seat_prh* s = getSeat_prh(r, c);
            if (s && !s->booked_prh) {
                s->booked_prh = true;
                ++booked;
            }
            ++tries;
        }
    }
};

int main() {
    Multiplex_prh multiplex;
    cout << "Welcome to Galaxy Multiplex Reservation System\n";

    while (true) {
        cout << "\n1. Display Seats\n2. Book Seat(s)\n3. Cancel Booking\n4. Exit\nEnter choice: ";
        int choice; if (!(cin >> choice)) { cout << "Invalid input\n"; cin.clear(); cin.ignore(10000,'\n'); continue; }

        if (choice == 1) {
            multiplex.displaySeats_prh();
        } else if (choice == 2) {
            cout << "Enter number of seats to book: ";
            int k; cin >> k;
            vector<pair<int,int>> req;
            for (int i = 0; i < k; ++i) {
                int r, c; cout << "Seat " << (i+1) << " Row and Column: "; cin >> r >> c;
                req.emplace_back(r, c);
            }
            // Attempt atomic booking
            if (multiplex.bookSeatsAtomic_prh(req)) {
                cout << "Booking successful for all requested seats.\n";
            } else {
                cout << "Could not book all seats: at least one seat is invalid or already booked.\n";
            }
        } else if (choice == 3) {
            int r, c; cout << "Enter Row and Column to cancel: "; cin >> r >> c;
            if (multiplex.cancelSeat_prh(r, c)) cout << "Cancelled successfully.\n";
            else cout << "Cancel failed: invalid seat or seat was not booked.\n";
        } else if (choice == 4) {
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

**Compile:**
```bash
g++ -std=c++11 -O2 galaxy_multiplex_reservation_in_depth.cpp -o galaxy_reserve
```

**Run:**
```bash
./galaxy_reserve
```

On Windows (MinGW):
```bash
g++ -std=c++11 galaxy_multiplex_reservation_in_depth.cpp -o galaxy_reserve.exe
./galaxy_reserve.exe
```

---

## 7. Example Runs and Test Cases

### Sample Interaction (user input shown after `>`):
```
Welcome to Galaxy Multiplex Reservation System

1. Display Seats
2. Book Seat(s)
3. Cancel Booking
4. Exit
Enter choice: > 1

--- Current Seat Availability ---
Row 1: [O] [O] [X] [O] [O] [O] [O] [O]
Row 2: [O] [X] [O] [O] [O] [O] [O] [O]
...

Enter choice: > 2
Enter number of seats to book: > 2
Seat 1 Row and Column: > 1 1
Seat 2 Row and Column: > 1 2
Booking successful for all requested seats.

Enter choice: > 1
Row 1: [X] [X] [X] [O] [O] [O] [O] [O]

Enter choice: > 3
Enter Row and Column to cancel: > 1 3
Cancelled successfully.

Enter choice: > 4
Exiting...
```

### Test cases to try
1. Book an already booked seat — should fail atomic booking.
2. Cancel a seat that is not booked — should report failure.
3. Provide invalid row/column (e.g., 0 or 9) — should be rejected.
4. Book all seats in a row — verify display shows all `[X]`.

---

## 8. Memory Management & Edge Cases
- The destructor deletes every allocated `Seat_prh` node to avoid memory leaks.
- `randomPrebook_prh` has a `tries` cap to avoid infinite loops if count > available seats.
- Input validation: `getSeat_prh` returns `nullptr` for invalid indices and caller checks.
- Atomic booking prevents partial bookings for multi-seat requests (either all or none).

---

## 9. Extensions and Enhancements (ideas)
- **Persistent storage:** Save/load booking state to/from a file (CSV or binary).
- **GUI:** Build a simple Qt/GTK or web-based UI that visualizes the seat map.
- **Seat categories:** Add pricing tiers (VIP, Regular) and reserved seats.
- **Concurrent access:** Add locking or transaction handling for multi-threaded or networked scenarios.
- **Best-fit booking:** Implement automatic best contiguous-seat allocation for group bookings.
- **Search/filter:** Find available contiguous blocks of N seats in any row.

---

## 10. References
- Standard C++ documentation
- Data structures textbooks (doubly circular linked list concept)

---

**End of document — Galaxy Multiplex Ticket Reservation System (In-Depth)**
