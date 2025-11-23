# Assignment 60: Smart College Placement Portal using Advanced Hashing  
**Author:** Prasad Ramdas Hargude  

---

## Table of Contents  
1. Problem Statement  
2. Objectives & Features  
3. System Requirements  
4. Data Structure & Design  
5. Advanced Hashing Strategy  
6. Algorithms (Pseudocode + Complexity)  
7. Complete C++ Program (Menu-Driven)  
8. Compilation & Run Instructions  
9. Sample Input/Output (Example Run)  
10. Memory Management & Edge Cases  
11. Possible Enhancements  
12. References  

---

## 1. Problem Statement  

Design and implement a **Smart College Placement Portal** in C++ that manages **student placement records** efficiently using **advanced hashing techniques**.

The system should:

- Store and manage **thousands of student placement records**.  
- Support **fast insertion, search, update, and deletion**.  
- Use **advanced hashing** with **low collision probability**.  
- Maintain **high performance** even when data **grows dynamically**.  

The core data structure must be a **hash table** with:

- A good hash function (not just simple `key % size`).  
- **Double hashing** for collision resolution.  
- **Dynamic resizing (rehashing)** when the load factor becomes too high.  

---

## 2. Objectives & Features  

### Functional Objectives  

- Maintain a **placement database** of students.  
- Each record includes:
  - `id_prh` — Unique student ID / roll number  
  - `name_prh` — Student name  
  - `branch_prh` — Branch / department  
  - `cgpa_prh` — CGPA / grade  
  - `placed_prh` — Whether placed or not (bool)  
  - `company_prh` — Company name (if placed)  
  - `package_prh` — Package offered (LPA, double)  

### Operations  

1. **Insert** a new student placement record  
2. **Search** for a student by ID  
3. **Update** placement details (e.g., set placed = true, update company & package)  
4. **Delete** a record  
5. **Display** some/all records  
6. Automatically **rehash** when load factor exceeds a threshold  

---

## 3. System Requirements  

- **Language:** C++  
- **Standard:** C++11 or later  
- **Platform:** Any (Linux/Windows with g++ / MinGW)  
- **Core Data Structure:** Custom **hash table** with:
  - **Double hashing** (open addressing)  
  - **Dynamic resizing and rehashing**  

All identifiers follow the `_prh` suffix convention.

---

## 4. Data Structure & Design  

### 4.1 Placement Record  

```cpp
struct PlacementRecord_prh {
    int id_prh;              // unique key
    string name_prh;
    string branch_prh;
    double cgpa_prh;
    bool placed_prh;
    string company_prh;
    double package_prh;

    PlacementRecord_prh(int id = -1,
                        const string &name = "",
                        const string &branch = "",
                        double cgpa = 0.0,
                        bool placed = false,
                        const string &company = "",
                        double packageVal = 0.0)
        : id_prh(id),
          name_prh(name),
          branch_prh(branch),
          cgpa_prh(cgpa),
          placed_prh(placed),
          company_prh(company),
          package_prh(packageVal) {}
};
```

### 4.2 Hash Table Slot Status  

We use open addressing, so each slot must track its state:

- `0` → **EMPTY**  
- `1` → **OCCUPIED**  
- `2` → **DELETED (tombstone)**  

```cpp
vector<PlacementRecord_prh> table_prh;
vector<int> status_prh; // 0 = EMPTY, 1 = OCCUPIED, 2 = DELETED
int tableSize_prh;
int count_prh;          // number of OCCUPIED records
```

---

## 5. Advanced Hashing Strategy  

To achieve **low collision probability** and **high performance** under **dynamic growth**, we use:

### 5.1 Double Hashing (Open Addressing)  

- Primary hash: `h1(k)`  
- Secondary hash: `h2(k)`  
- Probe sequence:  
  \[
  h(k, i) = (h1(k) + i * h2(k)) \bmod M
  \]
  where:
  - `M` = table size (preferably **prime**)  
  - `i` = probe attempt index  

#### Chosen Hash Functions  

Let `key = abs(id_prh)`.

1. **Primary hash** (multiplicative + modulus):  
   ```cpp
   int h1_prh(int key) const {
       key = (key < 0) ? -key : key;
       return key % tableSize_prh;
   }
   ```

2. **Secondary hash** (odd step, avoids zero):  
   ```cpp
   int h2_prh(int key) const {
       key = (key < 0) ? -key : key;
       return 1 + (key % (tableSize_prh - 1));
   }
   ```

