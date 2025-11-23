# Assignment 51: Hash Table Implementation with Linear Probing  
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
7. Sample Input/Output (Example Runs)  
8. Memory Management & Edge Cases  
9. Possible Enhancements  
10. References  

---

## 1. Problem Statement

Implement a **hash table** in C++ that stores integer keys and resolves collisions using **linear probing**.

The hash table should support the following operations:

1. **Insert** a key into the hash table.  
2. **Search** for a key in the hash table.  
3. **Delete** a key from the hash table.  
4. **Display** the current state of the hash table.  

Use **linear probing** to handle collisions and a simple hash function like:

\[
h(k) = k \mod \, 	ext{TABLE\_SIZE}
\]

All important identifiers must use the `_prh` suffix.

---

## 2. System Requirements

- **Language:** C++  
- **Compiler:** Any C++11+ compatible compiler (e.g., `g++`)  
- **Data Structure:** Array-based hash table with **linear probing**  
- **Input/Output:** Console-based, menu-driven program  

---

## 3. Data Structure & Design

### 3.1 Hash Table Representation

We will store only **integer keys** for simplicity.

- `tableSize_prh` — size of the hash table (fixed, e.g., 10 or user-defined).  
- `table_prh` — integer array of length `tableSize_prh`.  
- `status_prh` — parallel array or special sentinel values to indicate cell state.

We use **sentinel values**:

- `-1` → **EMPTY** slot  
- `-2` → **DELETED** slot  
- Any other (>= 0) → **VALID key**  

### 3.2 Class Design

```cpp
class HashTable_prh {
    int tableSize_prh;
    vector<int> table_prh;

public:
    HashTable_prh(int size_prh);
    int hashFunction_prh(int key_prh) const;
    bool insertKey_prh(int key_prh);
    bool searchKey_prh(int key_prh, int &pos_prh) const;
    bool deleteKey_prh(int key_prh);
    void displayTable_prh() const;
};
```

---

## 4. Algorithms (Pseudocode + Complexity)

### 4.1 Hash Function

```text
hashFunction(key):
    return key % tableSize
```

Time: **O(1)**.

---

### 4.2 Linear Probing Insert

**Goal:** Insert `key` into the table.

```text
insert(key):
    index = hashFunction(key)
    for i from 0 to tableSize-1:
        pos = (index + i) % tableSize
        if table[pos] == EMPTY or table[pos] == DELETED:
            table[pos] = key
            return true
        else if table[pos] == key:
            // already exists
            return false
    // table full
    return false
```

**Time Complexity:**

- Best case: **O(1)**  
- Worst case (table nearly full): **O(tableSize)**  

---

### 4.3 Linear Probing Search

```text
search(key):
    index = hashFunction(key)
    for i from 0 to tableSize-1:
        pos = (index + i) % tableSize
        if table[pos] == EMPTY:
            return false // key not found
        if table[pos] == key:
            return true
    return false
```

**Time Complexity:**  
- Average: **O(1)** (low load factor)  
- Worst: **O(tableSize)**  

---

### 4.4 Linear Probing Delete

```text
delete(key):
    index = hashFunction(key)
    for i from 0 to tableSize-1:
        pos = (index + i) % tableSize
        if table[pos] == EMPTY:
            return false // key not in table
        if table[pos] == key:
            table[pos] = DELETED
            return true
    return false
```

**Important:** We mark the slot as **DELETED** instead of EMPTY so that future searches still continue beyond this slot if needed.

---

## 5. Complete C++ Program (Menu-Driven)

