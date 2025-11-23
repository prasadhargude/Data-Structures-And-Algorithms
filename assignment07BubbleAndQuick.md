# Assignment 7: Bubble Sort & Quick Sort on Student Structure

**Author:** Prasad Hargude  
**Notes:** Demonstrates **Bubble Sort** and **Quick Sort** on 1D array of **Student structures**, counting number of swaps.

---

## 📝 Problem Statement  

WAP to implement **Bubble Sort** and **Quick Sort** on a 1D array of Student structure:  

- **Structure fields:**  
  - `student_name_prh`  
  - `student_roll_prh`  
  - `total_marks_prh`  
- **Key for sorting:** `student_roll_prh`  
- **Additional requirement:** Count the number of swaps performed by each method.

---

## 📊 Algorithm (Step-by-Step)  

### Bubble Sort
1. Traverse the array multiple times.  
2. Compare adjacent students' `roll_prh`.  
3. Swap if the current student’s roll number is greater than the next student.  
4. Increment swap counter on each swap.  
5. Repeat until array is sorted.

### Quick Sort
1. Select a **pivot** (last element).  
2. Partition array: elements smaller than pivot go left, larger go right.  
3. Swap elements as needed and increment swap counter.  
4. Recursively sort left and right partitions.  

---

## 💻 Program (C++ with Functions)

```cpp
#include <iostream>
#include <cstdlib>
#include <ctime>
#include <cstring>
using namespace std;

// Structure definition
struct Student_prh {
    char student_name_prh[20];
    int student_roll_prh;
    int total_marks_prh;
};

// Function to generate random student data
void generateRandomStudent_prh(Student_prh &student_prh, int maxRoll_prh) {
    int len = 3 + rand() % 5;
    for (int i = 0; i < len; i++)
        student_prh.student_name_prh[i] = 'A' + rand() % 26;
    student_prh.student_name_prh[len] = '\0';
    student_prh.student_roll_prh = 1 + rand() % maxRoll_prh;
    student_prh.total_marks_prh = 40 + rand() % 61; // marks 40-100
}

// Swap function
void swapStudent_prh(Student_prh &a_prh, Student_prh &b_prh, int &swapCount_prh) {
    Student_prh temp_prh = a_prh;
    a_prh = b_prh;
    b_prh = temp_prh;
    swapCount_prh++;
}

// Bubble Sort
void bubbleSort_prh(Student_prh arr_prh[], int n_prh, int &swapCount_prh) {
    swapCount_prh = 0;
    for (int i = 0; i < n_prh - 1; i++) {
        for (int j = 0; j < n_prh - i - 1; j++) {
            if (arr_prh[j].student_roll_prh > arr_prh[j + 1].student_roll_prh) {
                swapStudent_prh(arr_prh[j], arr_prh[j + 1], swapCount_prh);
            }
        }
    }
}

// Partition function for Quick Sort
int partition_prh(Student_prh arr_prh[], int low_prh, int high_prh, int &swapCount_prh) {
    int pivot_prh = arr_prh[high_prh].student_roll_prh;
    int i_prh = low_prh - 1;
    for (int j = low_prh; j < high_prh; j++) {
        if (arr_prh[j].student_roll_prh < pivot_prh) {
            i_prh++;
            swapStudent_prh(arr_prh[i_prh], arr_prh[j], swapCount_prh);
        }
    }
    swapStudent_prh(arr_prh[i_prh + 1], arr_prh[high_prh], swapCount_prh);
    return i_prh + 1;
}

// Quick Sort
void quickSort_prh(Student_prh arr_prh[], int low_prh, int high_prh, int &swapCount_prh) {
    if (low_prh < high_prh) {
        int pi_prh = partition_prh(arr_prh, low_prh, high_prh, swapCount_prh);
        quickSort_prh(arr_prh, low_prh, pi_prh - 1, swapCount_prh);
        quickSort_prh(arr_prh, pi_prh + 1, high_prh, swapCount_prh);
    }
}

// Display student array
void displayStudents_prh(Student_prh arr_prh[], int n_prh, const string &title_prh) {
    cout << "\n" << title_prh << ":\n";
    cout << "Name\tRoll\tMarks\n";
    for (int i = 0; i < n_prh; i++)
        cout << arr_prh[i].student_name_prh << "\t" 
             << arr_prh[i].student_roll_prh << "\t" 
             << arr_prh[i].total_marks_prh << "\n";
}

int main() {
    srand(time(0));
    int n_prh;
    cout << "Enter number of students: ";
    cin >> n_prh;

    Student_prh students_prh[100], temp_prh[100];

    // Generate random students
    for (int i = 0; i < n_prh; i++)
        generateRandomStudent_prh(students_prh[i], n_prh*2);

    // Copy for Quick Sort
    for (int i = 0; i < n_prh; i++)
        temp_prh[i] = students_prh[i];

    // Bubble Sort
    int bubbleSwap_prh = 0;
    bubbleSort_prh(students_prh, n_prh, bubbleSwap_prh);
    displayStudents_prh(students_prh, n_prh, "After Bubble Sort");
    cout << "Number of swaps (Bubble Sort): " << bubbleSwap_prh << "\n";

    // Quick Sort
    int quickSwap_prh = 0;
    quickSort_prh(temp_prh, 0, n_prh - 1, quickSwap_prh);
    displayStudents_prh(temp_prh, n_prh, "After Quick Sort");
    cout << "Number of swaps (Quick Sort): " << quickSwap_prh << "\n";

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Enter number of students: 5
```

**Output (sample):**
```
After Bubble Sort:
Name    Roll    Marks
QWE     2       45
ABC     3       78
XYZ     5       66
...
Number of swaps (Bubble Sort): 7

After Quick Sort:
Name    Roll    Marks
QWE     2       45
ABC     3       78
XYZ     5       66
...
Number of swaps (Quick Sort): 5
```

---

## 📊 Notes

1. `_prh` is used for all main variables.  
2. **Bubble Sort** counts swaps during adjacent comparisons.  
3. **Quick Sort** counts swaps during partitioning.  
4. Random student data ensures variability.  
5. Functions used:  
   - `generateRandomStudent_prh()`  
   - `swapStudent_prh()`  
   - `bubbleSort_prh()`  
   - `quickSort_prh()`  
   - `displayStudents_prh()`  

---

## 📌 Pointer/Array Access Summary

| Expression                   | Meaning                                  |
|-------------------------------|------------------------------------------|
| `students_prh[i].student_name_prh`  | Name of i-th student                  |
| `students_prh[i].student_roll_prh`  | Roll number of i-th student           |
| `students_prh[i].total_marks_prh`   | Total marks of i-th student           |
| `bubbleSwap_prh`              | Number of swaps in Bubble Sort          |
| `quickSwap_prh`               | Number of swaps in Quick Sort           |

---