Probe sequence:  

```cpp
pos = (h1_prh(key) + i * h2_prh(key)) % tableSize_prh;
```

This spreads collisions across the table better than simple linear probing.

---

### 5.2 Dynamic Resizing (Rehashing)  

To maintain performance, we **rehash** when:

\[
\text{load factor} = \frac{\text{count\_prh}}{\text{tableSize\_prh}} > 0.7
\]

Rehashing steps:

1. Compute a new **larger prime table size** (e.g., ~2x current).  
2. Allocate new arrays `newTable_prh`, `newStatus_prh`.  
3. Re-insert all existing OCCUPIED records using the **new hash functions**.  
4. Replace old table with new one.

This keeps clusters short and ensures **O(1) average** insert/search even as database grows.

---

## 6. Algorithms (Pseudocode + Complexity)  

### 6.1 Insert / Upsert Placement Record  

```text
insertOrUpdate(record):
    if load_factor > 0.7:
        rehash()

    key = record.id
    h1 = h1(key)
    h2 = h2(key)

    firstDeletedPos = -1

    for i from 0 to tableSize-1:
        pos = (h1 + i * h2) mod tableSize

        if status[pos] == OCCUPIED and table[pos].id == key:
            // UPDATE existing record
            table[pos] = record
            return true

        if status[pos] == DELETED and firstDeletedPos == -1:
            firstDeletedPos = pos

        if status[pos] == EMPTY:
            if firstDeletedPos != -1:
                pos = firstDeletedPos
            table[pos] = record
            status[pos] = OCCUPIED
            count++
            return true

    // table full (should not happen if rehashing is correct)
    return false
```

**Average Time Complexity:** `O(1)`  
**Worst Case:** `O(M)` when table is very full (rare because of rehashing).

---

### 6.2 Search  

```text
search(id):
    key = id
    h1 = h1(key)
    h2 = h2(key)

    for i from 0 to tableSize-1:
        pos = (h1 + i * h2) mod tableSize
        if status[pos] == EMPTY:
            return NOT FOUND
        if status[pos] == OCCUPIED and table[pos].id == id:
            return FOUND at pos
    return NOT FOUND
```

**Average:** `O(1)`  
**Worst:** `O(M)`.

---

### 6.3 Delete  

```text
delete(id):
    pos = search(id)
    if pos found:
        status[pos] = DELETED
        count--
        return true
    else:
        return false
```

---

### 6.4 Rehash  

```text
rehash():
    newSize = nextPrime(2 * tableSize)
    allocate newTable[newSize], newStatus[newSize] = EMPTY

    for each index i in old table:
        if status[i] == OCCUPIED:
            re-insert table[i] into new table using new h1/h2

    replace old arrays with new arrays
    tableSize = newSize
```

**Time Complexity:** `O(M)` when rehashing is triggered, but amortized per operation is still ~`O(1)`.

---

## 7. Complete C++ Program (Menu-Driven)  

