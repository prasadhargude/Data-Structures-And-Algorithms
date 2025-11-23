# Assignment 10: Employee Sorting by Average (Height & Weight) using Merge and Selection Sort

**Author:** Prasad Hargude  
**Notes:** Demonstrates sorting of employees by average of height & weight using **Selection Sort** and **Merge Sort**, and compares their theoretical time complexities.

---

## 📝 Problem Statement  

1. Input the **height** and **weight** of `n` employees.  
2. Compute **average** = (height + weight)/2 for each employee.  
3. Sort employees based on **average** using:  
   - **Selection Sort**  
   - **Merge Sort**  
4. Compare sorting efficiency based on theoretical **time complexity**.

---

## 📚 Concepts  

### Selection Sort
- Simple **comparison-based sorting**.  
- Repeatedly find the minimum (or maximum) element and swap with the first unsorted element.  
- **Time Complexity:** O(n²) in all cases.  
- Suitable for small datasets.  

### Merge Sort
- **Divide & Conquer** algorithm.  
- Split array into halves, recursively sort each half, and **merge** sorted halves.  
- **Time Complexity:** O(n log n) for all cases.  
- More efficient for large datasets.

### Average for Sorting
- Average = `(height + weight)/2`.  
- Sorting employees by average helps combine multiple attributes into a single key.

---

## 📊 Algorithm (Step-by-Step)  

### Step 1: Input
1. Read number of employees `n_prh`.  
2. Read `height_prh` and `weight_prh` for each employee.  
3. Compute `avg_prh` = (height + weight)/2.

### Step 2: Selection Sort
1. For `i` = 0 to n-2 → loop through employees.  
2. Find index of minimum `avg_prh` from unsorted part.  
3. Swap employee data at current index and minimum index.  
4. Repeat until array is sorted.  
5. Print **pass-by-pass** sorting if needed.

### Step 3: Merge Sort
1. Recursively divide array into halves.  
2. Merge sorted halves based on `avg_prh`.  
3. Print **sorted array** at the end.  

### Step 4: Compare Time Complexity
- **Selection Sort:** O(n²)  
- **Merge Sort:** O(n log n)  
- Conclude Merge Sort is theoretically faster for larger datasets.

---

## 💻 Program (C++ with Functions)

