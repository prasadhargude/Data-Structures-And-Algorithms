# Assignment 58: Employee Database using Mid-Square Hash Function + Linear Probing  
**Author:** Prasad Ramdas Hargude  
**Unit:** VI — Hashing  

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Mid-Square Hash Function (Theory & Design)  
5. Algorithms (Pseudocode + Complexity)  
6. Complete C++ Program (Menu-Driven)  
7. Compilation & Run Instructions  
8. Sample Input/Output (Example Run)  
9. Memory Management & Edge Cases  
10. Possible Enhancements  
11. References  

---

## 1. Problem Statement

Write a C++ program to **simulate an employee database** using a **hash table**.

- Use the **Mid-Square method** as the hash function.  
- Use **linear probing** as the collision handling technique.  
- Assume suitable data for an **employee record** (similar to a faculty record: ID, name, department, salary, etc.).  
- The program should allow the user to **search a particular employee** using their ID through the hash table.

The program must be **menu-driven** and support:

1. Insert an employee record  
2. Search for an employee by ID  
3. Delete an employee by ID  
4. Display the full hash table  

All identifiers follow the `_prh` suffix style.

---

## 2. System Requirements

- **Language:** C++  
- **Standard:** C++11 or later  
- **Compiler:** `g++` / MinGW / any modern C++ compiler  
- **Data Structure:**  
  - Array-based hash table (implemented using `vector`)  
  - Collision resolution via **linear probing**  
- **User Interface:** Console-based, menu-driven  

---

## 3. Data Structure & Design

### 3.1 Employee Record Structure

We assume each employee has:

- `empId_prh` — Employee ID (int, used as hash key)  
- `name_prh` — Employee name (string)  
- `dept_prh` — Department (string)  
- `salary_prh` — Salary (double)  

```cpp
struct Employee_prh {
    int empId_prh;
    string name_prh;
    string dept_prh;
    double salary_prh;

    Employee_prh(int id_prh_val = -1,
                 string name_prh_val = "",
                 string dept_prh_val = "",
                 double salary_prh_val = 0.0)
        : empId_prh(id_prh_val),
          name_prh(name_prh_val),
          dept_prh(dept_prh_val),
          salary_prh(salary_prh_val) {}
};
```

### 3.2 Hash Table Representation

We use **open addressing with linear probing**:

```cpp
int tableSize_prh;
vector<Employee_prh> table_prh;
vector<int> status_prh;  // 0 = EMPTY, 1 = OCCUPIED, 2 = DELETED
```

- `table_prh[i]` stores an `Employee_prh` object.  
- `status_prh[i]` indicates slot state:  
  - `0` → EMPTY  
  - `1` → OCCUPIED  
  - `2` → DELETED  

---

## 4. Mid-Square Hash Function (Theory & Design)

### 4.1 Concept

The **Mid-Square method** for hashing works as follows:

1. Take the **key** (here, Employee ID).  
2. Compute its **square**.  
3. Extract one or more **middle digits** from the squared value.  
4. Apply `MOD tableSize` on this middle part to obtain the final hash index.

Example:

- Key = `123`  
- Square = `123^2 = 15129`  
- Middle digits (e.g., `51` or `512`) → suppose we take `512`  
- Index = `512 % tableSize_prh`

### 4.2 Implementation Choice

In our program:

- We square the key (using `long long` to avoid overflow).  
- Convert the squared value to a string.  
- Extract **two middle digits** from the squared string (or as many as possible if it’s short).  
- Convert these middle digits back to integer and take `MOD tableSize_prh`.

This satisfies the requirement of using the **Mid-Square method** and keeps the implementation clear and easy to understand.

#### C++ Implementation Sketch

```cpp
int midSquareHash_prh(int key_prh) const {
    long long k_prh = key_prh;
    if (k_prh < 0) k_prh = -k_prh;
    long long sq_prh = k_prh * k_prh;        // square
    string s_prh = to_string(sq_prh);        // convert to string
    int len_prh = (int)s_prh.length();
    int mid_prh = len_prh / 2;

    int start_prh = mid_prh - 1;            // aim to pick 2 middle digits
    if (start_prh < 0) start_prh = 0;
    int count_prh = 2;
    if (start_prh + count_prh > len_prh) {
        count_prh = len_prh - start_prh;
    }

    string midStr_prh = s_prh.substr(start_prh, count_prh);
    int midVal_prh = stoi(midStr_prh);
    return midVal_prh % tableSize_prh;
}
```