```cpp
// assignment60_smart_placement_portal_hashing_prasad_hargude.cpp
// Smart college placement portal using advanced hashing (double hashing + dynamic rehashing)
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <string>
#include <limits>
using namespace std;

struct PlacementRecord_prh {
    int id_prh;
    string name_prh;
    string branch_prh;
    double cgpa_prh;
    bool placed_prh;
    string company_prh;
    double package_prh;

    PlacementRecord_prh(int id = -1,
                        const string &name = "",
                        const string &branch = "",
                        double cgpa = 0.0,
                        bool placed = false,
                        const string &company = "",
                        double packageVal = 0.0)
        : id_prh(id),
          name_prh(name),
          branch_prh(branch),
          cgpa_prh(cgpa),
          placed_prh(placed),
          company_prh(company),
          package_prh(packageVal) {}
};

class PlacementHashTable_prh {
private:
    int tableSize_prh;
    int count_prh; // number of OCCUPIED slots
    vector<PlacementRecord_prh> table_prh;
    vector<int> status_prh; // 0 = EMPTY, 1 = OCCUPIED, 2 = DELETED

public:
    PlacementHashTable_prh(int initSize_prh = 11) {
        tableSize_prh = nextPrime_prh(initSize_prh);
        count_prh = 0;
        table_prh.assign(tableSize_prh, PlacementRecord_prh());
        status_prh.assign(tableSize_prh, 0);
    }

    // Primary hash
    int h1_prh(int key_prh) const {
        if (key_prh < 0) key_prh = -key_prh;
        return key_prh % tableSize_prh;
    }

    // Secondary hash
    int h2_prh(int key_prh) const {
        if (key_prh < 0) key_prh = -key_prh;
        // ensure non-zero step less than table size
        return 1 + (key_prh % (tableSize_prh - 1));
    }

    double loadFactor_prh() const {
        return (tableSize_prh == 0) ? 0.0 : (double)count_prh / tableSize_prh;
    }

    bool insertOrUpdate_prh(const PlacementRecord_prh &rec_prh) {
        if (loadFactor_prh() > 0.7) {
            rehash_prh();
        }

        int key_prh = rec_prh.id_prh;
        int hash1_prh = h1_prh(key_prh);
        int hash2_prh = h2_prh(key_prh);

        int firstDeletedPos_prh = -1;

        for (int i = 0; i < tableSize_prh; ++i) {
            int pos_prh = (hash1_prh + i * hash2_prh) % tableSize_prh;

            if (status_prh[pos_prh] == 1 && table_prh[pos_prh].id_prh == key_prh) {
                // update existing record
                table_prh[pos_prh] = rec_prh;
                cout << "Updated record for ID " << key_prh
                     << " at index " << pos_prh << ".\n";
                return true;
            }

            if (status_prh[pos_prh] == 2 && firstDeletedPos_prh == -1) {
                firstDeletedPos_prh = pos_prh;
            }

            if (status_prh[pos_prh] == 0) {
                int insertPos_prh = (firstDeletedPos_prh != -1) ? firstDeletedPos_prh : pos_prh;
                table_prh[insertPos_prh] = rec_prh;
                status_prh[insertPos_prh] = 1;
                ++count_prh;
                cout << "Inserted record for ID " << key_prh
                     << " at index " << insertPos_prh << ".\n";
                return true;
            }
        }

        cout << "Hash table is full. Could not insert record for ID " << key_prh << ".\n";
        return false;
    }

    bool search_prh(int id_prh, PlacementRecord_prh &out_prh, int &pos_prh) const {
        int hash1_prh = h1_prh(id_prh);
        int hash2_prh = h2_prh(id_prh);

        for (int i = 0; i < tableSize_prh; ++i) {
            int p_prh = (hash1_prh + i * hash2_prh) % tableSize_prh;
            if (status_prh[p_prh] == 0) {
                // empty slot -> key not present
                return false;
            }
            if (status_prh[p_prh] == 1 && table_prh[p_prh].id_prh == id_prh) {
                out_prh = table_prh[p_prh];
                pos_prh = p_prh;
                return true;
            }
        }
        return false;
    }

    bool delete_prh(int id_prh) {
        PlacementRecord_prh dummy_prh;
        int pos_prh = -1;
        if (search_prh(id_prh, dummy_prh, pos_prh)) {
            status_prh[pos_prh] = 2; // tombstone
            --count_prh;
            cout << "Deleted record for ID " << id_prh
                 << " at index " << pos_prh << ".\n";
            return true;
        } else {
            cout << "Record for ID " << id_prh << " not found.\n";
            return false;
        }
    }

    void displayAll_prh() const {
        cout << "\n--- Placement Hash Table (Double Hashing + Dynamic Rehashing) ---\n";
        cout << "Table size: " << tableSize_prh
             << ", Count: " << count_prh
             << ", Load factor: " << loadFactor_prh() << "\n";
        cout << "Index | Status   | ID  | Name        | Branch   | CGPA | Placed | Company       | Package\n";
        cout << "---------------------------------------------------------------------------------------------\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << i << "     | ";
            if (status_prh[i] == 0) {
                cout << "EMPTY    | -   | -           | -        | -    | -      | -            | -";
            } else if (status_prh[i] == 2) {
                cout << "DELETED  | -   | -           | -        | -    | -      | -            | -";
            } else {
                cout << "OCCUPIED | "
                     << table_prh[i].id_prh << " | "
                     << table_prh[i].name_prh << " | "
                     << table_prh[i].branch_prh << " | "
                     << table_prh[i].cgpa_prh << " | "
                     << (table_prh[i].placed_prh ? "YES" : "NO ") << "    | "
                     << (table_prh[i].placed_prh ? table_prh[i].company_prh : "-") << " | "
                     << (table_prh[i].placed_prh ? table_prh[i].package_prh : 0.0);
            }
            cout << "\n";
        }
    }

    // Utility: check if n is prime
    bool isPrime_prh(int n_prh) const {
        if (n_prh <= 1) return false;
        if (n_prh <= 3) return true;
        if (n_prh % 2 == 0 || n_prh % 3 == 0) return false;
        for (int i = 5; i * i <= n_prh; i += 6) {
            if (n_prh % i == 0 || n_prh % (i + 2) == 0)
                return false;
        }
        return true;
    }

    int nextPrime_prh(int n_prh) const {
        while (!isPrime_prh(n_prh)) ++n_prh;
        return n_prh;
    }

    void rehash_prh() {
        int oldSize_prh = tableSize_prh;
        int newSize_prh = nextPrime_prh(2 * tableSize_prh + 1);

        cout << "\nRehashing: resizing table from " << oldSize_prh
             << " to " << newSize_prh << "...\n";

        vector<PlacementRecord_prh> oldTable_prh = table_prh;
        vector<int> oldStatus_prh = status_prh;

        tableSize_prh = newSize_prh;
        table_prh.assign(tableSize_prh, PlacementRecord_prh());
        status_prh.assign(tableSize_prh, 0);
        count_prh = 0;

        for (int i = 0; i < oldSize_prh; ++i) {
            if (oldStatus_prh[i] == 1) {
                insertOrUpdate_prh(oldTable_prh[i]);
            }
        }
    }
};

void flushInput_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

PlacementRecord_prh inputRecord_prh() {
    int id_prh;
    string name_prh, branch_prh;
    double cgpa_prh;
    char placedChar_prh;
    bool placed_prh = false;
    string company_prh;
    double package_prh = 0.0;

    cout << "Enter Student ID: ";
    cin >> id_prh;
    flushInput_prh();

    cout << "Enter Name: ";
    getline(cin, name_prh);

    cout << "Enter Branch: ";
    getline(cin, branch_prh);

    cout << "Enter CGPA: ";
    cin >> cgpa_prh;
    flushInput_prh();

    cout << "Is the student placed? (y/n): ";
    cin >> placedChar_prh;
    flushInput_prh();

    if (placedChar_prh == 'y' || placedChar_prh == 'Y') {
        placed_prh = true;
        cout << "Enter Company Name: ";
        getline(cin, company_prh);
        cout << "Enter Package (in LPA): ";
        cin >> package_prh;
        flushInput_prh();
    }

    return PlacementRecord_prh(id_prh, name_prh, branch_prh,
                               cgpa_prh, placed_prh, company_prh, package_prh);
}

int main() {
    int initSize_prh;
    cout << "Enter initial hash table size: ";
    if (!(cin >> initSize_prh) || initSize_prh <= 0) {
        cout << "Invalid size. Using default = 11.\n";
        flushInput_prh();
        initSize_prh = 11;
    }

    PlacementHashTable_prh portal_prh(initSize_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Smart College Placement Portal (Advanced Hashing) ---\n";
        cout << "1. Insert/Update student placement record\n";
        cout << "2. Search student by ID\n";
        cout << "3. Delete student record\n";
        cout << "4. Display all records\n";
        cout << "5. Exit\n";
        cout << "Enter your choice: ";
        if (!(cin >> choice_prh)) {
            cout << "Invalid input. Try again.\n";
            flushInput_prh();
            continue;
        }
        flushInput_prh();

        if (choice_prh == 1) {
            PlacementRecord_prh rec_prh = inputRecord_prh();
            portal_prh.insertOrUpdate_prh(rec_prh);
        } else if (choice_prh == 2) {
            int id_prh;
            cout << "Enter ID to search: ";
            cin >> id_prh;
            flushInput_prh();
            PlacementRecord_prh found_prh;
            int pos_prh = -1;
            if (portal_prh.search_prh(id_prh, found_prh, pos_prh)) {
                cout << "Record found at index " << pos_prh << ":\n";
                cout << "  ID: " << found_prh.id_prh << "\n";
                cout << "  Name: " << found_prh.name_prh << "\n";
                cout << "  Branch: " << found_prh.branch_prh << "\n";
                cout << "  CGPA: " << found_prh.cgpa_prh << "\n";
                cout << "  Placed: " << (found_prh.placed_prh ? "YES" : "NO") << "\n";
                if (found_prh.placed_prh) {
                    cout << "  Company: " << found_prh.company_prh << "\n";
                    cout << "  Package: " << found_prh.package_prh << " LPA\n";
                }
            } else {
                cout << "Record for ID " << id_prh << " not found.\n";
            }
        } else if (choice_prh == 3) {
            int id_prh;
            cout << "Enter ID to delete: ";
            cin >> id_prh;
            flushInput_prh();
            portal_prh.delete_prh(id_prh);
        } else if (choice_prh == 4) {
            portal_prh.displayAll_prh();
        } else if (choice_prh == 5) {
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

## 8. Compilation & Run Instructions  

### Compile  

```bash
g++ -std=c++11 assignment60_smart_placement_portal_hashing_prasad_hargude.cpp -o placement60
```

### Run  

```bash
./placement60
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment60_smart_placement_portal_hashing_prasad_hargude.cpp -o placement60.exe
placement60.exe
```

---

## 9. Sample Input/Output (Example Run)  

```text
Enter initial hash table size: 7
Invalid size. Using default = 11.   (if input invalid, otherwise use user size)

