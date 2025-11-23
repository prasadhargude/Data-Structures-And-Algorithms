# Assignment 4: Sparse Matrix Representation and Basic Operations (Function-Based)

**Author:** Prasad Hargude  
**Notes:** Demonstrates compact storage for sparse matrices and basic operations like display and simple transpose using functions.

---

## 📝 Problem Statement  

Develop a program to:  
1. Identify a **sparse matrix** (mostly zeros).  
2. Store it efficiently using a **compact representation** (row, column, value).  
3. Perform basic operations:  
   - Display the sparse matrix in full form.  
   - Simple transpose (swap rows and columns).

---

## 📊 Algorithm (Step-by-Step)  

1. Read the dimensions `rows_prh` and `cols_prh` of the matrix.  
2. Input elements into `matrix_prh[100][100]`.  
3. Count the number of non-zero elements `nonZero_prh`.  
4. Create a compact representation:  
   ```
   compact_prh[nonZero_prh][3] = {row_index, col_index, value}
   ```  
5. **Display full matrix**:  
   - Use compact representation to place values in proper positions, fill remaining with 0.  
6. **Simple transpose**:  
   - Swap row and column in compact representation.  
   - Display transposed matrix.  

---

## 💻 Program (C++ with Functions)

```cpp
#include <iostream>
using namespace std;

// Function to input matrix and count non-zero elements
int inputMatrix_prh(int rows_prh, int cols_prh, int matrix_prh[100][100]) {
    int nonZero_prh = 0;
    cout << "Enter elements of matrix:\n";
    for (int i = 0; i < rows_prh; i++) {
        for (int j = 0; j < cols_prh; j++) {
            cin >> matrix_prh[i][j];
            if (matrix_prh[i][j] != 0) nonZero_prh++;
        }
    }
    return nonZero_prh;
}

// Function to create compact representation
void createCompact_prh(int rows_prh, int cols_prh, int matrix_prh[100][100],
                       int nonZero_prh, int compact_prh[100][3]) {
    int index_prh = 0;
    for (int i = 0; i < rows_prh; i++)
        for (int j = 0; j < cols_prh; j++)
            if (matrix_prh[i][j] != 0) {
                compact_prh[index_prh][0] = i;
                compact_prh[index_prh][1] = j;
                compact_prh[index_prh][2] = matrix_prh[i][j];
                index_prh++;
            }
}

// Function to display full matrix from compact representation
void displayFullMatrix_prh(int rows_prh, int cols_prh,
                           int nonZero_prh, int compact_prh[100][3]) {
    int display_prh[100][100] = {0};
    for (int i = 0; i < nonZero_prh; i++)
        display_prh[compact_prh[i][0]][compact_prh[i][1]] = compact_prh[i][2];

    cout << "\nFull Matrix:\n";
    for (int i = 0; i < rows_prh; i++) {
        for (int j = 0; j < cols_prh; j++)
            cout << display_prh[i][j] << "\t";
        cout << endl;
    }
}

// Function to display simple transpose
void simpleTranspose_prh(int nonZero_prh, int compact_prh[100][3]) {
    cout << "\nTranspose (row, col, value):\n";
    for (int i = 0; i < nonZero_prh; i++)
        cout << compact_prh[i][1] << "\t"  // swapped
             << compact_prh[i][0] << "\t"
             << compact_prh[i][2] << endl;
}

int main() {
    int rows_prh, cols_prh;
    cout << "Enter number of rows and columns: ";
    cin >> rows_prh >> cols_prh;

    int matrix_prh[100][100] = {0};
    int nonZero_prh = inputMatrix_prh(rows_prh, cols_prh, matrix_prh);

    int compact_prh[100][3];
    createCompact_prh(rows_prh, cols_prh, matrix_prh, nonZero_prh, compact_prh);

    cout << "\nCompact Representation (row, col, value):\n";
    for (int i = 0; i < nonZero_prh; i++)
        cout << compact_prh[i][0] << "\t" 
             << compact_prh[i][1] << "\t" 
             << compact_prh[i][2] << endl;

    displayFullMatrix_prh(rows_prh, cols_prh, nonZero_prh, compact_prh);
    simpleTranspose_prh(nonZero_prh, compact_prh);

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Matrix (3x4):
0 0 3 0
22 0 0 0
0 17 0 0
```

**Output:**
```
Compact Representation (row, col, value):
0 2 3
1 0 22
2 1 17

Full Matrix:
0 0 3 0
22 0 0 0
0 17 0 0

Transpose (row, col, value):
2 0 3
0 1 22
1 2 17
```

---

## 📊 Memory Visualization

- Original matrix stored as 2D array with mostly zeros.  
- Compact representation stores only **non-zero elements** as 3-column array: `[row, col, value]`.  
- Transpose is performed by swapping **row and column indices** in compact representation.

---

## 📌 Pointer/Array Access Summary

| Expression               | Meaning                           |
|---------------------------|----------------------------------|
| `matrix_prh[i][j]`        | Element at row i, column j       |
| `compact_prh[k][0]`       | Row index of k-th non-zero element |
| `compact_prh[k][1]`       | Column index of k-th non-zero element |
| `compact_prh[k][2]`       | Value of k-th non-zero element    |
| `display_prh[i][j]`       | Full matrix display element      |
| Transpose operation       | Swap row and column in compact array |

---