```cpp
#include <iostream>
using namespace std;

struct Employee_prh {
    string name_prh;
    float height_prh;
    float weight_prh;
    float avg_prh;
};

// Function to calculate average
void calculateAverage_prh(Employee_prh emp_prh[], int n_prh) {
    for (int i = 0; i < n_prh; i++)
        emp_prh[i].avg_prh = (emp_prh[i].height_prh + emp_prh[i].weight_prh) / 2.0;
}

// Function to print employees
void printEmployees_prh(Employee_prh emp_prh[], int n_prh) {
    cout << "Name\tHeight\tWeight\tAverage\n";
    for (int i = 0; i < n_prh; i++)
        cout << emp_prh[i].name_prh << "\t" << emp_prh[i].height_prh << "\t" 
             << emp_prh[i].weight_prh << "\t" << emp_prh[i].avg_prh << "\n";
    cout << endl;
}

// Selection Sort
void selectionSort_prh(Employee_prh emp_prh[], int n_prh) {
    for (int i = 0; i < n_prh - 1; i++) {
        int min_idx_prh = i;
        for (int j = i + 1; j < n_prh; j++) {
            if (emp_prh[j].avg_prh < emp_prh[min_idx_prh].avg_prh)
                min_idx_prh = j;
        }
        swap(emp_prh[i], emp_prh[min_idx_prh]);
    }
}

// Merge function for Merge Sort
void merge_prh(Employee_prh emp_prh[], int l_prh, int m_prh, int r_prh) {
    int n1_prh = m_prh - l_prh + 1;
    int n2_prh = r_prh - m_prh;

    Employee_prh L_prh[100], R_prh[100];
    for (int i = 0; i < n1_prh; i++) L_prh[i] = emp_prh[l_prh + i];
    for (int i = 0; i < n2_prh; i++) R_prh[i] = emp_prh[m_prh + 1 + i];

    int i_prh = 0, j_prh = 0, k_prh = l_prh;
    while (i_prh < n1_prh && j_prh < n2_prh) {
        if (L_prh[i_prh].avg_prh <= R_prh[j_prh].avg_prh)
            emp_prh[k_prh++] = L_prh[i_prh++];
        else
            emp_prh[k_prh++] = R_prh[j_prh++];
    }
    while (i_prh < n1_prh) emp_prh[k_prh++] = L_prh[i_prh++];
    while (j_prh < n2_prh) emp_prh[k_prh++] = R_prh[j_prh++];
}

// Merge Sort
void mergeSort_prh(Employee_prh emp_prh[], int l_prh, int r_prh) {
    if (l_prh < r_prh) {
        int m_prh = l_prh + (r_prh - l_prh)/2;
        mergeSort_prh(emp_prh, l_prh, m_prh);
        mergeSort_prh(emp_prh, m_prh + 1, r_prh);
        merge_prh(emp_prh, l_prh, m_prh, r_prh);
    }
}

int main() {
    int n_prh;
    cout << "Enter number of employees: ";
    cin >> n_prh;

    Employee_prh emp_prh[100];
    for (int i = 0; i < n_prh; i++) {
        cout << "Enter name of employee " << i+1 << ": ";
        cin >> emp_prh[i].name_prh;
        cout << "Enter height: ";
        cin >> emp_prh[i].height_prh;
        cout << "Enter weight: ";
        cin >> emp_prh[i].weight_prh;
    }

    calculateAverage_prh(emp_prh, n_prh);

    cout << "\nEmployees before sorting:\n";
    printEmployees_prh(emp_prh, n_prh);

    // Selection Sort
    Employee_prh empSelection_prh[100];
    for (int i = 0; i < n_prh; i++) empSelection_prh[i] = emp_prh[i];

    selectionSort_prh(empSelection_prh, n_prh);
    cout << "\nEmployees after Selection Sort (ascending avg):\n";
    printEmployees_prh(empSelection_prh, n_prh);

    // Merge Sort
    Employee_prh empMerge_prh[100];
    for (int i = 0; i < n_prh; i++) empMerge_prh[i] = emp_prh[i];

    mergeSort_prh(empMerge_prh, 0, n_prh - 1);
    cout << "\nEmployees after Merge Sort (ascending avg):\n";
    printEmployees_prh(empMerge_prh, n_prh);

    cout << "\nTime Complexity Analysis:\n";
    cout << "Selection Sort: O(n^2) - slower for large n\n";
    cout << "Merge Sort: O(n log n) - faster for large n\n";

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Enter number of employees: 4
Employee 1: Alice 160 60
Employee 2: Bob 170 75
Employee 3: Charlie 165 68
Employee 4: Dave 180 80
```

**Output:**
```
Employees before sorting:
Name    Height  Weight  Average
Alice   160     60      110
Bob     170     75      122.5
Charlie 165     68      116.5
Dave    180     80      130

Employees after Selection Sort (ascending avg):
Alice   160     60      110
Charlie 165     68      116.5
Bob     170     75      122.5
Dave    180     80      130

Employees after Merge Sort (ascending avg):
Alice   160     60      110
Charlie 165     68      116.5
Bob     170     75      122.5
Dave    180     80      130

Time Complexity Analysis:
Selection Sort: O(n^2) - slower for large n
Merge Sort: O(n log n) - faster for large n
```

---

## 📊 Notes

1. **Selection Sort** is simple but inefficient for large employee lists.  
2. **Merge Sort** efficiently handles larger datasets using divide & conquer.  
3. `_prh` suffix is used for main variables and arrays.  
4. Sorting by **average of height and weight** combines multiple criteria into one key.

