# Assignment 59: Student Database Management using Hash Table (Insert, Search, Delete)  
**Author:** Prasad Ramdas Hargude  
**Unit:** VI — Hashing  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Hash Function (Divide Method)  
5. Algorithms (Pseudocode + Complexity)  
6. Complete C++ Program (Menu-Driven)  
7. Compilation & Run Instructions  
8. Sample Input/Output (Example Run)  
9. Memory Management & Edge Cases  
10. Possible Enhancements  
11. References  

---

## 1. Problem Statement

Write a C++ program to **simulate a student database as a hash table**.

Build a simple **Student Database Management System** using **hashing techniques** to allow efficient:

- **Insertion** of student records  
- **Search** of a student by roll number  
- **Deletion** of a student record  

You may assume suitable fields for the student record. Use a **hash table** with an appropriate hashing method and a suitable collision resolution strategy.  

This implementation uses:

- **Divide (MOD) method** as the hash function.  
- **Separate chaining** using **linked lists** (via `std::list`) for collision handling.  
- Roll number as the **primary key**.

All identifiers follow the `_prh` suffix style.

---

## 2. System Requirements

- **Language:** C++  
- **Standard:** C++11 or later  
- **Compiler:** `g++`, MinGW, or any modern C++ compiler  
- **Data Structure:**  
  - Hash table = `vector< list<Student_prh> >`  
  - Each bucket is a **linked list** of students whose roll numbers hash to the same index.  
- **Operations supported:**  
  - Insert student record  
  - Search student by roll number  
  - Delete student by roll number  
  - Display full hash table  

---

## 3. Data Structure & Design

### 3.1 Student Record Structure

Each **student record** contains:

- `roll_prh` — Unique roll number (int, used as key)  
- `name_prh` — Student name (string)  
- `dept_prh` — Department / branch (string)  
- `year_prh` — Year of study (int, e.g., 1, 2, 3, 4)  

```cpp
struct Student_prh {
    int roll_prh;
    string name_prh;
    string dept_prh;
    int year_prh;

    Student_prh(int r_prh = -1,
                const string &n_prh = "",
                const string &d_prh = "",
                int y_prh = 0)
        : roll_prh(r_prh), name_prh(n_prh), dept_prh(d_prh), year_prh(y_prh) {}
};
```

### 3.2 Hash Table Representation

We represent the hash table as:

```cpp
int tableSize_prh;
vector< list<Student_prh> > table_prh;
```

- `tableSize_prh` — number of buckets (hash table size).  
- `table_prh[i]` — a **linked list (chain)** storing all students whose roll numbers hash to index `i`.  

---

## 4. Hash Function (Divide Method)

We use the **divide method** for hashing:

\[
h(roll) = |\text{roll}| \bmod \text{tableSize\_prh}
\]

This is simple, efficient, and standard for integer keys.

```cpp
int hashFunction_prh(int roll_prh) const {
    if (roll_prh < 0) roll_prh = -roll_prh;
    return roll_prh % tableSize_prh;
}
```

- **Time complexity:** O(1) per hash computation.  
- Works well when `tableSize_prh` is chosen sensibly (often a prime or odd number).

---

## 5. Algorithms (Pseudocode + Complexity)

### 5.1 Insert Student (Separate Chaining)

```text
insertStudent(S):
    index = hashFunction(S.roll)
    for each student X in table[index]:
        if X.roll == S.roll:
            // duplicate roll number not allowed
            print "Already exists"
            return false
    append S to list table[index]
    return true
```

- **Average Time:** O(1)  
- **Worst Time:** O(n) if all elements fall into the same bucket (rare with good hashing).  

---

### 5.2 Search Student by Roll Number

```text
searchStudent(roll):
    index = hashFunction(roll)
    for each student X in table[index]:
        if X.roll == roll:
            return FOUND, X
    return NOT FOUND
```

- **Average Time:** O(1)  
- **Worst Time:** O(n) (single long chain).  

---

### 5.3 Delete Student by Roll Number

```text
deleteStudent(roll):
    index = hashFunction(roll)
    for each student X in table[index] with iterator it:
        if X.roll == roll:
            erase X via iterator it
            return true
    return false
```

- **Average Time:** O(1)  
- **Worst Time:** O(n)  

