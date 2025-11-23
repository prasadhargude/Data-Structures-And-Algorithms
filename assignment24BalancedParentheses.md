# Assignment 24: Balanced Parentheses Checker Using Stack (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithm (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References  

---

## 1. Problem Statement
You are given a string containing only the characters:

```
(  )  {  }  [  ]
```

Your task is to check whether the parentheses are **balanced**.

A string is considered **balanced** if:

1. Every opening bracket has a corresponding closing bracket of the same type  
2. Brackets are closed in the correct order (LIFO behavior)

Examples:

```
Balanced:   ()   ()[]{}   { [ ( ) ] }  
Not Balanced:   (]   ([)]   ((()  
```

---

## 2. System Requirements
- **Language:** C++ (C++11)  
- **Data Structure:** Stack implemented using `std::vector`, `std::stack`, or custom linked list  
- **Input:** A string containing only bracket characters  
- **Output:** Whether the string is balanced  
- **Portability:** Should compile using g++ on Linux/Windows  

---

## 3. Data Structure & Design

We use a **stack** to hold opening brackets:

- When encountering an opening bracket (`(`, `{`, `[`), push it on stack  
- When encountering a closing bracket (`)`, `}`, `]`):
  - Stack must not be empty  
  - The top of stack must match the type  
  - If yes → pop  
  - If no → unbalanced  

At end:
- If stack is empty → balanced  
- Else → unbalanced  

Why stack?  
Because brackets follow LIFO (last opening bracket must be closed first).

---

## 4. Algorithm (pseudocode + complexity)

### Pseudocode
```
function isBalanced(expr):
    create empty stack S

    for each character ch in expr:
        if ch is '(', '{', '[':
            push ch onto S

        else if ch is ')', '}', ']':
            if S is empty:
                return false
            top = S.pop()
            if top and ch are not matching pair:
                return false

    return S is empty
```

### Matching rule
```
( matches )
{ matches }
[ matches ]
```

### Time Complexity
- O(n) — each bracket processed exactly once.

### Space Complexity
- O(n) in worst case (all opening brackets).

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment24_balanced_parentheses_prasad_hargude.cpp
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <stack>
#include <string>
using namespace std;

bool isMatchingPair_prh(char open_prh, char close_prh) {
    return (open_prh == '(' && close_prh == ')') ||
           (open_prh == '{' && close_prh == '}') ||
           (open_prh == '[' && close_prh == ']');
}

bool isBalanced_prh(const string &expr_prh) {
    stack<char> st_prh;

    for (char ch_prh : expr_prh) {
        if (ch_prh == '(' || ch_prh == '{' || ch_prh == '[') {
            st_prh.push(ch_prh);
        }
        else if (ch_prh == ')' || ch_prh == '}' || ch_prh == ']') {
            if (st_prh.empty()) return false;
            char top_prh = st_prh.top();
            st_prh.pop();
            if (!isMatchingPair_prh(top_prh, ch_prh))
                return false;
        }
        else {
            // Invalid character
            return false;
        }
    }
    return st_prh.empty();
}

int main() {
    cout << "Assignment 24: Balanced Parentheses Checker\n";

    string expr_prh;
    cout << "Enter expression: ";
    cin >> expr_prh;

    if (isBalanced_prh(expr_prh))
        cout << "The expression is BALANCED.\n";
    else
        cout << "The expression is NOT balanced.\n";

    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile:

```bash
g++ -std=c++11 assignment24_balanced_parentheses_prasad_hargude.cpp -o assignment24
```

Run:

```bash
./assignment24
```

---

## 7. Example Runs and Test Cases

### Test 1
Input:
```
()[]{}
```
Output:
```
BALANCED
```

### Test 2
Input:
```
([{}])
```
Output:
```
BALANCED
```

### Test 3
Input:
```
(]
```
Output:
```
NOT balanced
```

### Test 4
Input:
```
((()))
```
Output:
```
BALANCED
```

### Test 5
Input:
```
([)]
```
Output:
```
NOT balanced
```

---

## 8. Memory Management & Edge Cases
- Stack automatically manages memory (STL)  
- Edge cases:
  - Empty string → Balanced  
  - Single bracket → Not balanced  
  - Incorrect nesting → Not balanced  
  - Characters other than brackets → reject  

---

## 9. Extensions and Enhancements
- Show mismatch position index  
- Support multi-character tokens  
- Count number of correctly matched bracket pairs  
- Visualize bracket matching  
- Use custom linked-list-based stack instead of STL  

---

## 10. References
- Classic Stack ADT problems  
- Balanced parentheses checker (compilers and expression parsing)  
- C++ STL documentation  

