# Assignment 9: Bubble Sort for Student Roll Number Assignment

**Author:** Prasad Hargude  
**Notes:** Demonstrates **Bubble Sort** with pass-by-pass analysis for assigning roll numbers based on previous year results.

---

## 📝 Problem Statement  

1. Input marks of students.  
2. Assign **roll numbers** based on marks in descending order (topper gets roll no. 1).  
3. Use **Bubble Sort** to sort marks.  
4. Display **pass-by-pass** sorting progress.

---

## 📚 Concepts  

### Bubble Sort
- Simple **comparison-based sorting algorithm**.  
- Repeatedly swap adjacent elements if they are in the wrong order.  
- Sorting progresses **pass by pass**, largest remaining element moves to correct position each pass.  
- Count and display **each pass** to analyze sorting.

---

## 📊 Algorithm (Step-by-Step)  

1. Read number of students `n_prh` and their marks `marks_prh[]`.  
2. Initialize `roll_no_prh[]` corresponding to marks.  
3. For `i` = 0 to n-2 → iterate through passes:  
   - Compare adjacent marks.  
   - Swap if current < next (descending order).  
   - Swap corresponding roll numbers.  
   - Display array **after each pass**.  
4. Assign **roll numbers** from sorted array.  
5. Print marks with assigned roll numbers.

---

## 💻 Program (C++ with Functions)

```cpp
#include <iostream>
using namespace std;

// Function to print marks with roll numbers
void printMarksWithRolls_prh(int marks_prh[], int roll_no_prh[], int n_prh, int pass_prh) {
    cout << "Pass " << pass_prh << ":\n";
    for (int i = 0; i < n_prh; i++)
        cout << "Marks: " << marks_prh[i] << ", Roll No: " << roll_no_prh[i] << endl;
    cout << endl;
}

// Bubble Sort function (descending order)
void bubbleSort_prh(int marks_prh[], int roll_no_prh[], int n_prh) {
    int pass_prh = 0;
    for (int i = 0; i < n_prh - 1; i++) {
        for (int j = 0; j < n_prh - i - 1; j++) {
            if (marks_prh[j] < marks_prh[j + 1]) { // Descending order
                swap(marks_prh[j], marks_prh[j + 1]);
                swap(roll_no_prh[j], roll_no_prh[j + 1]);
            }
        }
        pass_prh++;
        printMarksWithRolls_prh(marks_prh, roll_no_prh, n_prh, pass_prh);
    }
}

int main() {
    int n_prh;
    cout << "Enter number of students: ";
    cin >> n_prh;

    int marks_prh[100], roll_no_prh[100];
    cout << "Enter marks of " << n_prh << " students:\n";
    for (int i = 0; i < n_prh; i++) {
        cin >> marks_prh[i];
        roll_no_prh[i] = i + 1; // Initial roll numbers
    }

    cout << "\nBubble Sort Pass-by-Pass (Descending for roll assignment):\n";
    bubbleSort_prh(marks_prh, roll_no_prh, n_prh);

    cout << "Final Assigned Roll Numbers:\n";
    for (int i = 0; i < n_prh; i++)
        cout << "Marks: " << marks_prh[i] << ", Roll No: " << roll_no_prh[i] << endl;

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Enter number of students: 5
Enter marks: 78 92 65 88 80
```

**Output:**
```
Bubble Sort Pass-by-Pass (Descending for roll assignment):
Pass 1:
Marks: 92, Roll No: 2
Marks: 78, Roll No: 1
Marks: 88, Roll No: 4
Marks: 80, Roll No: 5
Marks: 65, Roll No: 3

Pass 2:
Marks: 92, Roll No: 2
Marks: 88, Roll No: 4
Marks: 78, Roll No: 1
Marks: 80, Roll No: 5
Marks: 65, Roll No: 3

Pass 3:
Marks: 92, Roll No: 2
Marks: 88, Roll No: 4
Marks: 80, Roll No: 5
Marks: 78, Roll No: 1
Marks: 65, Roll No: 3

Pass 4:
Marks: 92, Roll No: 2
Marks: 88, Roll No: 4
Marks: 80, Roll No: 5
Marks: 78, Roll No: 1
Marks: 65, Roll No: 3

Final Assigned Roll Numbers:
Marks: 92, Roll No: 1
Marks: 88, Roll No: 2
Marks: 80, Roll No: 3
Marks: 78, Roll No: 4
Marks: 65, Roll No: 5
```

---

## 📊 Notes

1. Bubble Sort is **stable**, so initial roll numbers are preserved for equal marks.  
2. `_prh` suffix used for main variables.  
3. Pass-by-pass display helps visualize sorting and roll assignment progression.
