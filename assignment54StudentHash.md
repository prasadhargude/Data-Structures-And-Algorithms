# Assignment 54: Store and Retrieve Student Records using Roll Numbers (Hash Table)  
**Author:** Prasad Ramdas Hargude  
**Unit:** Hashing / File & Record Management  

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

Write a **C++ program** to store and retrieve **student records** using their **roll numbers** as keys.

Use a **hash table** to map:

> `roll number → student record`

The program must support:

1. **Insert** a new student record (roll number must be unique).  
2. **Search / Retrieve** a student record using roll number.  
3. **Delete** a student record using roll number.  
4. **Display** all stored student records (show underlying hash table).

Use **linear probing** as the collision resolution technique, and follow the `_prh` suffix naming style.

---

## 2. System Requirements

- **Language:** C++  
- **Standard:** C++11 or later  
- **Compiler:** Any standard C++ compiler (`g++`, MinGW, etc.)  
- **Data Structure:**  
  - **Hash table** with **open addressing (linear probing)**  
  - Each slot stores a **student record**  
- **User Interface:** Console-based, menu-driven.  

---

## 3. Data Structure & Design

### 3.1 Student Record

Each student record contains:

- `roll_prh` — Roll number (int, **primary key**)  
- `name_prh` — Student name (string)  
- `dept_prh` — Department / branch (string)  
- `marks_prh` — Marks / percentage (double)

```cpp
struct Student_prh {
    int roll_prh;
    string name_prh;
    string dept_prh;
    double marks_prh;

    Student_prh(int r_prh = -1, string n_prh = "", string d_prh = "", double m_prh = 0.0)
        : roll_prh(r_prh), name_prh(n_prh), dept_prh(d_prh), marks_prh(m_prh) {}
};
```

### 3.2 Hash Table Representation

We use:

```cpp
vector<Student_prh> table_prh;
vector<int> status_prh;   // 0 = EMPTY, 1 = OCCUPIED, 2 = DELETED
int tableSize_prh;
```

- `table_prh[i]` stores the student record.  
- `status_prh[i]` keeps track of the **state** of each slot:
  - `0` → EMPTY  
  - `1` → OCCUPIED  
  - `2` → DELETED  

### 3.3 Hash Function

For a roll number `roll_prh`:

\[
h(roll) = |roll| \bmod \text{tableSize\_prh}
\]

---

## 4. Algorithms (Pseudocode + Complexity)

### 4.1 Hash Function

```text
hashFunction(roll):
    if roll < 0: roll = -roll
    return roll % tableSize
```

Time: **O(1)**.

---

### 4.2 Insert Student (Linear Probing)

**Goal:** Insert a new student record; roll number must be unique.

```text
insertStudent(record):
    index = hashFunction(record.roll)
    for i from 0 to tableSize - 1:
        pos = (index + i) % tableSize
        if status[pos] == EMPTY or status[pos] == DELETED:
            table[pos] = record
            status[pos] = OCCUPIED
            return true
        else if status[pos] == OCCUPIED and table[pos].roll == record.roll:
            // duplicate roll number
            return false
    // table full
    return false
```

- **Best Case:** O(1)  
- **Worst Case:** O(tableSize_prh)  

---

### 4.3 Search Student by Roll Number

```text
searchStudent(roll):
    index = hashFunction(roll)
    for i from 0 to tableSize - 1:
        pos = (index + i) % tableSize
        if status[pos] == EMPTY:
            return NOT FOUND
        if status[pos] == OCCUPIED and table[pos].roll == roll:
            return FOUND at pos
    return NOT FOUND
```

- Stops early if an **EMPTY** slot is encountered — means key can’t be further in probe chain.  
- **Average Time:** O(1) at low load factor.  

---

### 4.4 Delete Student by Roll Number

```text
deleteStudent(roll):
    pos = searchStudent(roll)
    if pos is NOT FOUND:
        return false
    status[pos] = DELETED
    // record is logically deleted
    return true
```

