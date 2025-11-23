# Assignment 5: Fast Transpose of Sparse Matrix (Function-Based)

**Author:** Prasad Hargude  
**Notes:** Demonstrates **fast transpose** of a sparse matrix using **compact triplet representation**, with functions and theory for clarity.

---

## 📝 Problem Statement  

Develop a program to:  
1. Store a sparse matrix using **compact (triplet) representation**.  
2. Compute the **fast transpose** efficiently without reconstructing the full matrix.  
3. Display the transposed sparse matrix in **triplet form**.

---

## 📚 Theory / Background

- **Sparse Matrix**: A matrix in which most elements are zero. Storing all elements wastes memory.  
- **Triplet Representation**: Only non-zero elements are stored as `[row, col, value]`.  
- **Simple Transpose**: Swaps rows and columns element by element; can be inefficient for large matrices.  
- **Fast Transpose**:  
  - Counts non-zero elements in each column.  
  - Computes **starting positions** for each column in the transposed matrix.  
  - Places elements directly into the correct position in one pass.  
- **Advantages**:  
  - Reduces computation by avoiding repeated searches.  
  - Memory-efficient; no need to reconstruct full matrix.

---

## 📊 Algorithm (Step-by-Step)  

1. Read the matrix dimensions `rows_prh`, `cols_prh` and the number of non-zero elements `nonZero_prh`.  
2. Input **triplet representation** of the sparse matrix into `compact_prh[nonZero_prh][3]`.  
3. Count the number of elements in each column using `count_prh[cols_prh]`.  
4. Compute the starting position of each column in the transposed matrix using `start_prh[cols_prh]`.  
5. For each element in `compact_prh`:  
   - Place it at `fastTranspose_prh[start_prh[col]]`.  
   - Increment `start_prh[col]`.  
6. Display the **fast transposed matrix** in triplet form.  

---

## 💻 Program (C++ with Functions)

```cpp
#include <iostream>
using namespace std;

// Function to input sparse matrix in triplet form
void inputSparse_prh(int nonZero_prh, int compact_prh[100][3]) {
    cout << "Enter row, column, value for each non-zero element:\n";
    for (int i = 0; i < nonZero_prh; i++)
        cin >> compact_prh[i][0] >> compact_prh[i][1] >> compact_prh[i][2];
}

// Function to compute fast transpose
void fastTranspose_prh(int nonZero_prh, int cols_prh, int compact_prh[100][3], int fast_prh[100][3]) {
    int count_prh[100] = {0};
    int start_prh[100];

    // Count elements in each column
    for (int i = 0; i < nonZero_prh; i++)
        count_prh[compact_prh[i][1]]++;

    // Compute starting positions
    start_prh[0] = 0;
    for (int i = 1; i < cols_prh; i++)
        start_prh[i] = start_prh[i - 1] + count_prh[i - 1];

    // Place elements in fast transposed array
    for (int i = 0; i < nonZero_prh; i++) {
        int col_prh = compact_prh[i][1];
        int pos_prh = start_prh[col_prh];

        fast_prh[pos_prh][0] = compact_prh[i][1]; // new row = old col
        fast_prh[pos_prh][1] = compact_prh[i][0]; // new col = old row
        fast_prh[pos_prh][2] = compact_prh[i][2]; // value

        start_prh[col_prh]++;
    }
}

// Function to display sparse matrix in triplet form
void displaySparse_prh(int nonZero_prh, int sparse_prh[100][3], const string &title_prh) {
    cout << "\n" << title_prh << " (row, col, value):\n";
    for (int i = 0; i < nonZero_prh; i++)
        cout << sparse_prh[i][0] << "\t" 
             << sparse_prh[i][1] << "\t" 
             << sparse_prh[i][2] << endl;
}

int main() {
    int rows_prh, cols_prh, nonZero_prh;
    cout << "Enter number of rows, columns, and non-zero elements: ";
    cin >> rows_prh >> cols_prh >> nonZero_prh;

    int compact_prh[100][3], fast_prh[100][3];

    inputSparse_prh(nonZero_prh, compact_prh);
    fastTranspose_prh(nonZero_prh, cols_prh, compact_prh, fast_prh);

    displaySparse_prh(nonZero_prh, compact_prh, "Original Sparse Matrix");
    displaySparse_prh(nonZero_prh, fast_prh, "Fast Transposed Sparse Matrix");

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Rows, Columns, Non-zero elements: 3 4 4
0 1 5
0 3 8
1 0 3
2 1 6
```

**Output:**
```
Original Sparse Matrix (row, col, value):
0 1 5
0 3 8
1 0 3
2 1 6

Fast Transposed Sparse Matrix (row, col, value):
1 0 5
3 0 8
0 1 3
1 2 6
```

---

## 📊 Memory & Efficiency Notes

- `compact_prh` stores only **non-zero elements** in triplets.  
- `fast_prh` stores **transposed elements efficiently** by calculating starting positions.  
- Avoids constructing a full matrix → **better for large sparse matrices**.  
- Reduces number of comparisons and memory writes compared to simple transpose.

---

## 📌 Pointer/Array Access Summary

| Expression                  | Meaning                                  |
|------------------------------|------------------------------------------|
| `compact_prh[i][0]`          | Row index of i-th non-zero element       |
| `compact_prh[i][1]`          | Column index of i-th non-zero element    |
| `compact_prh[i][2]`          | Value of i-th non-zero element           |
| `fast_prh[i][0]`             | Row index in fast transposed matrix      |
| `fast_prh[i][1]`             | Column index in fast transposed matrix   |
| `fast_prh[i][2]`             | Value in fast transposed matrix          |
| `count_prh[col]`             | Number of elements in a specific column |
| `start_prh[col]`             | Starting index in fast transposed array  |

---

