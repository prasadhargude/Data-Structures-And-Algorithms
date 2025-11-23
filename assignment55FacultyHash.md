# Assignment 55: Faculty Database using Hash Table with Linear Probing  
**Author:** Prasad Ramdas Hargude  
**Unit:** Hashing  

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

- Use **MOD** as the hash function.  
- Use **linear probing** as the collision handling technique.  
- Assume suitable data fields for each **faculty record**.  
- Allow the user to **search a particular faculty** efficiently using the hash table.

The program should support at least the following operations:

1. Insert a new **faculty record** into the hash table.  
2. Search for a faculty using the **faculty ID** (or code).  
3. Delete a faculty record.  
4. Display the full hash table.

All identifiers should use the `_prh` suffix.

---

## 2. System Requirements

- **Language:** C++  
- **Standard:** C++11 or later  
- **Compiler:** `g++` / MinGW / any modern C++ compiler  
- **Data Structure:**  
  - Hash table implemented using an **array/vector** with **open addressing (linear probing)**  
- **Interface:**  
  - Console-based, menu-driven  

---

## 3. Data Structure & Design

### 3.1 Faculty Record

Each faculty record will contain:

- `id_prh` — Unique Faculty ID (int, hash key)  
- `name_prh` — Faculty name (string)  
- `dept_prh` — Department (string)  
- `designation_prh` — Designation (e.g., Asst Prof, Prof)  

```cpp
struct Faculty_prh {
    int id_prh;
    string name_prh;
    string dept_prh;
    string designation_prh;

    Faculty_prh(int id = -1, string nm = "", string dp = "", string desig = "")
        : id_prh(id), name_prh(nm), dept_prh(dp), designation_prh(desig) {}
};
```

### 3.2 Hash Table Representation

We will use:

```cpp
vector<Faculty_prh> table_prh;
vector<int> status_prh; // 0 = EMPTY, 1 = OCCUPIED, 2 = DELETED
int tableSize_prh;
```

- `table_prh[i]` stores the faculty record at index `i`.  
- `status_prh[i]` stores the state of that slot:
  - `0` → **EMPTY**  
  - `1` → **OCCUPIED**  
  - `2` → **DELETED**  

### 3.3 Hash Function (MOD)

For a faculty ID `id_prh`:

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

Time: **O(1)**.

---

### 4.2 Insert Faculty (Linear Probing)

**Goal:** Insert faculty record; ID must be unique.

```text
insertFaculty(record):
    index = hashFunction(record.id)
    for i from 0 to tableSize - 1:
        pos = (index + i) % tableSize
        if status[pos] == EMPTY or status[pos] == DELETED:
            table[pos] = record
            status[pos] = OCCUPIED
            return true
        else if status[pos] == OCCUPIED and table[pos].id == record.id:
            // duplicate ID
            return false
    // table full
    return false
```

- **Best:** O(1)  
- **Worst:** O(tableSize)  

---

### 4.3 Search Faculty by ID

```text
searchFaculty(id):
    index = hashFunction(id)
    for i from 0 to tableSize - 1:
        pos = (index + i) % tableSize
        if status[pos] == EMPTY:
            return NOT FOUND
        if status[pos] == OCCUPIED and table[pos].id == id:
            return FOUND at pos
    return NOT FOUND
```

- **Average:** O(1) (low load factor)  
- **Worst:** O(tableSize)  

---

### 4.4 Delete Faculty by ID

```text
deleteFaculty(id):
    pos = searchFaculty(id)
    if pos not found:
        return false
    status[pos] = DELETED
    return true
```

We mark slot as **DELETED** to keep probe chains valid.

---

## 5. Complete C++ Program (Menu-Driven)

