# Assignment 6: Searching for a Specific Student in Computer Engg. Dept.

**Author:** Prasad Hargude  
**Notes:** Demonstrates **linear search** on a list of students using **structures**, **functions**, and **random data generation**.

---

## 📝 Problem Statement  

In the Computer Engineering Department of VIT, there are **S.Y., T.Y., and B.Tech. students**. Assume all students are present for a function.  

We need to identify a student of **S.Y. division X** whose:  
- Name is `"XYZ"`  
- Roll number is `"17"`  

**Use an appropriate searching method** (Linear Search) to identify the required student.

---

## 📊 Algorithm (Step-by-Step)  

1. Define a `struct Student_prh` with fields: `name_prh`, `roll_prh`, `year_prh`, `division_prh`.  
2. Ask the user to enter **number of students** in each category (S.Y., T.Y., B.Tech).  
3. Generate **random data** for all students using `rand()` for names and roll numbers.  
4. Assign **specific values** `"XYZ"` and `"17"` to one S.Y. student in division X.  
5. Display the list of students (optional).  
6. Apply **Linear Search** to find the student:  
   - Iterate through S.Y. students in division X.  
   - Compare `name_prh` and `roll_prh` with `"XYZ"` and `"17"`.  
7. If found, display the student's details; else, display "Not found".  

---

## 💻 Program (C++ with Functions)

```cpp
#include <iostream>
#include <cstdlib>
#include <ctime>
#include <cstring>
using namespace std;

// Structure to represent a student
struct Student_prh {
    char name_prh[20];
    char roll_prh[5];
    char year_prh[5];
    char division_prh;
};

// Function to generate random name
void generateRandomName_prh(char name_prh[]) {
    int len = 3 + rand() % 5; // names of length 3-7
    for (int i = 0; i < len; i++) {
        name_prh[i] = 'A' + rand() % 26;
    }
    name_prh[len] = '\0';
}

// Function to generate random roll number
void generateRandomRoll_prh(char roll_prh[]) {
    int num = 1 + rand() % 99;
    sprintf(roll_prh, "%d", num);
}

// Function to display student
void displayStudent_prh(Student_prh student_prh) {
    cout << "Name: " << student_prh.name_prh
         << ", Roll: " << student_prh.roll_prh
         << ", Year: " << student_prh.year_prh
         << ", Division: " << student_prh.division_prh
         << endl;
}

// Linear search function
int searchStudent_prh(Student_prh students_prh[], int n_prh, const char targetName_prh[], const char targetRoll_prh[], char divX_prh) {
    for (int i = 0; i < n_prh; i++) {
        if (students_prh[i].division_prh == divX_prh &&
            strcmp(students_prh[i].name_prh, targetName_prh) == 0 &&
            strcmp(students_prh[i].roll_prh, targetRoll_prh) == 0) {
            return i; // found
        }
    }
    return -1; // not found
}

int main() {
    srand(time(0));

    int numSY_prh, numTY_prh, numBtech_prh;
    cout << "Enter number of S.Y. students: ";
    cin >> numSY_prh;
    cout << "Enter number of T.Y. students: ";
    cin >> numTY_prh;
    cout << "Enter number of B.Tech students: ";
    cin >> numBtech_prh;

    int total_prh = numSY_prh + numTY_prh + numBtech_prh;
    Student_prh students_prh[100];

    // Generate random students
    for (int i = 0; i < total_prh; i++) {
        generateRandomName_prh(students_prh[i].name_prh);
        generateRandomRoll_prh(students_prh[i].roll_prh);
        if (i < numSY_prh)
            strcpy(students_prh[i].year_prh, "SY");
        else if (i < numSY_prh + numTY_prh)
            strcpy(students_prh[i].year_prh, "TY");
        else
            strcpy(students_prh[i].year_prh, "B.Tech");
        students_prh[i].division_prh = 'A' + rand() % 3; // Divisions A, B, C
    }

    // Insert target student in S.Y. division X
    int targetIndex_prh = rand() % numSY_prh;
    strcpy(students_prh[targetIndex_prh].name_prh, "XYZ");
    strcpy(students_prh[targetIndex_prh].roll_prh, "17");
    students_prh[targetIndex_prh].division_prh = 'X';

    // Display all students (optional)
    cout << "\nList of students:\n";
    for (int i = 0; i < total_prh; i++)
        displayStudent_prh(students_prh[i]);

    // Search for student
    int foundIndex_prh = searchStudent_prh(students_prh, total_prh, "XYZ", "17", 'X');
    if (foundIndex_prh != -1) {
        cout << "\nTarget student found:\n";
        displayStudent_prh(students_prh[foundIndex_prh]);
    } else {
        cout << "\nStudent not found.\n";
    }

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Number of S.Y. students: 5
Number of T.Y. students: 3
Number of B.Tech students: 2
```

**Output (sample):**
```
List of students:
Name: ABC, Roll: 12, Year: SY, Division: A
Name: XYZ, Roll: 17, Year: SY, Division: X
Name: QWE, Roll: 7, Year: SY, Division: C
...
Target student found:
Name: XYZ, Roll: 17, Year: SY, Division: X
```

---

## 📊 Notes / Explanation

1. **Structures** are used to organize student data.  
2. **Random data generation** ensures diversity of names and roll numbers.  
3. **Linear Search** efficiently identifies the target student in S.Y. division X.  
4. `_prh` naming convention ensures **consistent coding standard**.  
5. Functions (`generateRandomName_prh`, `generateRandomRoll_prh`, `displayStudent_prh`, `searchStudent_prh`) improve **readability and maintainability**.  

---

## 📌 Pointer/Array Access Summary

| Expression                    | Meaning                                  |
|--------------------------------|------------------------------------------|
| `students_prh[i].name_prh`    | Name of i-th student                     |
| `students_prh[i].roll_prh`    | Roll number of i-th student              |
| `students_prh[i].year_prh`    | Year (SY/TY/B.Tech) of i-th student     |
| `students_prh[i].division_prh`| Division of i-th student                 |
| `foundIndex_prh`               | Index of the found student (-1 if none) |

---
