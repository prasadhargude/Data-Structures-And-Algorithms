# Assignment 52: Hash Table with Collision Handling using Separate Chaining  
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

Implement a **hash table** in C++ to store **integer keys**, using **separate chaining** to handle collisions.

The hash table must support:

1. **Insert** a key  
2. **Search** for a key  
3. **Delete** a key  
4. **Display** the entire hash table (showing all chains in each bucket)

Use a simple hash function:

\[
h(k) = k \bmod \text{TABLE\_SIZE}
\]

All identifiers should follow your style with `_prh` suffix.

---

## 2. System Requirements

- **Language:** C++  
- **Standard:** C++11 or later  
- **Compiler:** Any standard compiler (e.g., `g++`)  
- **Data Structure:**  
  - Array (or `vector`) of **linked lists** (here implemented using `std::list<int>`).  
- **Interface:** Console-based, menu-driven program.  

---

## 3. Data Structure & Design

### 3.1 Concept of Separate Chaining

- The hash table is an array of **buckets**.  
- Each bucket is a **linked list** (chain) of keys that hash to the same index.  
- For a key `k`, the bucket index is:

```cpp
index = k % tableSize_prh;
```

- All keys with the same index are stored in the corresponding list.

### 3.2 Class Structure

```cpp
class HashTableChain_prh {
    int tableSize_prh;
    vector<list<int>> table_prh;

public:
    HashTableChain_prh(int size_prh);
    int hashFunction_prh(int key_prh) const;
    void insertKey_prh(int key_prh);
    bool searchKey_prh(int key_prh, int &bucket_prh) const;
    bool deleteKey_prh(int key_prh);
    void displayTable_prh() const;
};
```

We use:

- `vector<list<int>> table_prh;`  
  where `table_prh[i]` is the chain at index `i`.

---

## 4. Algorithms (Pseudocode + Complexity)

### 4.1 Hash Function

```text
hashFunction(key):
    if key < 0: key = -key
    return key % tableSize
```

Time: **O(1)**.

---

### 4.2 Insert (Separate Chaining)

```text
insert(key):
    index = hashFunction(key)
    if key is already present in table[index]:
        do not insert duplicate
        return
    push_back key into table[index] list
```

- **Average Time:** O(1) (assuming small chains, good distribution)  
- **Worst Time:** O(n) if all keys go into same bucket.

---

### 4.3 Search

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

### 4.4 Delete

```text
delete(key):
    index = hashFunction(key)
    for each element x in table[index]:
        if x == key:
            erase x from the list
            return true
    return false
```

- Uses list erase operation — efficient when iterator is known.

---

## 5. Complete C++ Program (Separate Chaining, Menu-Driven)

```cpp
// assignment52_hash_separate_chaining_prasad_hargude.cpp
// Implement a hash table with collision handling using separate chaining.
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <list>
#include <limits>
using namespace std;

class HashTableChain_prh {
private:
    int tableSize_prh;
    vector<list<int>> table_prh;

public:
    HashTableChain_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, list<int>());
    }

    int hashFunction_prh(int key_prh) const {
        if (key_prh < 0) key_prh = -key_prh;
        return key_prh % tableSize_prh;
    }

    void insertKey_prh(int key_prh) {
        int index_prh = hashFunction_prh(key_prh);
        // Avoid duplicates
        for (int x_prh : table_prh[index_prh]) {
            if (x_prh == key_prh) {
                cout << "Key " << key_prh << " already exists in bucket " << index_prh << ".\n";
                return;
            }
        }
        table_prh[index_prh].push_back(key_prh);
        cout << "Inserted key " << key_prh << " into bucket " << index_prh << ".\n";
    }

    bool searchKey_prh(int key_prh, int &bucket_prh) const {
        int index_prh = hashFunction_prh(key_prh);
        for (int x_prh : table_prh[index_prh]) {
            if (x_prh == key_prh) {
                bucket_prh = index_prh;
                return true;
            }
        }
        return false;
    }

    bool deleteKey_prh(int key_prh) {
        int index_prh = hashFunction_prh(key_prh);
        auto &chain_prh = table_prh[index_prh];
        for (auto it_prh = chain_prh.begin(); it_prh != chain_prh.end(); ++it_prh) {
            if (*it_prh == key_prh) {
                chain_prh.erase(it_prh);
                return true;
            }
        }
        return false;
    }

    void displayTable_prh() const {
        cout << "\nCurrent Hash Table (Separate Chaining):\n";
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

    HashTableChain_prh ht_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Hash Table Menu (Separate Chaining) ---\n";
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
g++ -std=c++11 assignment52_hash_separate_chaining_prasad_hargude.cpp -o hash52
```

### Run

```bash
./hash52
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment52_hash_separate_chaining_prasad_hargude.cpp -o hash52.exe
hash52.exe
```

---

## 7. Sample Input/Output (Example Runs)

### Example 1: Insert & Display

```text
Enter hash table size: 5

--- Hash Table Menu (Separate Chaining) ---
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

Current Hash Table (Separate Chaining):
0 : 10 -> 15 -> NULL
1 : EMPTY
2 : 7 -> NULL
3 : EMPTY
4 : EMPTY
```

### Example 2: Search & Delete

```text
Enter your choice: 2
Enter key to search: 15
Key 15 found in bucket 0.

Enter your choice: 3
Enter key to delete: 15
Key 15 deleted from hash table.

Enter your choice: 4

Current Hash Table (Separate Chaining):
0 : 10 -> NULL
1 : EMPTY
2 : 7 -> NULL
3 : EMPTY
4 : EMPTY
```

---

## 8. Memory Management & Edge Cases

- Uses `vector<list<int>>`, which automatically handles dynamic memory.  
- Handles:
  - Duplicate key insertion (prints message, does not insert).  
  - Search/Delete on non-existent keys.  
  - Negative keys (converted to positive for hashing).  
  - Custom table size entered by user.

---

## 9. Possible Enhancements

- Store **(key, value)** pairs instead of only integer keys.  
- Collect statistics such as **load factor** and **average chain length**.  
- Implement **rehashing** when load factor exceeds a threshold.  
- Use `forward_list` or custom linked list implementation for educational purposes.

---

## 10. References

- Data Structures textbooks — Hashing with Separate Chaining  
- C++ STL (`<vector>`, `<list>`, `<limits>`)  

---

_End of Assignment 52 — Hash Table with Separate Chaining_
