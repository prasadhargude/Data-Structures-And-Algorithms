# Assignment 56: Faculty Database using Hash Table (Divide Hash + Linear Probing with Chaining Without Replacement)  
**Author:** Prasad Ramdas Hargude  
**Unit:** VI — Hashing  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (Pseudocode + Complexity)  
5. Complete C++ Program (Menu-Driven)  
6. Compilation & Run Instructions  
7. Sample Input/Output (Example Run)  
8. Memory Management & Edge Cases  
9. Possible Enhancements  
10. References  

---

## 1. Problem Statement

Write a C++ program to **simulate a faculty database** using a **hash table**.

- Use **divide method** (`MOD`) as the hash function.  
- Use **linear probing with chaining without replacement** as the collision handling technique.  
- Use suitable fields for a **faculty record**.  
- Support efficient **search of a particular faculty** using the hash table.

The program should provide a **menu-driven interface** to:

1. Insert a faculty record.  
2. Search for a faculty by ID.  
3. Display the hash table (showing chaining links).  
4. Exit.

All main identifiers must use the `_prh` suffix.

---

## 2. System Requirements

- **Language:** C++  
- **Standard:** C++11 or later  
- **Compiler:** Any modern C++ compiler (`g++`, MinGW, etc.)  
- **Data Structure:**  
  - Hash table implemented using an **array** (or `vector`) of records  
  - Each record has a **link field** to build chains  
- **Collision Strategy:**  
  - **Linear probing** to find free slots  
  - **Chaining without replacement:**  
    - We **never move** existing records once placed  
    - We maintain a **separate head index** for each hash bucket  

---

## 3. Data Structure & Design

### 3.1 Faculty Record Structure

Each faculty record consists of:

- `id_prh` — Unique Faculty ID (int, used as hash key)  
- `name_prh` — Faculty name (string)  
- `dept_prh` — Department (string)  
- `designation_prh` — Designation (string, e.g., Assistant Professor, Professor)  
- `link_prh` — Index of the **next record in the chain**, or `-1` if end of chain  

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

### 3.2 Hash Table Arrays

We use:

```cpp
int tableSize_prh;
vector<Faculty_prh> table_prh;
vector<int> status_prh;  // 0 = EMPTY, 1 = OCCUPIED
vector<int> head_prh;    // head_prh[hash] = index of first node in that hash chain, or -1
```

- `table_prh[i]` holds the faculty record at index `i`.  
- `status_prh[i]` tells whether that slot is **empty** or **occupied**.  
- `head_prh[h]` gives the **starting index** of the chain for hash bucket `h`.  
  - If `head_prh[h] == -1`, there is currently **no record** with hash `h`.  

### 3.3 Hash Function — Divide Method

We use the **divide (modulus) method**:

\[
h(id) = |id| \bmod \text{tableSize\_prh}
\]

---

## 4. Algorithms (Pseudocode + Complexity)

### 4.1 Hash Function

```text
hashFunction(id):
    if id < 0: id = -id
    return id % tableSize
```

- **Time Complexity:** O(1)

---

### 4.2 Insert (Linear Probing + Chaining Without Replacement)

We want to:

- Compute **home bucket h** for the faculty ID.  
- If no record exists yet for this bucket (`head_prh[h] == -1`):
  - Place this record into:
    - the **home slot h** if it is empty, else
    - any free slot found via **linear probing**.
  - Set `head_prh[h]` to that index.
- If some records already exist with same hash `h`:
  - Use linear probing to find a free slot.
  - Insert the new record there.
  - Append it to the **end of the existing chain**.

#### Pseudocode

```text
insertFaculty(faculty):
    h = hashFunction(faculty.id)

    if head[h] == -1:
        // first record for this hash
        if status[h] == EMPTY:
            pos = h
        else:
            pos = findFreeSlotByLinearProbing(h)
            if pos == -1: report "table full" and return false
        place faculty at table[pos], link[pos] = -1
        status[pos] = OCCUPIED
        head[h] = pos
    else:
        // chain already exists for this hash bucket
        pos = findFreeSlotByLinearProbing(h)
        if pos == -1: report "table full" and return false
        place faculty at table[pos], link[pos] = -1
        status[pos] = OCCUPIED

        // append at end of chain starting at head[h]
        cur = head[h]
        while table[cur].link != -1:
            cur = table[cur].link
        table[cur].link = pos
```