```cpp
// assignment55_faculty_hash_linear_probing_prasad_hargude.cpp
// Simulate a faculty database using hash table (MOD hash + linear probing)
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

    Faculty_prh(int id = -1, string nm = "", string dp = "", string desig = "")
        : id_prh(id), name_prh(nm), dept_prh(dp), designation_prh(desig) {}
};

class FacultyHashTable_prh {
private:
    int tableSize_prh;
    vector<Faculty_prh> table_prh;
    vector<int> status_prh; // 0 = EMPTY, 1 = OCCUPIED, 2 = DELETED

public:
    FacultyHashTable_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, Faculty_prh());
        status_prh.assign(tableSize_prh, 0);
    }

    int hashFunction_prh(int id_prh) const {
        if (id_prh < 0) id_prh = -id_prh;
        return id_prh % tableSize_prh; // MOD hash function
    }

    bool insertFaculty_prh(const Faculty_prh &f_prh) {
        int index_prh = hashFunction_prh(f_prh.id_prh);
        for (int i = 0; i < tableSize_prh; ++i) {
            int pos_prh = (index_prh + i) % tableSize_prh;
            if (status_prh[pos_prh] == 0 || status_prh[pos_prh] == 2) {
                table_prh[pos_prh] = f_prh;
                status_prh[pos_prh] = 1;
                cout << "Inserted faculty with ID " << f_prh.id_prh
                     << " at index " << pos_prh << ".\n";
                return true;
            } else if (status_prh[pos_prh] == 1 && table_prh[pos_prh].id_prh == f_prh.id_prh) {
                cout << "Faculty with ID " << f_prh.id_prh
                     << " already exists at index " << pos_prh << ".\n";
                return false;
            }
        }
        cout << "Hash table is full. Cannot insert more faculty records.\n";
        return false;
    }

    bool searchFaculty_prh(int id_prh, int &pos_prh) const {
        int index_prh = hashFunction_prh(id_prh);
        for (int i = 0; i < tableSize_prh; ++i) {
            int p_prh = (index_prh + i) % tableSize_prh;
            if (status_prh[p_prh] == 0) {
                return false; // empty slot found -> not in table
            }
            if (status_prh[p_prh] == 1 && table_prh[p_prh].id_prh == id_prh) {
                pos_prh = p_prh;
                return true;
            }
        }
        return false;
    }

    bool deleteFaculty_prh(int id_prh) {
        int pos_prh;
        if (searchFaculty_prh(id_prh, pos_prh)) {
            status_prh[pos_prh] = 2; // mark as DELETED
            cout << "Faculty with ID " << id_prh << " deleted (marked DELETED) at index "
                 << pos_prh << ".\n";
            return true;
        }
        return false;
    }

    void displayTable_prh() const {
        cout << "\n--- Faculty Hash Table (Linear Probing) ---\n";
        cout << "Index : Status    ID     Name         Dept        Designation\n";
        cout << "----------------------------------------------------------------\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << i << " : ";
            if (status_prh[i] == 0) {
                cout << "EMPTY";
            } else if (status_prh[i] == 2) {
                cout << "DELETED";
            } else {
                cout << "OCCUPIED  "
                     << table_prh[i].id_prh << "  "
                     << table_prh[i].name_prh << "  "
                     << table_prh[i].dept_prh << "  "
                     << table_prh[i].designation_prh;
            }
            cout << "\n";
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

    return Faculty_prh(id_prh, name_prh, dept_prh, desig_prh);
}

int main() {
    int size_prh;
    cout << "Enter hash table size: ";
    if (!(cin >> size_prh) || size_prh <= 0) {
        cout << "Invalid size. Using default size = 10.\n";
        flushInput_prh();
        size_prh = 10;
    }

    FacultyHashTable_prh ht_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Faculty Database (Hash Table + Linear Probing) ---\n";
        cout << "1. Insert faculty record\n";
        cout << "2. Search faculty by ID\n";
        cout << "3. Delete faculty by ID\n";
        cout << "4. Display hash table\n";
        cout << "5. Exit\n";
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
            int id_prh;
            cout << "Enter Faculty ID to delete: ";
            cin >> id_prh;
            flushInput_prh();
            if (!ht_prh.deleteFaculty_prh(id_prh)) {
                cout << "Faculty with ID " << id_prh << " not found.\n";
            }
        } else if (choice_prh == 4) {
            ht_prh.displayTable_prh();
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

## 6. Compilation & Run Instructions

### Compile

```bash
g++ -std=c++11 assignment55_faculty_hash_linear_probing_prasad_hargude.cpp -o faculty55
```

### Run

```bash
./faculty55
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment55_faculty_hash_linear_probing_prasad_hargude.cpp -o faculty55.exe
faculty55.exe
```

---

## 7. Sample Input/Output (Example Run)

```text
Enter hash table size: 7

--- Faculty Database (Hash Table + Linear Probing) ---
1. Insert faculty record
2. Search faculty by ID
3. Delete faculty by ID
4. Display hash table
5. Exit
Enter your choice: 1
Enter Faculty ID: 101
Enter Name: Aru
Enter Department: CSE
Enter Designation: Assistant_Professor
Inserted faculty with ID 101 at index 3.

Enter your choice: 1
Enter Faculty ID: 108
Enter Name: Purva
Enter Department: MED
Enter Designation: Professor
Inserted faculty with ID 108 at index 4.

Enter your choice: 4

--- Faculty Hash Table (Linear Probing) ---
Index : Status    ID     Name         Dept        Designation
----------------------------------------------------------------
0 : EMPTY
1 : EMPTY
2 : EMPTY
3 : OCCUPIED  101  Aru  CSE  Assistant_Professor
4 : OCCUPIED  108  Purva  MED  Professor
5 : EMPTY
6 : EMPTY

Enter your choice: 2
Enter Faculty ID to search: 108
Faculty with ID 108 found at index 4.

Enter your choice: 3
Enter Faculty ID to delete: 101
Faculty with ID 101 deleted (marked DELETED) at index 3.

Enter your choice: 4

--- Faculty Hash Table (Linear Probing) ---
Index : Status    ID     Name         Dept        Designation
----------------------------------------------------------------
0 : EMPTY
1 : EMPTY
2 : EMPTY
3 : DELETED
4 : OCCUPIED  108  Purva  MED  Professor
5 : EMPTY
6 : EMPTY

Enter your choice: 5
Exiting...
```

*(Exact indices depend on table size and hash function.)*

---

## 8. Memory Management & Edge Cases

- Uses only `vector` and simple structs — **no manual `new`/`delete`** required.  
- Handles:
  - Invalid hash table size → falls back to default size 10.  
  - Duplicate faculty IDs → insertion rejected with a message.  
  - Searching and deleting non-existent IDs.  
  - Negative IDs handled by taking absolute values for hashing.  

---

## 9. Possible Enhancements

- Add more attributes like phone number, email, specialization.  
- Use **separate chaining** when load factor becomes too high.  
- Provide functionality to **update** existing faculty details.  
- Integrate with file handling to store and load records from disk.  

---

## 10. References

- Data Structures textbooks — Hashing & Linear Probing  
- C++ STL documentation: `vector`, `string`, `limits`  

---

_End of Assignment 55 — Faculty Database using Hash Table (MOD + Linear Probing)_