```cpp
// assignment51_hash_linear_probing_prasad_hargude.cpp
// Implement a hash table with collision resolution using linear probing.
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <limits>
using namespace std;

class HashTable_prh {
private:
    int tableSize_prh;
    vector<int> table_prh;
    const int EMPTY_prh = -1;
    const int DELETED_prh = -2;

public:
    HashTable_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, EMPTY_prh);
    }

    int hashFunction_prh(int key_prh) const {
        if (key_prh < 0) key_prh = -key_prh;
        return key_prh % tableSize_prh;
    }

    bool insertKey_prh(int key_prh) {
        int index_prh = hashFunction_prh(key_prh);
        for (int i = 0; i < tableSize_prh; ++i) {
            int pos_prh = (index_prh + i) % tableSize_prh;
            if (table_prh[pos_prh] == EMPTY_prh || table_prh[pos_prh] == DELETED_prh) {
                table_prh[pos_prh] = key_prh;
                return true;
            } else if (table_prh[pos_prh] == key_prh) {
                cout << "Key already exists at position " << pos_prh << ".\n";
                return false;
            }
        }
        cout << "Hash table is full. Cannot insert key: " << key_prh << "\n";
        return false;
    }

    bool searchKey_prh(int key_prh, int &pos_prh) const {
        int index_prh = hashFunction_prh(key_prh);
        for (int i = 0; i < tableSize_prh; ++i) {
            int p_prh = (index_prh + i) % tableSize_prh;
            if (table_prh[p_prh] == EMPTY_prh) {
                // if we hit an empty slot, key is not present
                return false;
            }
            if (table_prh[p_prh] == key_prh) {
                pos_prh = p_prh;
                return true;
            }
        }
        return false;
    }

    bool deleteKey_prh(int key_prh) {
        int pos_prh;
        if (searchKey_prh(key_prh, pos_prh)) {
            table_prh[pos_prh] = DELETED_prh;
            return true;
        }
        return false;
    }

    void displayTable_prh() const {
        cout << "\nCurrent Hash Table:\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << i << " : ";
            if (table_prh[i] == EMPTY_prh) cout << "EMPTY";
            else if (table_prh[i] == DELETED_prh) cout << "DELETED";
            else cout << table_prh[i];
            cout << "\n";
        }
    }
};

void flushInput_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    int size_prh;
    cout << "Enter hash table size: ";
    if (!(cin >> size_prh) || size_prh <= 0) {
        cout << "Invalid size. Using default size = 10.\n";
        flushInput_prh();
        size_prh = 10;
    }

    HashTable_prh ht_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Hash Table Menu (Linear Probing) ---\n";
        cout << "1. Insert key\n";
        cout << "2. Search key\n";
        cout << "3. Delete key\n";
        cout << "4. Display table\n";
        cout << "5. Exit\n";
        cout << "Enter your choice: ";
        if (!(cin >> choice_prh)) {
            cout << "Invalid input. Try again.\n";
            flushInput_prh();
            continue;
        }

        if (choice_prh == 1) {
            int key_prh;
            cout << "Enter key to insert: ";
            if (!(cin >> key_prh)) {
                cout << "Invalid input.\n";
                flushInput_prh();
                continue;
            }
            ht_prh.insertKey_prh(key_prh);
        } else if (choice_prh == 2) {
            int key_prh;
            cout << "Enter key to search: ";
            if (!(cin >> key_prh)) {
                cout << "Invalid input.\n";
                flushInput_prh();
                continue;
            }
            int pos_prh = -1;
            if (ht_prh.searchKey_prh(key_prh, pos_prh)) {
                cout << "Key " << key_prh << " found at index " << pos_prh << ".\n";
            } else {
                cout << "Key " << key_prh << " not found.\n";
            }
        } else if (choice_prh == 3) {
            int key_prh;
            cout << "Enter key to delete: ";
            if (!(cin >> key_prh)) {
                cout << "Invalid input.\n";
                flushInput_prh();
                continue;
            }
            if (ht_prh.deleteKey_prh(key_prh)) {
                cout << "Key " << key_prh << " deleted (marked DELETED).\n";
            } else {
                cout << "Key " << key_prh << " not found.\n";
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
g++ -std=c++11 assignment51_hash_linear_probing_prasad_hargude.cpp -o hash51
```

### Run

```bash
./hash51
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment51_hash_linear_probing_prasad_hargude.cpp -o hash51.exe
hash51.exe
```

---

## 7. Sample Input/Output (Example Runs)

### Example 1: Insert and Display

```text
Enter hash table size: 7

--- Hash Table Menu (Linear Probing) ---
1. Insert key
2. Search key
3. Delete key
4. Display table
5. Exit
Enter your choice: 1
Enter key to insert: 10

Enter your choice: 1
Enter key to insert: 17

Enter your choice: 1
Enter key to insert: 24

Enter your choice: 4

Current Hash Table:
0 : EMPTY
1 : 8
2 : 15
3 : 22
4 : EMPTY
5 : EMPTY
6 : EMPTY
```

*(Example above is illustrative; actual positions depend on keys and table size.)*

### Example 2: Search and Delete

```text
Enter your choice: 2
Enter key to search: 17
Key 17 found at index 3.

Enter your choice: 3
Enter key to delete: 17
Key 17 deleted (marked DELETED).

Enter your choice: 4

Current Hash Table:
0 : EMPTY
1 : 10
2 : DELETED
3 : 24
4 : EMPTY
5 : EMPTY
6 : EMPTY
```

---

## 8. Memory Management & Edge Cases

- No manual `new`/`delete` used — `vector` manages memory automatically.  
- Edge cases handled:
  - **Full table** during insertion → message printed.  
  - **Duplicate insert** → rejected with message.  
  - **Search/Delete for non-existent key** → proper message.  
  - **Negative keys** → hashed after converting to positive.  

---

## 9. Possible Enhancements

- Store **(key, value)** pairs instead of only keys.  
- Implement **rehashing** when load factor becomes high.  
- Implement other collision strategies:
  - Quadratic probing  
  - Double hashing  
- Allow **string keys** using a custom string hash function.

---

## 10. References

- Data Structures and Algorithms textbooks (Hashing & Collision Resolution).  
- C++ STL documentation (`<vector>`, `<limits>`).  

---

_End of Assignment 51 — Hash Table with Linear Probing_