**Time Complexity:**

- Best: **O(1)** (home slot empty, no chain)  
- Worst: **O(n)** (table nearly full → long linear probe)

---

### 4.3 Search by Faculty ID

To search:

1. Compute `h = hashFunction(id)`.  
2. Look at `head_prh[h]`.  
3. If `head_prh[h] == -1`, **no record** with that hash → not found.  
4. Otherwise, traverse the linked chain starting at `head_prh[h]`:
   - At each index, if `id_prh` matches, return **FOUND**.  
   - Otherwise follow `link_prh` until `-1`.

#### Pseudocode

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
- Average: close to **O(1)** if chains are short  
- Worst: **O(n)** when all records fall in same chain  

---

## 5. Complete C++ Program (Menu-Driven)

```cpp
// assignment56_faculty_hash_chaining_no_replacement_prasad_hargude.cpp
// Faculty database using hash table (divide hash + linear probing with chaining without replacement)
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

class FacultyHashChain_prh {
private:
    int tableSize_prh;
    vector<Faculty_prh> table_prh;
    vector<int> status_prh;  // 0 = EMPTY, 1 = OCCUPIED
    vector<int> head_prh;    // head index for each hash bucket (-1 if none)

public:
    FacultyHashChain_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, Faculty_prh());
        status_prh.assign(tableSize_prh, 0);
        head_prh.assign(tableSize_prh, -1);
    }

    int hashFunction_prh(int id_prh) const {
        if (id_prh < 0) id_prh = -id_prh;
        return id_prh % tableSize_prh; // divide (mod) method
    }

    // Find a free slot using linear probing (returns -1 if none)
    int findFreeSlot_prh(int start_prh) const {
        for (int i = 0; i < tableSize_prh; ++i) {
            int pos_prh = (start_prh + i) % tableSize_prh;
            if (status_prh[pos_prh] == 0) {
                return pos_prh;
            }
        }
        return -1; // table full
    }

    bool insertFaculty_prh(const Faculty_prh &f_prh) {
        int h_prh = hashFunction_prh(f_prh.id_prh);

        // Check if this exact ID already exists in its chain
        int existingIndex_prh;
        if (searchFaculty_prh(f_prh.id_prh, existingIndex_prh)) {
            cout << "Faculty with ID " << f_prh.id_prh << " already exists at index "
                 << existingIndex_prh << ".\n";
            return false;
        }

        int pos_prh = -1;

        if (head_prh[h_prh] == -1) {
            // First record for this hash bucket
            if (status_prh[h_prh] == 0) {
                pos_prh = h_prh;
            } else {
                // home location occupied by some other bucket (no replacement)
                pos_prh = findFreeSlot_prh(h_prh);
            }
            if (pos_prh == -1) {
                cout << "Hash table is full. Cannot insert faculty.\n";
                return false;
            }
            table_prh[pos_prh] = f_prh;
            table_prh[pos_prh].link_prh = -1;
            status_prh[pos_prh] = 1;
            head_prh[h_prh] = pos_prh;
        } else {
            // Chain already exists for this hash bucket
            pos_prh = findFreeSlot_prh(h_prh);
            if (pos_prh == -1) {
                cout << "Hash table is full. Cannot insert faculty.\n";
                return false;
            }
            table_prh[pos_prh] = f_prh;
            table_prh[pos_prh].link_prh = -1;
            status_prh[pos_prh] = 1;

            // Append to the end of chain starting at head_prh[h_prh]
            int cur_prh = head_prh[h_prh];
            while (table_prh[cur_prh].link_prh != -1) {
                cur_prh = table_prh[cur_prh].link_prh;
            }
            table_prh[cur_prh].link_prh = pos_prh;
        }

        cout << "Inserted faculty with ID " << f_prh.id_prh
             << " at index " << pos_prh << " (hash bucket " << h_prh << ").\n";
        return true;
    }

    // Search faculty by ID; returns true and position if found
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

    void displayTable_prh() const {
        cout << "\n--- Faculty Hash Table (Linear Probing + Chaining Without Replacement) ---\n";
        cout << "Index | Status   | ID   | Name        | Dept        | Designation   | Link\n";
        cout << "--------------------------------------------------------------------------\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << i << "     | ";
            if (status_prh[i] == 0) {
                cout << "EMPTY    |";
            } else {
                cout << "OCCUPIED |"
                     << " " << table_prh[i].id_prh << "   "
                     << "| " << table_prh[i].name_prh
                     << " | " << table_prh[i].dept_prh
                     << " | " << table_prh[i].designation_prh;
            }
            cout << " | " << table_prh[i].link_prh << "\n";
        }

        cout << "\nHash bucket heads (head_prh array):\n";
        for (int h = 0; h < tableSize_prh; ++h) {
            cout << "Bucket " << h << " -> head index " << head_prh[h] << "\n";
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

    FacultyHashChain_prh ht_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Faculty Database (Hash Table: Divide + Linear Probing + Chaining) ---\n";
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

## 6. Compilation & Run Instructions

### Compile

```bash
g++ -std=c++11 assignment56_faculty_hash_chaining_no_replacement_prasad_hargude.cpp -o faculty56
```

### Run

```bash
./faculty56
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment56_faculty_hash_chaining_no_replacement_prasad_hargude.cpp -o faculty56.exe
faculty56.exe
```

---

## 7. Sample Input/Output (Example Run)

```text
Enter hash table size: 7