We mark as `DELETED` instead of `EMPTY` so the probing chain is not broken for future searches.

---

## 5. Complete C++ Program (Menu-Driven)

```cpp
// assignment54_student_hash_rollno_prasad_hargude.cpp
// Store and retrieve student records using roll numbers (hash table with linear probing)
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <string>
#include <limits>
using namespace std;

struct Student_prh {
    int roll_prh;
    string name_prh;
    string dept_prh;
    double marks_prh;

    Student_prh(int r_prh = -1, string n_prh = "", string d_prh = "", double m_prh = 0.0)
        : roll_prh(r_prh), name_prh(n_prh), dept_prh(d_prh), marks_prh(m_prh) {}
};

class StudentHashTable_prh {
private:
    int tableSize_prh;
    vector<Student_prh> table_prh;
    vector<int> status_prh; // 0 = EMPTY, 1 = OCCUPIED, 2 = DELETED

public:
    StudentHashTable_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, Student_prh());
        status_prh.assign(tableSize_prh, 0);
    }

    int hashFunction_prh(int roll_prh) const {
        if (roll_prh < 0) roll_prh = -roll_prh;
        return roll_prh % tableSize_prh;
    }

    bool insertStudent_prh(const Student_prh &stud_prh) {
        int index_prh = hashFunction_prh(stud_prh.roll_prh);
        for (int i = 0; i < tableSize_prh; ++i) {
            int pos_prh = (index_prh + i) % tableSize_prh;
            if (status_prh[pos_prh] == 0 || status_prh[pos_prh] == 2) {
                table_prh[pos_prh] = stud_prh;
                status_prh[pos_prh] = 1;
                return true;
            } else if (status_prh[pos_prh] == 1 && table_prh[pos_prh].roll_prh == stud_prh.roll_prh) {
                cout << "Student with roll " << stud_prh.roll_prh << " already exists at index "
                     << pos_prh << ".\n";
                return false;
            }
        }
        cout << "Hash table is full. Cannot insert more records.\n";
        return false;
    }

    bool searchStudent_prh(int roll_prh, int &pos_prh) const {
        int index_prh = hashFunction_prh(roll_prh);
        for (int i = 0; i < tableSize_prh; ++i) {
            int p_prh = (index_prh + i) % tableSize_prh;
            if (status_prh[p_prh] == 0) {
                // empty cell -> not found
                return false;
            }
            if (status_prh[p_prh] == 1 && table_prh[p_prh].roll_prh == roll_prh) {
                pos_prh = p_prh;
                return true;
            }
        }
        return false;
    }

    bool deleteStudent_prh(int roll_prh) {
        int pos_prh;
        if (searchStudent_prh(roll_prh, pos_prh)) {
            status_prh[pos_prh] = 2; // mark as DELETED
            return true;
        }
        return false;
    }

    void displayTable_prh() const {
        cout << "\n--- Hash Table (Student Records) ---\n";
        cout << "Index : Roll  Name         Dept       Marks   Status\n";
        cout << "-------------------------------------------------------------\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << i << " : ";
            if (status_prh[i] == 0) {
                cout << "(EMPTY)";
            } else if (status_prh[i] == 2) {
                cout << "(DELETED)";
            } else {
                cout << table_prh[i].roll_prh << "  "
                     << table_prh[i].name_prh << "  "
                     << table_prh[i].dept_prh << "  "
                     << table_prh[i].marks_prh;
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
    int roll_prh;
    string name_prh, dept_prh;
    double marks_prh;

    cout << "Enter roll number: ";
    cin >> roll_prh;
    flushInput_prh();
    cout << "Enter name: ";
    getline(cin, name_prh);
    cout << "Enter department: ";
    getline(cin, dept_prh);
    cout << "Enter marks: ";
    cin >> marks_prh;
    flushInput_prh();

    return Student_prh(roll_prh, name_prh, dept_prh, marks_prh);
}

int main() {
    int size_prh;
    cout << "Enter hash table size: ";
    if (!(cin >> size_prh) || size_prh <= 0) {
        cout << "Invalid size. Using default size = 10.\n";
        flushInput_prh();
        size_prh = 10;
    }

    StudentHashTable_prh ht_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Student Hash Table Menu ---\n";
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
            ht_prh.insertStudent_prh(s_prh);
        } else if (choice_prh == 2) {
            int roll_prh;
            cout << "Enter roll number to search: ";
            cin >> roll_prh;
            flushInput_prh();
            int pos_prh = -1;
            if (ht_prh.searchStudent_prh(roll_prh, pos_prh)) {
                cout << "Student found at index " << pos_prh << ".\n";
            } else {
                cout << "Student with roll " << roll_prh << " not found.\n";
            }
        } else if (choice_prh == 3) {
            int roll_prh;
            cout << "Enter roll number to delete: ";
            cin >> roll_prh;
            flushInput_prh();
            if (ht_prh.deleteStudent_prh(roll_prh)) {
                cout << "Student with roll " << roll_prh << " deleted (marked DELETED).\n";
            } else {
                cout << "Student with roll " << roll_prh << " not found.\n";
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
g++ -std=c++11 assignment54_student_hash_rollno_prasad_hargude.cpp -o student_hash54
```