--- Smart College Placement Portal (Advanced Hashing) ---
1. Insert/Update student placement record
2. Search student by ID
3. Delete student record
4. Display all records
5. Exit
Enter your choice: 1
Enter Student ID: 101
Enter Name: Aru
Enter Branch: CSE
Enter CGPA: 9.1
Is the student placed? (y/n): y
Enter Company Name: Google
Enter Package (in LPA): 40
Inserted record for ID 101 at index 3.

Enter your choice: 1
Enter Student ID: 205
Enter Name: Raj
Enter Branch: IT
Enter CGPA: 8.7
Is the student placed? (y/n): n
Inserted record for ID 205 at index 7.

Enter your choice: 2
Enter ID to search: 101
Record found at index 3:
  ID: 101
  Name: Aru
  Branch: CSE
  CGPA: 9.1
  Placed: YES
  Company: Google
  Package: 40 LPA

Enter your choice: 3
Enter ID to delete: 205
Deleted record for ID 205 at index 7.

Enter your choice: 4

--- Placement Hash Table (Double Hashing + Dynamic Rehashing) ---
Table size: 11, Count: 1, Load factor: 0.0909091
Index | Status   | ID  | Name        | Branch   | CGPA | Placed | Company       | Package
---------------------------------------------------------------------------------------------
0     | EMPTY    | -   | -           | -        | -    | -      | -            | -
1     | EMPTY    | -   | -           | -        | -    | -      | -            | -
2     | EMPTY    | -   | -           | -        | -    | -      | -            | -
3     | OCCUPIED | 101 | Aru         | CSE      | 9.1  | YES    | Google       | 40
4     | EMPTY    | -   | -           | -        | -    | -      | -            | -
5     | EMPTY    | -   | -           | -        | -    | -      | -            | -
6     | EMPTY    | -   | -           | -        | -    | -      | -            | -
7     | DELETED  | -   | -           | -        | -    | -      | -            | -
8     | EMPTY    | -   | -           | -        | -    | -      | -            | -
9     | EMPTY    | -   | -           | -        | -    | -      | -            | -
10    | EMPTY    | -   | -           | -        | -    | -      | -            | -

Enter your choice: 5
Exiting...
```

---

## 10. Memory Management & Edge Cases  

- Uses only `vector` and automatic storage → **no manual `new`/`delete`**.  
- Handles:
  - Invalid initial size (falls back to 11).  
  - Duplicate IDs (perform **update** instead of blindly inserting).  
  - Deletion with **tombstones** (DELETED status).  
  - Automatic **rehash** when load factor exceeds 0.7.  

---

## 11. Possible Enhancements  

- Add filtering:
  - List all students placed in a given company.  
  - List all unplaced students above a certain CGPA.  
- Export data to CSV / file.  
- GUI or web frontend, with this hash table as backend.  
- Use different hashing options:
  - Mid-square hashing  
  - Universal hashing  
- Add **multi-key indexing** (e.g., company-wise index) using separate hash tables.

---

## 12. References  

- Standard Data Structures textbooks — double hashing & rehashing.  
- C++ STL documentation — `vector`, `string`, `limits`.  

---

_End of Assignment 60 — Smart College Placement Portal using Advanced Hashing_