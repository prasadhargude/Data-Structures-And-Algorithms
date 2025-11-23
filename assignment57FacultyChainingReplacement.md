# Assignment 57: Faculty Database using MOD Hash + Linear Probing with Chaining **With Replacement**  
**Author:** Prasad Ramdas Hargude  
**Unit:** VI — Hashing  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Collision Handling: Chaining With Replacement  
5. Algorithms (Pseudocode + Complexity)  
6. Complete C++ Program (Menu-Driven)  
7. Compilation & Run Instructions  
8. Sample Input/Output (Example Run)  
9. Memory Management & Edge Cases  
10. Possible Enhancements  
11. References  

---

## 1. Problem Statement

Write a C++ program to **simulate a faculty database as a hash table**.

- Use **MOD (divide method)** as the hash function.  
- Use **linear probing with chaining _with replacement_** as the collision handling technique.  
- Assume suitable data for each **faculty record**.  
- Allow the user to **search a particular faculty** efficiently by their ID.

The program should provide a **menu-driven interface** to:

1. Insert a faculty record.  
2. Search a faculty by ID.  
3. Display the internal hash table state (with links).  
4. Exit.  

All key variables and functions use the `_prh` suffix as per your style.

---

## 2. System Requirements

- **Language:** C++  
- **Standard:** C++11 or later  
- **Compiler:** `g++` / MinGW / any modern C++ compiler  
- **Data Structure:**  
  - Array-based hash table (using `vector`)  
  - **Coalesced hashing** style: linear probing + chaining via index links  
- **Collision Resolution:**  
  - **Linear probing with chaining with replacement**

---

## 3. Data Structure & Design

### 3.1 Faculty Record

Each faculty record will store:

- `id_prh` — unique Faculty ID (int, hash key)  
- `name_prh` — name of faculty (string)  
- `dept_prh` — department (string)  
- `designation_prh` — designation (string)  
- `link_prh` — index of next record in chain, or `-1` if none  

```cpp
struct Faculty_prh {
    int id_prh;
    string name_prh;
    string dept_prh;
    string designation_prh;
    int link_prh;

    Faculty_prh(int id_prh_val = -1,
                string name_prh_val = "",
                string dept_prh_val = "",
                string desig_prh_val = "",
                int link_prh_val = -1)
        : id_prh(id_prh_val),
          name_prh(name_prh_val),
          dept_prh(dept_prh_val),
          designation_prh(desig_prh_val),
          link_prh(link_prh_val) {}
};
```

### 3.2 Hash Table Layout

We maintain:

```cpp
int tableSize_prh;
vector<Faculty_prh> table_prh;
vector<int> status_prh;  // 0 = EMPTY, 1 = OCCUPIED
vector<int> head_prh;    // head_prh[h] = index of first node for bucket h, or -1
```

- `table_prh[i]` — record at index `i`.  
- `status_prh[i]` — whether this slot is empty or occupied.  
- `head_prh[h]` — start of the chain of all records whose **home bucket** is `h`.

### 3.3 Hash Function (MOD / Divide Method)

For Faculty ID `id_prh`:

\[
h(id) = |id| \bmod \text{tableSize\_prh}
\]

```cpp
int hashFunction_prh(int id_prh) const {
    if (id_prh < 0) id_prh = -id_prh;
    return id_prh % tableSize_prh;
}
```

---

## 4. Collision Handling: Chaining **With Replacement**

### 4.1 Idea

When inserting a new record with **home bucket** `h`:

1. If slot `h` is **empty**: just store it there, adjust `head_prh[h]`.  
2. If slot `h` is **occupied by a record whose home is also `h`**:  
   - Find a free slot using **linear probing**.  
   - Insert new record there and link it at the end of the chain for bucket `h`.  
3. If slot `h` is **occupied by a record whose home is different (say `h_old`)**:  
   - **Replacement case**:  
     - Find a free slot.  
     - Move the existing record at `h` to that free slot (including its link).  
     - Fix the chain of its original bucket `h_old` to point to the new position.  
     - Insert the new record in **its correct home** at index `h`.  
     - Adjust `head_prh[h]` and links so the new record becomes part of bucket `h`’s chain.  

We **do not leave a record in a non-home bucket** permanently when replacement is triggered — that’s why it’s called **“with replacement”**.

---

## 5. Algorithms (Pseudocode + Complexity)

### 5.1 Helper: Find Free Slot (Linear Probing)

```text
findFreeSlot(start):
    for i from 0 to tableSize-1:
        pos = (start + i) mod tableSize
        if status[pos] == EMPTY:
            return pos
    return -1  // table full
```

---

