# Assignment 1: Basic String Operations using Single Dimensional Arrays

**Author:** Prasad Hargude  
**Notes:** Demonstrates manual string operations using arrays, pointer concepts, and basic string manipulations.

---

## 📝 Problem Statement  

Implement basic string operations such as:  
- Length calculation  
- Copy  
- Reverse  
- Concatenation  

using character single-dimensional arrays **without using built-in string library functions**.

---

## 📊 Algorithm (Step-by-Step)  

1. Read two strings `str1_prh` and `str2_prh` from the user.  
2. Calculate length manually by looping until the null character `\0`.  
3. Copy string: assign each character from `str1_prh` to `copyStr_prh`.  
4. Reverse string: swap characters from ends to middle into `revStr_prh`.  
5. Concatenate strings: append characters of `str2_prh` after `str1_prh` into `concatStr_prh`.  
6. Display lengths, copied string, reversed string, and concatenated string.  
7. Optional: visualize memory as arrays of characters.

---

## 💻 Program (C++)

```cpp
#include <iostream>
using namespace std;

int main() {
    // Main variables with _prh suffix
    char str1_prh[100], str2_prh[100], copyStr_prh[100], revStr_prh[100], concatStr_prh[200];
    int len1_prh = 0, len2_prh = 0;

    // Input
    cout << "Enter first string: ";
    cin.getline(str1_prh, 100);
    cout << "Enter second string: ";
    cin.getline(str2_prh, 100);

    // Length calculation
    for (int i = 0; str1_prh[i] != '\0'; i++) len1_prh++;
    for (int i = 0; str2_prh[i] != '\0'; i++) len2_prh++;

    // Copy
    for (int i = 0; i <= len1_prh; i++) copyStr_prh[i] = str1_prh[i];

    // Reverse
    for (int i = 0; i < len1_prh; i++) revStr_prh[i] = str1_prh[len1_prh - i - 1];
    revStr_prh[len1_prh] = '\0';

    // Concatenate
    int k_prh = 0;
    for (int i = 0; i < len1_prh; i++) concatStr_prh[k_prh++] = str1_prh[i];
    for (int i = 0; i < len2_prh; i++) concatStr_prh[k_prh++] = str2_prh[i];
    concatStr_prh[k_prh] = '\0';

    // Output
    cout << "\nResults of String Operations:\n";
    cout << "Length of str1_prh = " << len1_prh << endl;
    cout << "Length of str2_prh = " << len2_prh << endl;
    cout << "Copy of str1_prh   = " << copyStr_prh << endl;
    cout << "Reverse of str1_prh= " << revStr_prh << endl;
    cout << "Concatenation      = " << concatStr_prh << endl;

    return 0;
}
```

---

## 🧮 Example Execution

**Input:**
```
Enter first string: Hello
Enter second string: World
```

**Output:**
```
Results of String Operations:
Length of str1_prh = 5
Length of str2_prh = 5
Copy of str1_prh   = Hello
Reverse of str1_prh= olleH
Concatenation      = HelloWorld
```

---

## 📊 Memory Visualization

For input:  
```
str1_prh = "Hello"
str2_prh = "World"
```

Simulated memory (arrays):  
```
str1_prh      → [H][e][l][l][o][\0]
str2_prh      → [W][o][r][l][d][\0]
copyStr_prh   → [H][e][l][l][o][\0]
revStr_prh    → [o][l][l][e][H][\0]
concatStr_prh → [H][e][l][l][o][W][o][r][l][d][\0]
```

---

## 📌 Pointer / Array Access Summary

| Expression         | Meaning                          |
|--------------------|----------------------------------|
| `str1_prh[i]`      | i-th character of `str1_prh`     |
| `*str1_prh`        | first character of `str1_prh`    |
| `*(str1_prh + i)`  | i-th character (same as above)   |
| `copyStr_prh[i]`   | i-th character of copied string  |
| `revStr_prh[i]`    | i-th character of reversed string|
| `concatStr_prh[i]` | i-th character of concatenated string |

---