--- Faculty Database (Hash Table: Divide + Linear Probing + Chaining) ---
1. Insert faculty record
2. Search faculty by ID
3. Display hash table
4. Exit
Enter your choice: 1
Enter Faculty ID: 101
Enter Name: Aru
Enter Department: CSE
Enter Designation: Assistant Professor
Inserted faculty with ID 101 at index 3 (hash bucket 3).

Enter your choice: 1
Enter Faculty ID: 108
Enter Name: Purva
Enter Department: MED
Enter Designation: Professor
Inserted faculty with ID 108 at index 4 (hash bucket 4).

Enter your choice: 1
Enter Faculty ID: 115
Enter Name: Raj
Enter Department: CSE
Enter Designation: Associate Professor
Inserted faculty with ID 115 at index 5 (hash bucket 3).
(Here 115 % 7 = 3, so it chains with 101.)

Enter your choice: 3

--- Faculty Hash Table (Linear Probing + Chaining Without Replacement) ---
Index | Status   | ID   | Name        | Dept        | Designation   | Link
--------------------------------------------------------------------------
0     | EMPTY    | -1
1     | EMPTY    | -1
2     | EMPTY    | -1
3     | OCCUPIED | 101   | Aru | CSE | Assistant Professor | 5
4     | OCCUPIED | 108   | Purva | MED | Professor | -1
5     | OCCUPIED | 115   | Raj | CSE | Associate Professor | -1
6     | EMPTY    | -1

Hash bucket heads (head_prh array):
Bucket 0 -> head index -1
Bucket 1 -> head index -1
Bucket 2 -> head index -1
Bucket 3 -> head index 3
Bucket 4 -> head index 4
Bucket 5 -> head index -1
Bucket 6 -> head index -1

Enter your choice: 2
Enter Faculty ID to search: 115
Faculty with ID 115 found at index 5.

Enter your choice: 4
Exiting...
```

*(Exact indices may vary depending on table size and sequence of insertions.)*

---

## 8. Memory Management & Edge Cases

- Uses only `vector` and simple structs — no manual `new`/`delete`.  
- Handles:
  - Invalid table size input → defaults to 10.  
  - Duplicate faculty IDs → prevented (search before insert).  
  - Full table → insertion fails with a clear message.  
  - Clearly shows **chains** using `link_prh` and `head_prh` arrays.

---

## 9. Possible Enhancements

- Add **deletion** (requires careful chain maintenance).  
- Store more details (phone, email, specialization).  
- Track **average chain length** and **load factor**.  
- Persist data to files for long-term storage.  

---

## 10. References

- Hashing with **coalesced chaining (chaining with/without replacement)** — standard DSA notes.  
- C++ STL documentation (`vector`, `string`, `limits`).  

---

_End of Assignment 56 — Faculty Database using Divide Hash + Linear Probing + Chaining Without Replacement_
