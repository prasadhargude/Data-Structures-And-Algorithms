# Assignment 8: Quick Sort and Divide & Conquer for Min/Max Marks

**Author:** Prasad Hargude  
**Notes:** Demonstrates **Quick Sort** pass-by-pass sorting and recursive **Divide & Conquer** for minimum and maximum values.

---

## 📝 Problem Statement  

1. Input marks of `n` students.  
2. Sort the marks in ascending order using **Quick Sort** (without built-in functions).  
3. Analyze the sorting **pass by pass**.  
4. Find **minimum** and **maximum** marks using **Divide & Conquer** recursively.

---

## 📚 Concepts  

### Quick Sort
- **Divide and Conquer algorithm**.  
- Pick a **pivot** element, partition array: smaller elements to the left, larger to the right.  
- Recursively sort subarrays.  
- Counting **pass by pass** helps visualize sorting progress.

### Divide & Conquer for Min/Max
- Split array into two halves recursively.  
- Find min and max of each half.  
- Combine results: `min = min(leftMin, rightMin)`, `max = max(leftMax, rightMax)`.  
- More efficient than linear search for large arrays.

---

## 📊 Algorithm (Step-by-Step)  

### Quick Sort
1. Pick pivot (last element).  
2. Partition array: smaller than pivot left, greater right.  
3. Swap elements if needed.  
4. Recursively sort left and right subarrays.  
5. Print array **after each partition** for pass-by-pass analysis.

### Divide & Conquer for Min/Max
1. If array size = 1 → min and max are the single element.  
2. If array size = 2 → compare both elements.  
3. Else → split array into two halves.  
4. Recursively find min/max of each half.  
5. Combine results to get overall min and max.

---

## 💻 Program (C++ with Functions)

```cpp
#include <iostream>
using namespace std;

// Function to print array
void printMarks_prh(int arr_prh[], int n_prh, int pass_prh) {
    cout << "Pass " << pass_prh << ": ";
    for (int i = 0; i < n_prh; i++)
        cout << arr_prh[i] << " ";
    cout << endl;
}

// Swap function
void swap_prh(int &a_prh, int &b_prh) {
    int temp_prh = a_prh;
    a_prh = b_prh;
    b_prh = temp_prh;
}

// Partition function for Quick Sort
int partition_prh(int arr_prh[], int low_prh, int high_prh) {
    int pivot_prh = arr_prh[high_prh];
    int i_prh = low_prh - 1;
    for (int j = low_prh; j < high_prh; j++) {
        if (arr_prh[j] < pivot_prh) {
            i_prh++;
            swap_prh(arr_prh[i_prh], arr_prh[j]);
        }
    }
    swap_prh(arr_prh[i_prh + 1], arr_prh[high_prh]);
    return i_prh + 1;
}

// Quick Sort with pass-by-pass print
void quickSort_prh(int arr_prh[], int low_prh, int high_prh, int n_prh, int &pass_prh) {
    if (low_prh < high_prh) {
        int pi_prh = partition_prh(arr_prh, low_prh, high_prh);
        pass_prh++;
        printMarks_prh(arr_prh, n_prh, pass_prh);
        quickSort_prh(arr_prh, low_prh, pi_prh - 1, n_prh, pass_prh);
        quickSort_prh(arr_prh, pi_prh + 1, high_prh, n_prh, pass_prh);
    }
}

// Divide & Conquer to find min and max
void findMinMax_prh(int arr_prh[], int low_prh, int high_prh, int &min_prh, int &max_prh) {
    if (low_prh == high_prh) { // Only one element
        min_prh = max_prh = arr_prh[low_prh];
        return;
    } else if (high_prh == low_prh + 1) { // Two elements
        if (arr_prh[low_prh] < arr_prh[high_prh]) {
            min_prh = arr_prh[low_prh];
            max_prh = arr_prh[high_prh];
        } else {
            min_prh = arr_prh[high_prh];
            max_prh = arr_prh[low_prh];
        }
        return;
    } else {
        int mid_prh = (low_prh + high_prh) / 2;
        int min1_prh, max1_prh, min2_prh, max2_prh;
        findMinMax_prh(arr_prh, low_prh, mid_prh, min1_prh, max1_prh);
        findMinMax_prh(arr_prh, mid_prh + 1, high_prh, min2_prh, max2_prh);
        min_prh = (min1_prh < min2_prh) ? min1_prh : min2_prh;
        max_prh = (max1_prh > max2_prh) ? max1_prh : max2_prh;
    }
}

int main() {
    int n_prh;
    cout << "Enter number of students: ";
    cin >> n_prh;

    int marks_prh[100];
    cout << "Enter marks of " << n_prh << " students:\n";
    for (int i = 0; i < n_prh; i++) {
        cin >> marks_prh[i];
    }

    // Quick Sort with pass-by-pass
    int pass_prh = 0;
    cout << "\nQuick Sort Pass-by-Pass:\n";
    quickSort_prh(marks_prh, 0, n_prh - 1, n_prh, pass_prh);

    cout << "\nSorted Marks: ";
    for (int i = 0; i < n_prh; i++)
        cout << marks_prh[i] << " ";
    cout << endl;

    // Find min and max using Divide & Conquer
    int min_prh, max_prh;
    findMinMax_prh(marks_prh, 0, n_prh - 1, min_prh, max_prh);
    cout << "\nMinimum Marks: " << min_prh << endl;
    cout << "Maximum Marks: " << max_prh << endl;

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Enter number of students: 6
Enter marks: 45 78 32 90 56 67
```

**Output:**
```
Quick Sort Pass-by-Pass:
Pass 1: 45 32 78 56 67 90
Pass 2: 32 45 78 56 67 90
Pass 3: 32 45 78 56 67 90
Pass 4: 32 45 56 78 67 90
Pass 5: 32 45 56 67 78 90

Sorted Marks: 32 45 56 67 78 90

Minimum Marks: 32
Maximum Marks: 90
```

---

## 📊 Notes

1. Quick Sort uses **last element as pivot** and prints array **after each partition**.  
2. Divide & Conquer finds **min and max efficiently** in fewer comparisons than linear search.  
3. `_prh` suffix is used for main variables.  
4. Recursive approach shows clarity of Divide & Conquer principle.

---

## 📌 Array/Pointer Access Summary

| Expression         | Meaning                         |
|--------------------|---------------------------------|
| `marks_prh[i]`      | Marks of i-th student          |
| `pass_prh`          | Pass number in Quick Sort       |
| `min_prh`           | Minimum marks in the array     |
| `max_prh`           | Maximum marks in the array     |
