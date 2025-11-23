# Assignment 53: Hash Table – Collision Resolution using Linked Lists  
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
7. Sample Input/Output  
8. Memory Management & Edge Cases  
9. Possible Enhancements  
10. References  

---

## 1. Problem Statement

Implement a **hash table** in C++ that stores **integer keys** and resolves collisions using **linked lists**.

When two or more keys hash to the same index (collision), they should be stored in a **linked list (chain)** at that index.

The program must support the following operations:

1. **Insert** a key into the hash table  
2. **Search** for a key  
3. **Delete** a key  
4. **Display** the content of the hash table (showing all linked lists)

The collision resolution method is **separate chaining using linked lists**.

All key variables and function names should follow the `_prh` suffix style.

---

## 2. System Requirements

- **Language:** C++  
- **Standard:** C++11 or later  
- **Compiler:** Any standard C++ compiler (`g++`, MinGW, etc.)  
- **Data Structure:**  
  - Array (or `vector`) of **linked lists** (chains)  
- **User Interface:**  
  - Console-based  
  - Menu-driven  

---

## 3. Data Structure & Design

We represent the hash table as:

```cpp
vector< list<int> > table_prh;
```

- `tableSize_prh` — size of the hash table  
- `table_prh[i]` — a **linked list** containing all keys whose hash index is `i`.

### 3.1 Hash Function

For a key `k`:

\[
h(k) = |k| \bmod \text{tableSize\_prh}
\]

We take the absolute value so that negative keys are also handled.

### 3.2 Class Design

```cpp
class HashTableList_prh {
    int tableSize_prh;
    vector< list<int> > table_prh;

public:
    HashTableList_prh(int size_prh = 10);
    int hashFunction_prh(int key_prh) const;
    void insertKey_prh(int key_prh);
    bool searchKey_prh(int key_prh, int &bucketIndex_prh) const;
    bool deleteKey_prh(int key_prh);
    void displayTable_prh() const;
};
```

---

## 4. Algorithms (Pseudocode + Complexity)

### 4.1 Insert Using Linked List (Separate Chaining)

```text
insert(key):
    index = hashFunction(key)
    for each element x in table[index]:
        if x == key:
            // avoid duplicate keys
            return
    append key to linked list table[index]
```

- **Average Time:** O(1)  
- **Worst Time:** O(n) if all keys go into the same bucket  

---

### 4.2 Search in Hash Table with Linked Lists

```text
search(key):
    index = hashFunction(key)
    for each element x in table[index]:
        if x == key:
            return true
    return false
```

- **Average Time:** O(1)  
- **Worst Time:** O(n)  

---

### 4.3 Delete from Hash Table with Linked Lists

```text
delete(key):
    index = hashFunction(key)
    for each element x in table[index]:
        if x == key:
            remove x from linked list table[index]
            return true
    return false
```

This uses linked list operations to remove the node containing the key.

---

## 5. Complete C++ Program (Menu-Driven)

```cpp
// assignment53_hash_linkedlist_collision_prasad_hargude.cpp
// Implement collision resolution using linked lists (separate chaining).
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <list>
#include <limits>
using namespace std;

class HashTableList_prh {
private:
    int tableSize_prh;
    vector< list<int> > table_prh;

public:
    HashTableList_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, list<int>());
    }

    int hashFunction_prh(int key_prh) const {
        if (key_prh < 0) key_prh = -key_prh;
        return key_prh % tableSize_prh;
    }

    void insertKey_prh(int key_prh) {
        int index_prh = hashFunction_prh(key_prh);
        // check if already present
        for (int x_prh : table_prh[index_prh]) {
            if (x_prh == key_prh) {
                cout << "Key " << key_prh << " already exists in bucket " << index_prh << ".\n";
                return;
            }
        }
        table_prh[index_prh].push_back(key_prh);
        cout << "Inserted key " << key_prh << " into bucket " << index_prh << ".\n";
    }

    bool searchKey_prh(int key_prh, int &bucketIndex_prh) const {
        int index_prh = hashFunction_prh(key_prh);
        for (int x_prh : table_prh[index_prh]) {
            if (x_prh == key_prh) {
                bucketIndex_prh = index_prh;
                return true;
            }
        }
        return false;
    }

    bool deleteKey_prh(int key_prh) {
        int index_prh = hashFunction_prh(key_prh);
        list<int> &chain_prh = table_prh[index_prh];
        for (auto it_prh = chain_prh.begin(); it_prh != chain_prh.end(); ++it_prh) {
            if (*it_prh == key_prh) {
                chain_prh.erase(it_prh);
                return true;
            }
        }
        return false;
    }

    void displayTable_prh() const {
        cout << "\nCurrent Hash Table (Collision Resolution using Linked Lists):\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << i << " : ";
            if (table_prh[i].empty()) {
                cout << "EMPTY";
            } else {
                for (int x_prh : table_prh[i]) {
                    cout << x_prh << " -> ";
                }
                cout << "NULL";
            }
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

    HashTableList_prh ht_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Hash Table Menu (Linked List Collision Handling) ---\n";
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
            int bucket_prh = -1;
            if (ht_prh.searchKey_prh(key_prh, bucket_prh)) {
                cout << "Key " << key_prh << " found in bucket " << bucket_prh << ".\n";
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
                cout << "Key " << key_prh << " deleted from hash table.\n";
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
g++ -std=c++11 assignment53_hash_linkedlist_collision_prasad_hargude.cpp -o hash53
```

### Run

```bash
./hash53
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment53_hash_linkedlist_collision_prasad_hargude.cpp -o hash53.exe
hash53.exe
```

---

## 7. Sample Input/Output

### Example Session

```text
Enter hash table size: 5

--- Hash Table Menu (Linked List Collision Handling) ---
1. Insert key
2. Search key
3. Delete key
4. Display table
5. Exit
Enter your choice: 1
Enter key to insert: 10
Inserted key 10 into bucket 0.

Enter your choice: 1
Enter key to insert: 15
Inserted key 15 into bucket 0.

Enter your choice: 1
Enter key to insert: 7
Inserted key 7 into bucket 2.

Enter your choice: 4

Current Hash Table (Collision Resolution using Linked Lists):
0 : 10 -> 15 -> NULL
1 : EMPTY
2 : 7 -> NULL
3 : EMPTY
4 : EMPTY

Enter your choice: 2
Enter key to search: 15
Key 15 found in bucket 0.

Enter your choice: 3
Enter key to delete: 15
Key 15 deleted from hash table.

Enter your choice: 4

Current Hash Table (Collision Resolution using Linked Lists):
0 : 10 -> NULL
1 : EMPTY
2 : 7 -> NULL
3 : EMPTY
4 : EMPTY

Enter your choice: 5
Exiting...
```

---

## 8. Memory Management & Edge Cases

- Uses `vector<list<int>>` so memory is managed automatically.  
- Handles:
  - Invalid table size (defaults to 10).  
  - Duplicate insertions (ignored with a message).  
  - Searching/deleting keys that are not present.  
  - Negative keys are converted to positive for hashing.  

---

## 9. Possible Enhancements

- Change the node structure to store `(key, value)` pairs instead of plain integers.  
- Provide statistics: number of elements per bucket, maximum chain length, load factor.  
- Implement rehashing to a larger table when chains grow too long.  
- Use your own manual linked list class instead of `std::list` for practice.

---

## 10. References

- Hashing & collision resolution (separate chaining) from standard DSA textbooks.  
- C++ STL documentation for `vector`, `list`, and `limits`.  

---

_End of Assignment 53 — Collision Resolution using Linked Lists (Separate Chaining)_