---

## 6. Complete C++ Program (Menu-Driven)

```cpp
// assignment59_student_hash_db_prasad_hargude.cpp
// Student database management system using hashing (separate chaining)
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <list>
#include <string>
#include <limits>
using namespace std;

struct Student_prh {
    int roll_prh;
    string name_prh;
    string dept_prh;
    int year_prh;

    Student_prh(int r_prh = -1,
                const string &n_prh = "",
                const string &d_prh = "",
                int y_prh = 0)
        : roll_prh(r_prh), name_prh(n_prh), dept_prh(d_prh), year_prh(y_prh) {}
};

class StudentHashTable_prh {
private:
    int tableSize_prh;
    vector< list<Student_prh> > table_prh;

public:
    StudentHashTable_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, list<Student_prh>());
    }

    int hashFunction_prh(int roll_prh) const {
        if (roll_prh < 0) roll_prh = -roll_prh;
        return roll_prh % tableSize_prh;
    }

    bool insertStudent_prh(const Student_prh &s_prh) {
        int index_prh = hashFunction_prh(s_prh.roll_prh);
        // check for duplicate roll number
        for (const auto &st_prh : table_prh[index_prh]) {
            if (st_prh.roll_prh == s_prh.roll_prh) {
                cout << "Student with roll " << s_prh.roll_prh
                     << " already exists in bucket " << index_prh << ".\n";
                return false;
            }
        }
        table_prh[index_prh].push_back(s_prh);
        cout << "Inserted student with roll " << s_prh.roll_prh
             << " into bucket " << index_prh << ".\n";
        return true;
    }

    bool searchStudent_prh(int roll_prh, Student_prh &result_prh) const {
        int index_prh = hashFunction_prh(roll_prh);
        for (const auto &st_prh : table_prh[index_prh]) {
            if (st_prh.roll_prh == roll_prh) {
                result_prh = st_prh;
                return true;
            }
        }
        return false;
    }

    bool deleteStudent_prh(int roll_prh) {
        int index_prh = hashFunction_prh(roll_prh);
        auto &chain_prh = table_prh[index_prh];
        for (auto it_prh = chain_prh.begin(); it_prh != chain_prh.end(); ++it_prh) {
            if (it_prh->roll_prh == roll_prh) {
                chain_prh.erase(it_prh);
                cout << "Deleted student with roll " << roll_prh
                     << " from bucket " << index_prh << ".\n";
                return true;
            }
        }
        return false;
    }

    void displayTable_prh() const {
        cout << "\n--- Student Hash Table (Separate Chaining) ---\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << "Bucket " << i << " : ";
            if (table_prh[i].empty()) {
                cout << "EMPTY";
            } else {
                for (const auto &st_prh : table_prh[i]) {
                    cout << "[Roll: " << st_prh.roll_prh
                         << ", Name: " << st_prh.name_prh
                         << ", Dept: " << st_prh.dept_prh
                         << ", Year: " << st_prh.year_prh
                         << "] -> ";
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

Student_prh inputStudent_prh() {
    int roll_prh, year_prh;
    string name_prh, dept_prh;

    cout << "Enter Roll Number: ";
    cin >> roll_prh;
    flushInput_prh();

    cout << "Enter Name: ";
    getline(cin, name_prh);

    cout << "Enter Department: ";
    getline(cin, dept_prh);

    cout << "Enter Year of Study (1-4): ";
    cin >> year_prh;
    flushInput_prh();

    return Student_prh(roll_prh, name_prh, dept_prh, year_prh);
}

int main() {
    int size_prh;
    cout << "Enter hash table size (number of buckets): ";
    if (!(cin >> size_prh) || size_prh <= 0) {
        cout << "Invalid size. Using default size = 10.\n";
        flushInput_prh();
        size_prh = 10;
    }

    StudentHashTable_prh hashTable_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Student Database (Hash Table with Separate Chaining) ---\n";
        cout << "1. Insert student record\n";
        cout << "2. Search student by roll number\n";
        cout << "3. Delete student by roll number\n";
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
            Student_prh s_prh = inputStudent_prh();
            hashTable_prh.insertStudent_prh(s_prh);
        } else if (choice_prh == 2) {
            int roll_prh;
            cout << "Enter roll number to search: ";
            cin >> roll_prh;
            flushInput_prh();
            Student_prh found_prh;
            if (hashTable_prh.searchStudent_prh(roll_prh, found_prh)) {
                cout << "Student found:\n";
                cout << "  Roll: " << found_prh.roll_prh << "\n";
                cout << "  Name: " << found_prh.name_prh << "\n";
                cout << "  Dept: " << found_prh.dept_prh << "\n";
                cout << "  Year: " << found_prh.year_prh << "\n";
            } else {
                cout << "Student with roll " << roll_prh << " not found.\n";
            }
        } else if (choice_prh == 3) {
            int roll_prh;
            cout << "Enter roll number to delete: ";
            cin >> roll_prh;
            flushInput_prh();
            if (!hashTable_prh.deleteStudent_prh(roll_prh)) {
                cout << "Student with roll " << roll_prh << " not found.\n";
            }
        } else if (choice_prh == 4) {
            hashTable_prh.displayTable_prh();
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

## 7. Compilation & Run Instructions

### Compile

```bash
g++ -std=c++11 assignment59_student_hash_db_prasad_hargude.cpp -o stud59
```

### Run

```bash
./stud59
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment59_student_hash_db_prasad_hargude.cpp -o stud59.exe
stud59.exe
```

---

## 8. Sample Input/Output (Example Run)

```text
Enter hash table size (number of buckets): 5