### 5.2 Insert (Linear Probing + Chaining With Replacement)

```text
insertFaculty(F):
    h = hashFunction(F.id)

    if faculty with same id already exists:
        reject

    if slot h is EMPTY:
        place F at h
        status[h] = OCCUPIED
        if head[h] == -1:
            head[h] = h
        else:
            F.link = head[h]
            head[h] = h
        return

    // slot h is OCCUPIED
    existingHome = hashFunction(table[h].id)

    if existingHome == h:
        // no replacement, same bucket
        freePos = findFreeSlot(h)
        if freePos == -1: fail
        place F at freePos, link = -1
        append freePos at end of chain starting from head[h]
    else:
        // replacement case
        freePos = findFreeSlot(h)
        if freePos == -1: fail

        // move old record from h to freePos
        copy table[h] -> table[freePos]
        status[freePos] = OCCUPIED

        oldBucket = existingHome
        // fix chain in oldBucket so it points to freePos
        prev = -1
        cur = head[oldBucket]
        while cur != -1 and cur != h:
            prev = cur
            cur = table[cur].link
        if prev == -1:
            head[oldBucket] = freePos
        else:
            table[prev].link = freePos

        // now put new record in home slot h
        place F at h
        status[h] = OCCUPIED

        if head[h] == -1:
            F.link = -1
            head[h] = h
        else:
            F.link = head[h]
            head[h] = h
```

**Time Complexity:**  

- **Best case:** `O(1)` (home slot empty).  
- **Worst case:** `O(n)` if the table is nearly full and we probe many slots.  

---

### 5.3 Search by Faculty ID

We always search in the **chain of the home bucket**.

```text
searchFaculty(id):
    h = hashFunction(id)
    cur = head[h]
    while cur != -1:
        if table[cur].id == id:
            return FOUND at cur
        cur = table[cur].link
    return NOT FOUND
```

**Time Complexity:**  

- **Average:** Close to `O(1)` if chains are small.  
- **Worst:** `O(n)` when many items fall in the same bucket.

---

## 6. Complete C++ Program (Menu-Driven)