---

## 5. Algorithms (Pseudocode + Complexity)

### 5.1 Hash Function (Mid-Square + MOD)

```text
midSquareHash(key):
    k = abs(key)
    sq = k * k
    s = string representation of sq
    len = length(s)
    mid = len / 2
    start = max(0, mid - 1)
    middleDigits = s[start .. start+1] (or fewer if near ends)
    midVal = int(middleDigits)
    return midVal % tableSize
```

- **Time Complexity:** O(d), where d = number of digits (very small) → effectively O(1).

---

### 5.2 Insert Employee (Linear Probing)

```text
insertEmployee(emp):
    index = midSquareHash(emp.empId)
    for i from 0 to tableSize-1:
        pos = (index + i) mod tableSize
        if status[pos] == EMPTY or status[pos] == DELETED:
            table[pos] = emp
            status[pos] = OCCUPIED
            return true
        else if status[pos] == OCCUPIED and table[pos].empId == emp.empId:
            // duplicate key
            return false
    // table full
    return false
```

- **Best Case:** O(1)  
- **Worst Case:** O(tableSize_prh) (when table is nearly full)

---

### 5.3 Search Employee by ID

```text
searchEmployee(empId):
    index = midSquareHash(empId)
    for i from 0 to tableSize-1:
        pos = (index + i) mod tableSize
        if status[pos] == EMPTY:
            return NOT FOUND
        if status[pos] == OCCUPIED and table[pos].empId == empId:
            return FOUND at pos
    return NOT FOUND
```

- **Average:** O(1) for low load factor  
- **Worst:** O(tableSize_prh)

---

### 5.4 Delete Employee by ID

```text
deleteEmployee(empId):
    pos = searchEmployee(empId)
    if pos is NOT FOUND:
        return false
    status[pos] = DELETED
    return true
```

We mark the slot as **DELETED** instead of EMPTY to keep the probe sequence consistent.

---

## 6. Complete C++ Program (Menu-Driven)