--- Student Database (Hash Table with Separate Chaining) ---
1. Insert student record
2. Search student by roll number
3. Delete student by roll number
4. Display hash table
5. Exit
Enter your choice: 1
Enter Roll Number: 101
Enter Name: Aru
Enter Department: CSE
Enter Year of Study (1-4): 2
Inserted student with roll 101 into bucket 1.

Enter your choice: 1
Enter Roll Number: 106
Enter Name: Raj
Enter Department: IT
Enter Year of Study (1-4): 3
Inserted student with roll 106 into bucket 1.
(Here 101 % 5 = 1 and 106 % 5 = 1, so both go into bucket 1 as a chain.)

Enter your choice: 4

--- Student Hash Table (Separate Chaining) ---
Bucket 0 : EMPTY
Bucket 1 : [Roll: 101, Name: Aru, Dept: CSE, Year: 2] -> [Roll: 106, Name: Raj, Dept: IT, Year: 3] -> NULL
Bucket 2 : EMPTY
Bucket 3 : EMPTY
Bucket 4 : EMPTY

Enter your choice: 2
Enter roll number to search: 106
Student found:
  Roll: 106
  Name: Raj
  Dept: IT
  Year: 3

Enter your choice: 3
Enter roll number to delete: 101
Deleted student with roll 101 from bucket 1.

Enter your choice: 4

--- Student Hash Table (Separate Chaining) ---
Bucket 0 : EMPTY
Bucket 1 : [Roll: 106, Name: Raj, Dept: IT, Year: 3] -> NULL
Bucket 2 : EMPTY
Bucket 3 : EMPTY
Bucket 4 : EMPTY

Enter your choice: 5
Exiting...
```

---

## 9. Memory Management & Edge Cases

- Uses `vector< list<Student_prh> >` → memory is automatically managed (no manual `new`/`delete`).  
- Handles:
  - Invalid hash table size (defaults to 10).  
  - Duplicate roll numbers (insertion rejected with a clear message).  
  - Searching / deleting non-existent roll numbers (prints appropriate messages).  
  - Negative roll numbers (converted to positive for hashing).  

Separate chaining makes **deletion simple** and avoids clustering issues common with open addressing.

---

## 10. Possible Enhancements

- Add more fields to `Student_prh` (e.g., phone number, email, address).  
- Implement **update** operation to modify an existing record.  
- Support **file I/O** to save and load the hash table from disk.  
- Allow the user to choose between:
  - Separate chaining  
  - Linear probing / double hashing  

---

## 11. References

- Data Structures textbooks — Hashing and Separate Chaining.  
- C++ STL documentation (`<vector>`, `<list>`, `<string>`, `<limits>`).  

---

_End of Assignment 59 — Student Database using Hash Table (Insert, Search, Delete)_
