# Assignment 3: Matrix Multiplication and Memory Layout Analysis

**Author:** Prasad Hargude  
**Notes:** Demonstrates matrix multiplication using row-major and column-major access patterns to understand memory layout effects on performance.

---

## 📝 Problem Statement  

Implement matrix multiplication for two matrices `A` and `B`.  
- Multiply matrices using:  
  ```
  C[i][j] = sum_over_k (A[i][k] * B[k][j])
  ```  
- Analyze performance differences between **row-major** and **column-major** access patterns.

---

## 📊 Algorithm (Step-by-Step)  

1. Read matrix dimensions `rowsA_prh`, `colsA_prh`, `rowsB_prh`, `colsB_prh`.  
2. Validate: `colsA_prh == rowsB_prh`.  
3. Declare matrices: `A_prh`, `B_prh`, `C_row_prh`, `C_col_prh`.  
4. Input elements for `A_prh` and `B_prh`.  
5. **Row-major multiplication:**  
   - Loop `i` (rows of A), `j` (cols of B), `k` (cols of A / rows of B):  
     ```
     C_row_prh[i][j] += A_prh[i][k] * B_prh[k][j]
     ```  
6. **Column-major multiplication:**  
   - Loop `j` (cols of B), `i` (rows of A), `k` (cols of A / rows of B):  
     ```
     C_col_prh[i][j] += A_prh[i][k] * B_prh[k][j]
     ```  
7. Print both result matrices.  
8. Compare the results conceptually to observe any differences in memory access pattern performance.

---

## 💻 Program (C++)

```cpp
#include <iostream>
using namespace std;

int main() {
    // Main variables with _prh
    int rowsA_prh, colsA_prh, rowsB_prh, colsB_prh;

    // Input matrix dimensions
    cout << "Enter number of rows and columns for matrix A: ";
    cin >> rowsA_prh >> colsA_prh;
    cout << "Enter number of rows and columns for matrix B: ";
    cin >> rowsB_prh >> colsB_prh;

    if (colsA_prh != rowsB_prh) {
        cout << "Error: columns of A must equal rows of B.\n";
        return -1;
    }

    int A_prh[100][100], B_prh[100][100], C_row_prh[100][100] = {0}, C_col_prh[100][100] = {0};

    // Input matrices
    cout << "Enter elements of matrix A:\n";
    for (int i = 0; i < rowsA_prh; i++)
        for (int j = 0; j < colsA_prh; j++)
            cin >> A_prh[i][j];

    cout << "Enter elements of matrix B:\n";
    for (int i = 0; i < rowsB_prh; i++)
        for (int j = 0; j < colsB_prh; j++)
            cin >> B_prh[i][j];

    // Row-major multiplication
    for (int i = 0; i < rowsA_prh; i++) {
        for (int j = 0; j < colsB_prh; j++) {
            C_row_prh[i][j] = 0;
            for (int k = 0; k < colsA_prh; k++)
                C_row_prh[i][j] += A_prh[i][k] * B_prh[k][j];
        }
    }

    // Column-major multiplication
    for (int j = 0; j < colsB_prh; j++) {
        for (int i = 0; i < rowsA_prh; i++) {
            C_col_prh[i][j] = 0;
            for (int k = 0; k < colsA_prh; k++)
                C_col_prh[i][j] += A_prh[i][k] * B_prh[k][j];
        }
    }

    // Output
    cout << "\nResult Matrix (Row-Major):\n";
    for (int i = 0; i < rowsA_prh; i++) {
        for (int j = 0; j < colsB_prh; j++)
            cout << C_row_prh[i][j] << "\t";
        cout << endl;
    }

    cout << "\nResult Matrix (Column-Major):\n";
    for (int i = 0; i < rowsA_prh; i++) {
        for (int j = 0; j < colsB_prh; j++)
            cout << C_col_prh[i][j] << "\t";
        cout << endl;
    }

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Matrix A (2x3):
1 2 3
4 5 6
Matrix B (3x2):
7 8
9 10
11 12
```

**Output:**
```
Result Matrix (Row-Major):
58 64
139 154

Result Matrix (Column-Major):
58 64
139 154
```

**Observation:**  
- Both methods produce identical results.  
- Row-major access may have slightly better cache efficiency in practice due to contiguous row storage.

---

## 📌 Array Access Summary

| Expression          | Meaning                            |
|--------------------|------------------------------------|
| `A_prh[i][k]`       | Element at row i, column k of A    |
| `B_prh[k][j]`       | Element at row k, column j of B    |
| `C_row_prh[i][j]`   | Element at row i, column j of row-major result |
| `C_col_prh[i][j]`   | Element at row i, column j of column-major result |
| Row-major loops     | i → j → k                          |
| Column-major loops  | j → i → k                          |

---