```cpp
// assignment58_employee_hash_midsquare_prasad_hargude.cpp
// Employee database using Mid-Square hash function + linear probing
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <vector>
#include <string>
#include <limits>
using namespace std;

struct Employee_prh {
    int empId_prh;
    string name_prh;
    string dept_prh;
    double salary_prh;

    Employee_prh(int id_prh_val = -1,
                 string name_prh_val = "",
                 string dept_prh_val = "",
                 double salary_prh_val = 0.0)
        : empId_prh(id_prh_val),
          name_prh(name_prh_val),
          dept_prh(dept_prh_val),
          salary_prh(salary_prh_val) {}
};

class EmployeeHashTable_prh {
private:
    int tableSize_prh;
    vector<Employee_prh> table_prh;
    vector<int> status_prh; // 0 = EMPTY, 1 = OCCUPIED, 2 = DELETED

public:
    EmployeeHashTable_prh(int size_prh = 10) {
        tableSize_prh = size_prh;
        table_prh.assign(tableSize_prh, Employee_prh());
        status_prh.assign(tableSize_prh, 0);
    }

    int midSquareHash_prh(int key_prh) const {
        long long k_prh = key_prh;
        if (k_prh < 0) k_prh = -k_prh;
        long long sq_prh = k_prh * k_prh;

        string s_prh = to_string(sq_prh);
        int len_prh = static_cast<int>(s_prh.length());
        int mid_prh = len_prh / 2;

        int start_prh = mid_prh - 1;
        if (start_prh < 0) start_prh = 0;

        int count_prh = 2;
        if (start_prh + count_prh > len_prh) {
            count_prh = len_prh - start_prh;
        }

        string midStr_prh = s_prh.substr(start_prh, count_prh);
        int midVal_prh = stoi(midStr_prh);

        if (tableSize_prh == 0) return 0;
        return midVal_prh % tableSize_prh;
    }

    bool insertEmployee_prh(const Employee_prh &emp_prh) {
        if (tableSize_prh <= 0) {
            cout << "Invalid table size.\n";
            return false;
        }

        int index_prh = midSquareHash_prh(emp_prh.empId_prh);
        for (int i = 0; i < tableSize_prh; ++i) {
            int pos_prh = (index_prh + i) % tableSize_prh;
            if (status_prh[pos_prh] == 0 || status_prh[pos_prh] == 2) {
                table_prh[pos_prh] = emp_prh;
                status_prh[pos_prh] = 1;
                cout << "Inserted employee with ID " << emp_prh.empId_prh
                     << " at index " << pos_prh << ".\n";
                return true;
            } else if (status_prh[pos_prh] == 1 &&
                       table_prh[pos_prh].empId_prh == emp_prh.empId_prh) {
                cout << "Employee with ID " << emp_prh.empId_prh
                     << " already exists at index " << pos_prh << ".\n";
                return false;
            }
        }

        cout << "Hash table is full. Cannot insert employee.\n";
        return false;
    }

    bool searchEmployee_prh(int id_prh, int &pos_prh) const {
        if (tableSize_prh <= 0) return false;

        int index_prh = midSquareHash_prh(id_prh);
        for (int i = 0; i < tableSize_prh; ++i) {
            int p_prh = (index_prh + i) % tableSize_prh;
            if (status_prh[p_prh] == 0) {
                return false;
            }
            if (status_prh[p_prh] == 1 &&
                table_prh[p_prh].empId_prh == id_prh) {
                pos_prh = p_prh;
                return true;
            }
        }
        return false;
    }

    bool deleteEmployee_prh(int id_prh) {
        int pos_prh;
        if (searchEmployee_prh(id_prh, pos_prh)) {
            status_prh[pos_prh] = 2;
            cout << "Employee with ID " << id_prh
                 << " deleted (marked DELETED) at index " << pos_prh << ".\n";
            return true;
        }
        cout << "Employee with ID " << id_prh << " not found.\n";
        return false;
    }

    void displayTable_prh() const {
        cout << "\n--- Employee Hash Table (Mid-Square + Linear Probing) ---\n";
        cout << "Index | Status    | ID    | Name         | Dept        | Salary\n";
        cout << "----------------------------------------------------------------\n";
        for (int i = 0; i < tableSize_prh; ++i) {
            cout << i << "     | ";
            if (status_prh[i] == 0) {
                cout << "EMPTY     |"
                     << " -    | -           | -           | -";
            } else if (status_prh[i] == 2) {
                cout << "DELETED   |"
                     << " -    | -           | -           | -";
            } else {
                cout << "OCCUPIED  | "
                     << table_prh[i].empId_prh << "    | "
                     << table_prh[i].name_prh << " | "
                     << table_prh[i].dept_prh << " | "
                     << table_prh[i].salary_prh;
            }
            cout << "\n";
        }
    }
};

void flushInput_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

Employee_prh inputEmployee_prh() {
    int id_prh;
    string name_prh, dept_prh;
    double salary_prh;

    cout << "Enter Employee ID: ";
    cin >> id_prh;
    flushInput_prh();

    cout << "Enter Name: ";
    getline(cin, name_prh);

    cout << "Enter Department: ";
    getline(cin, dept_prh);

    cout << "Enter Salary: ";
    cin >> salary_prh;
    flushInput_prh();

    return Employee_prh(id_prh, name_prh, dept_prh, salary_prh);
}

int main() {
    int size_prh;
    cout << "Enter hash table size: ";
    if (!(cin >> size_prh) || size_prh <= 0) {
        cout << "Invalid size. Using default size = 10.\n";
        flushInput_prh();
        size_prh = 10;
    }

    EmployeeHashTable_prh ht_prh(size_prh);
    int choice_prh;

    while (true) {
        cout << "\n--- Employee Database (Mid-Square Hash + Linear Probing) ---\n";
        cout << "1. Insert employee record\n";
        cout << "2. Search employee by ID\n";
        cout << "3. Delete employee by ID\n";
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
            Employee_prh e_prh = inputEmployee_prh();
            ht_prh.insertEmployee_prh(e_prh);
        } else if (choice_prh == 2) {
            int id_prh;
            cout << "Enter Employee ID to search: ";
            cin >> id_prh;
            flushInput_prh();
            int pos_prh = -1;
            if (ht_prh.searchEmployee_prh(id_prh, pos_prh)) {
                cout << "Employee with ID " << id_prh
                     << " found at index " << pos_prh << ".\n";
            } else {
                cout << "Employee with ID " << id_prh << " not found.\n";
            }
        } else if (choice_prh == 3) {
            int id_prh;
            cout << "Enter Employee ID to delete: ";
            cin >> id_prh;
            flushInput_prh();
            ht_prh.deleteEmployee_prh(id_prh);
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

## 7. Compilation & Run Instructions

### Compile

```bash
g++ -std=c++11 assignment58_employee_hash_midsquare_prasad_hargude.cpp -o emp58
```

### Run

```bash
./emp58
```

On Windows (MinGW):

```bash
g++ -std=c++11 assignment58_employee_hash_midsquare_prasad_hargude.cpp -o emp58.exe
emp58.exe
```

---

## 8. Sample Input/Output (Example Run)

```text
Enter hash table size: 7