### Run

```bash
./student_hash54
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment54_student_hash_rollno_prasad_hargude.cpp -o student_hash54.exe
student_hash54.exe
```

---

## 7. Sample Input/Output (Example Run)

```text
Enter hash table size: 7

--- Student Hash Table Menu ---
1. Insert student record
2. Search student by roll number
3. Delete student by roll number
4. Display hash table
5. Exit
Enter your choice: 1
Enter roll number: 101
Enter name: Aru
Enter department: CSE
Enter marks: 89.5
Inserted...

Enter your choice: 1
Enter roll number: 108
Enter name: Purva
Enter department: MED
Enter marks: 92.0
Inserted...

Enter your choice: 4

--- Hash Table (Student Records) ---
Index : Roll  Name         Dept       Marks   Status
-------------------------------------------------------------
0 : (EMPTY)
1 : 108  Purva  MED  92
2 : (EMPTY)
3 : (EMPTY)
4 : (EMPTY)
5 : (EMPTY)
6 : 101  Aru  CSE  89.5

Enter your choice: 2
Enter roll number to search: 101
Student found at index 6.

Enter your choice: 3
Enter roll number to delete: 101
Student with roll 101 deleted (marked DELETED).

Enter your choice: 4

--- Hash Table (Student Records) ---
Index : Roll  Name         Dept       Marks   Status
-------------------------------------------------------------
0 : (EMPTY)
1 : 108  Purva  MED  92
2 : (EMPTY)
3 : (EMPTY)
4 : (EMPTY)
5 : (EMPTY)
6 : (DELETED)

Enter your choice: 5
Exiting...
```

*(Exact positions may vary depending on table size and hash function.)*

---

## 8. Memory Management & Edge Cases

- Uses only `vector` and simple structs — **no manual `new`/`delete`** required.  
- Handles:
  - Invalid hash table size → falls back to default size 10.  
  - Duplicate roll numbers → insertion rejected with a message.  
  - Search/Delete on non-existing roll numbers → appropriate messages.  
  - Negative roll numbers → converted to positive for hashing.  

---

## 9. Possible Enhancements

- Store more fields (address, phone number, etc.).  
- Use **separate chaining** instead of linear probing for higher load factors.  
- Add functionality to **update** an existing student’s data.  
- Persist records to a **file** for permanent storage.  

---

## 10. References

- Hashing and collision resolution (Data Structures textbooks).  
- C++ STL documentation (`vector`, `string`, `limits`).  

---

_End of Assignment 54 — Store and Retrieve Student Records using Roll Numbers (Hash Table)_