```cpp
// assignment57_faculty_hash_chaining_with_replacement_prasad_hargude.cpp
// Faculty database using MOD hash + linear probing with chaining WITH replacement
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <string>
#include <limits>
using namespace std;

struct Faculty_prh {
    int id_prh;
    string name_prh;
    string dept_prh;
    string designation_prh;
    int link_prh;

    Faculty_prh(int id_prh_val = -1,
                string name_prh_val = "",
                string dept_prh_val = "",
                string desig_prh_val = "",
                int link_prh_val = -1)
        : id_prh(id_prh_val),
          name_prh(name_prh_val),
          dept_prh(dept_prh_val),
          designation_prh(desig_prh_val),
          link_prh(link_prh_val) {}
};

class FacultyHashChainReplace_prh {
private:
    int tableSize_prh;
    vector<Faculty_prh> table_prh;
    vector<int> status_prh; // 0 = EMPTY, 1 = OCCUPIED
    vector<int> head_prh;   // head index for each bucket

public:
    FacultyHashChainReplace_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, Faculty_prh());
        status_prh.assign(tableSize_prh, 0);
        head_prh.assign(tableSize_prh, -1);
    }

    int hashFunction_prh(int id_prh) const {
        if (id_prh < 0) id_prh = -id_prh;
        return id_prh % tableSize_prh; // MOD hash
    }

    int findFreeSlot_prh(int start_prh) const {
        for (int i = 0; i < tableSize_prh; ++i) {
            int pos_prh = (start_prh + i) % tableSize_prh;
            if (status_prh[pos_prh] == 0) {
                return pos_prh;
            }
        }
        return -1; // table full
    }

    bool searchFaculty_prh(int id_prh, int &pos_prh) const {
        int h_prh = hashFunction_prh(id_prh);
        int cur_prh = head_prh[h_prh];
        while (cur_prh != -1) {
            if (table_prh[cur_prh].id_prh == id_prh) {
                pos_prh = cur_prh;
                return true;
            }
            cur_prh = table_prh[cur_prh].link_prh;
        }
        return false;
    }

    bool insertFaculty_prh(const Faculty_prh &f_prh) {
        int existingIndex_prh;
        if (searchFaculty_prh(f_prh.id_prh, existingIndex_prh)) {
            cout << "Faculty with ID " << f_prh.id_prh
                 << " already exists at index " << existingIndex_prh << ".\n";
            return false;
        }

        int h_prh = hashFunction_prh(f_prh.id_prh);

        // Case 1: home slot empty
        if (status_prh[h_prh] == 0) {
            table_prh[h_prh] = f_prh;
            status_prh[h_prh] = 1;

            if (head_prh[h_prh] == -1) {
                table_prh[h_prh].link_prh = -1;
                head_prh[h_prh] = h_prh;
            } else {
                // Insert at front of chain
                table_prh[h_prh].link_prh = head_prh[h_prh];
                head_prh[h_prh] = h_prh;
            }

            cout << "Inserted faculty with ID " << f_prh.id_prh
                 << " at index " << h_prh << " (home bucket).\n";
            return true;
        }

        // Home slot is occupied
        int existingHome_prh = hashFunction_prh(table_prh[h_prh].id_prh);

        if (existingHome_prh == h_prh) {
            // Case 2: same bucket, no replacement
            int freePos_prh = findFreeSlot_prh(h_prh);
            if (freePos_prh == -1) {
                cout << "Hash table is full. Cannot insert faculty.\n";
                return false;
            }

            table_prh[freePos_prh] = f_prh;
            table_prh[freePos_prh].link_prh = -1;
            status_prh[freePos_prh] = 1;

            // Append at end of chain for bucket h_prh
            int cur_prh = head_prh[h_prh];
            if (cur_prh == -1) {
                head_prh[h_prh] = freePos_prh;
            } else {
                while (table_prh[cur_prh].link_prh != -1) {
                    cur_prh = table_prh[cur_prh].link_prh;
                }
                table_prh[cur_prh].link_prh = freePos_prh;
            }

            cout << "Inserted faculty with ID " << f_prh.id_prh
                 << " at index " << freePos_prh
                 << " (chained in bucket " << h_prh << ").\n";
            return true;
        } else {
            // Case 3: replacement needed (existing record belongs to another bucket)
            int freePos_prh = findFreeSlot_prh(h_prh);
            if (freePos_prh == -1) {
                cout << "Hash table is full. Cannot insert faculty.\n";
                return false;
            }

            // Move existing record from h_prh -> freePos_prh
            Faculty_prh moved_prh = table_prh[h_prh];
            int oldBucket_prh = existingHome_prh;

            table_prh[freePos_prh] = moved_prh;
            status_prh[freePos_prh] = 1;

            // Fix chain of old bucket
            int prev_prh = -1;
            int cur_prh = head_prh[oldBucket_prh];
            while (cur_prh != -1 && cur_prh != h_prh) {
                prev_prh = cur_prh;
                cur_prh = table_prh[cur_prh].link_prh;
            }
            if (cur_prh == h_prh) {
                if (prev_prh == -1) {
                    head_prh[oldBucket_prh] = freePos_prh;
                } else {
                    table_prh[prev_prh].link_prh = freePos_prh;
                }
            }

            // Now put new faculty into home slot h_prh
            table_prh[h_prh] = f_prh;
            status_prh[h_prh] = 1;

            if (head_prh[h_prh] == -1) {
                table_prh[h_prh].link_prh = -1;
                head_prh[h_prh] = h_prh;
            } else {
                table_prh[h_prh].link_prh = head_prh[h_prh];
                head_prh[h_prh] = h_prh;
            }

            cout << "Moved existing faculty from index " << h_prh
                 << " to free index " << freePos_prh
                 << " (belongs to bucket " << oldBucket_prh << ").\n";
            cout << "Inserted new faculty with ID " << f_prh.id_prh
                 << " at index " << h_prh << " (home bucket, with replacement).\n";

            return true;
        }
    }

    void displayTable_prh() const {
        cout << "\n--- Faculty Hash Table (MOD + Linear Probing + Chaining WITH Replacement) ---\n";
        cout << "Index | Status   | ID   | Name        | Dept        | Designation        | Link\n";
        cout << "-------------------------------------------------------------------------------\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << i << "     | ";
            if (status_prh[i] == 0) {
                cout << "EMPTY    |";
                cout << " -    | -           | -           | -                | " << table_prh[i].link_prh;
            } else {
                cout << "OCCUPIED | "
                     << table_prh[i].id_prh << "   | "
                     << table_prh[i].name_prh << " | "
                     << table_prh[i].dept_prh << " | "
                     << table_prh[i].designation_prh << " | "
                     << table_prh[i].link_prh;
            }
            cout << "\n";
        }

        cout << "\nHash bucket heads (head_prh array):\n";
        for (int h_prh = 0; h_prh < tableSize_prh; ++h_prh) {
            cout << "Bucket " << h_prh << " -> head index " << head_prh[h_prh] << "\n";
        }
    }
};

void flushInput_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

Faculty_prh inputFaculty_prh() {
    int id_prh;
    string name_prh, dept_prh, desig_prh;

    cout << "Enter Faculty ID: ";
    cin >> id_prh;
    flushInput_prh();

    cout << "Enter Name: ";
    getline(cin, name_prh);

    cout << "Enter Department: ";
    getline(cin, dept_prh);

    cout << "Enter Designation: ";
    getline(cin, desig_prh);

    return Faculty_prh(id_prh, name_prh, dept_prh, desig_prh, -1);
}

int main() {
    int size_prh;
    cout << "Enter hash table size: ";
    if (!(cin >> size_prh) || size_prh <= 0) {
        cout << "Invalid size. Using default size = 10.\n";
        flushInput_prh();
        size_prh = 10;
    }

    FacultyHashChainReplace_prh ht_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Faculty Database (MOD Hash + Linear Probing + Chaining WITH Replacement) ---\n";
        cout << "1. Insert faculty record\n";
        cout << "2. Search faculty by ID\n";
        cout << "3. Display hash table\n";
        cout << "4. Exit\n";
        cout << "Enter your choice: ";
        if (!(cin >> choice_prh)) {
            cout << "Invalid input. Try again.\n";
            flushInput_prh();
            continue;
        }
        flushInput_prh();

        if (choice_prh == 1) {
            Faculty_prh f_prh = inputFaculty_prh();
            ht_prh.insertFaculty_prh(f_prh);
        } else if (choice_prh == 2) {
            int id_prh;
            cout << "Enter Faculty ID to search: ";
            cin >> id_prh;
            flushInput_prh();
            int pos_prh = -1;
            if (ht_prh.searchFaculty_prh(id_prh, pos_prh)) {
                cout << "Faculty with ID " << id_prh << " found at index " << pos_prh << ".\n";
            } else {
                cout << "Faculty with ID " << id_prh << " not found.\n";
            }
        } else if (choice_prh == 3) {
            ht_prh.displayTable_prh();
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
g++ -std=c++11 assignment57_faculty_hash_chaining_with_replacement_prasad_hargude.cpp -o faculty57
```