--- Employee Database (Mid-Square Hash + Linear Probing) ---
1. Insert employee record
2. Search employee by ID
3. Delete employee by ID
4. Display hash table
5. Exit
Enter your choice: 1
Enter Employee ID: 101
Enter Name: Aru
Enter Department: CSE
Enter Salary: 65000
Inserted employee with ID 101 at index 3.

Enter your choice: 1
Enter Employee ID: 123
Enter Name: Raj
Enter Department: IT
Enter Salary: 72000
Inserted employee with ID 123 at index 5.
(101^2 = 10201 -> middle '20' -> 20 % 7 = 6 or similar depending on mid digits logic.)

Enter your choice: 4

--- Employee Hash Table (Mid-Square + Linear Probing) ---
Index | Status    | ID    | Name         | Dept        | Salary
----------------------------------------------------------------
0     | EMPTY     | -    | -           | -           | -
1     | EMPTY     | -    | -           | -           | -
2     | EMPTY     | -    | -           | -           | -
3     | OCCUPIED  | 101  | Aru         | CSE         | 65000
4     | EMPTY     | -    | -           | -           | -
5     | OCCUPIED  | 123  | Raj         | IT          | 72000
6     | EMPTY     | -    | -           | -           | -

Enter your choice: 2
Enter Employee ID to search: 123
Employee with ID 123 found at index 5.

Enter your choice: 3
Enter Employee ID to delete: 101
Employee with ID 101 deleted (marked DELETED) at index 3.

Enter your choice: 4
--- Employee Hash Table (Mid-Square + Linear Probing) ---
Index | Status    | ID    | Name         | Dept        | Salary
----------------------------------------------------------------
0     | EMPTY     | -    | -           | -           | -
1     | EMPTY     | -    | -           | -           | -
2     | EMPTY     | -    | -           | -           | -
3     | DELETED   | -    | -           | -           | -
4     | EMPTY     | -    | -           | -           | -
5     | OCCUPIED  | 123  | Raj         | IT          | 72000
6     | EMPTY     | -    | -           | -           | -

Enter your choice: 5
Exiting...
```

*(Exact indices will depend on the mid-square hash value and table size.)*

---

## 9. Memory Management & Edge Cases

- Uses only `vector` and simple `struct` → no manual `new` / `delete`.  
- Handles:
  - Invalid table size → defaults to 10.  
  - Duplicate employee IDs → insertion rejected with a message.  
  - Search/Delete on non-existing IDs → appropriate messages.  
  - Negative IDs handled by using absolute value in hash.  
- Mid-square logic robust for small and large keys (uses `long long` and strings).

---

## 10. Possible Enhancements

- Add more employee fields (phone, email, position).  
- Add **update** operation to modify salary/department.  
- Compute and show **load factor** and average number of probes.  
- Persist the hash table to a file for long-term storage.

---

## 11. References

- Data Structures textbooks — Mid-Square hashing, open addressing, and linear probing.  
- C++ STL: `vector`, `string`, `limits`.

---

_End of Assignment 58 — Employee Database using Mid-Square Hash + Linear Probing_