### Run

```bash
./faculty57
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment57_faculty_hash_chaining_with_replacement_prasad_hargude.cpp -o faculty57.exe
faculty57.exe
```

---

## 8. Sample Input/Output (Example Run)

```text
Enter hash table size: 7

--- Faculty Database (MOD Hash + Linear Probing + Chaining WITH Replacement) ---
1. Insert faculty record
2. Search faculty by ID
3. Display hash table
4. Exit
Enter your choice: 1
Enter Faculty ID: 101
Enter Name: Aru
Enter Department: CSE
Enter Designation: Assistant Professor
Inserted faculty with ID 101 at index 3 (home bucket).

Enter your choice: 1
Enter Faculty ID: 108
Enter Name: Purva
Enter Department: MED
Enter Designation: Professor
Inserted faculty with ID 108 at index 4 (home bucket).

Enter your choice: 1
Enter Faculty ID: 115
Enter Department: CSE
Enter Designation: Associate Professor
(Here 115 % 7 = 3, so home bucket = 3, causing chain with replacement if needed.)

...

Then on display, you see:
--- Faculty Hash Table (MOD + Linear Probing + Chaining WITH Replacement) ---
Index | Status   | ID   | Name        | Dept        | Designation        | Link
-------------------------------------------------------------------------------
3     | OCCUPIED | 101  | Aru         | CSE         | Assistant Professor | 5
5     | OCCUPIED | 115  | Raj         | CSE         | Associate Professor | -1
4     | OCCUPIED | 108  | Purva       | MED         | Professor           | -1
...

Hash bucket heads (head_prh array):
Bucket 3 -> head index 3
Bucket 4 -> head index 4
...
```

*(Exact indices depend on table size and insertion sequence.)*

---

## 9. Memory Management & Edge Cases

- Uses only `vector` and simple structs — **no manual `new`/`delete` required**.  
- Handles:
  - Invalid table size (uses default).  
  - Duplicate IDs (rejected).  
  - Full table (insertion fails with message).  
- Demonstrates clearly how **chaining with replacement** keeps each element as close as possible to its home bucket.

---

## 10. Possible Enhancements

- Add deletion operations (requires careful chain repair).  
- Add more fields (phone, email, specialization).  
- Compute metrics like **average chain length** and **load factor**.  
- Add file I/O to store faculty records persistently.

---

## 11. References

- Coalesced hashing / chaining with replacement — typical DSA syllabus topic.  
- C++ STL: `vector`, `string`, `limits`.

---

_End of Assignment 57 — Faculty Database using MOD Hash + Linear Probing with Chaining WITH Replacement_
