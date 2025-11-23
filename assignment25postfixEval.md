# Assignment 25: Evaluate Postfix Expression using Stack (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithm (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases (with expected outputs)  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References  

---

## 1. Problem Statement
You are given a **postfix expression** (Reverse Polish Notation) consisting of **single-digit operands** (`0`–`9`) and binary operators: `+`, `-`, `*`, `/`.  
Write a C++ program that **evaluates** the postfix expression using a **stack** and returns its result.

Assumptions:
- Operands are single-digit integers (0–9).
- Operators are the four basic arithmetic operators `+`, `-`, `*`, `/`.
- Division uses integer division (truncates toward zero). Division by zero must be handled gracefully.
- Whitespace may be present and should be ignored.

Example:
```
Postfix: 231*+9-
Infix: 2 + 3*1 - 9
Result: -4
```

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible or later)  
- **Data structure:** Stack (use `std::vector` or `std::stack`)  
- **Input/Output:** Console-based; accepts a postfix string and prints the numeric result or error message  
- **Portability:** Should compile with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design
We use a stack of integers:

- Scan the postfix expression from left to right.
- When encountering an operand (digit), convert to integer and push onto stack.
- When encountering an operator:
  - Pop two operands from stack: `b = pop()`, then `a = pop()` (note order).
  - Compute `a op b` and push the result back onto stack.
- After processing all tokens, the stack should contain exactly one value — the final result.

Why stack?
- Postfix evaluation naturally follows a LIFO pattern: most recent operands are used by incoming operators.

---

## 4. Algorithm (pseudocode + complexity)

```
evaluatePostfix(expr):
    create empty stack S
    for each character ch in expr:
        if ch is whitespace: continue
        if ch is digit:
            push int(ch - '0') onto S
        else if ch is operator op:
            if S.size() < 2: error (invalid expression)
            b = S.pop()
            a = S.pop()
            if op == '/' and b == 0: error (division by zero)
            result = apply op to a and b
            push result onto S
        else:
            error (invalid character)
    if S.size() != 1: error (invalid expression)
    return S.top()
```

**Time Complexity:** O(n) where n is length of expression.  
**Space Complexity:** O(n) in worst case for the stack.

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment25_postfix_evaluation_prasad_hargude.cpp
// C++11 — Evaluate postfix expression using stack
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <stack>
#include <cctype>
#include <stdexcept>

using namespace std;

// Apply operator op to operands a and b (a op b)
int applyOp_prh(char op, int a, int b) {
    switch (op) {
        case '+': return a + b;
        case '-': return a - b;
        case '*': return a * b;
        case '/':
            if (b == 0) throw runtime_error("Division by zero");
            return a / b; // integer division
        default:
            throw runtime_error(string("Unsupported operator: ") + op);
    }
}

// Evaluate postfix expression containing single-digit operands and operators + - * /
// Ignores whitespace
int evaluatePostfix_prh(const string &expr) {
    stack<int> st_prh;
    for (size_t i = 0; i < expr.size(); ++i) {
        char ch = expr[i];
        if (isspace((unsigned char)ch)) continue;
        if (isdigit((unsigned char)ch)) {
            st_prh.push(ch - '0');
        } else if (ch == '+' || ch == '-' || ch == '*' || ch == '/') {
            if (st_prh.size() < 2) throw runtime_error("Invalid postfix expression: insufficient operands");
            int b = st_prh.top(); st_prh.pop();
            int a = st_prh.top(); st_prh.pop();
            int res = applyOp_prh(ch, a, b);
            st_prh.push(res);
        } else {
            throw runtime_error(string("Invalid character in expression: ") + ch);
        }
    }
    if (st_prh.size() != 1) throw runtime_error("Invalid postfix expression: leftover operands or insufficient operators");
    return st_prh.top();
}

int main() {
    cout << "Assignment 25: Postfix Expression Evaluation (Single-digit operands)\n";
    cout << "Enter postfix expression (single-digit operands, operators + - * /): ";
    string expr;
    if (!getline(cin, expr)) return 0;

    try {
        int result = evaluatePostfix_prh(expr);
        cout << "Result: " << result << "\n";
    } catch (const exception &e) {
        cout << "Error: " << e.what() << "\n";
    }
    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile with g++ (C++11):

```bash
g++ -std=c++11 -O2 assignment25_postfix_evaluation_prasad_hargude.cpp -o assignment25_postfix_evaluation
./assignment25_postfix_evaluation
```

---

## 7. Example Runs and Test Cases (with expected outputs)

### Example 1
Input:
```
231*+9-
```
Processing:
- Push 2, push 3, push 1
- `*` → pop 1 and 3 → 3*1 = 3 → push 3 (stack: 2,3)
- `+` → pop 3 and 2 → 2+3 = 5 → push 5
- push 9
- `-` → pop 9 and 5 → 5-9 = -4

Output:
```
Result: -4
```

### Example 2
Input:
```
82/
```
Processing:
- push 8, push 2, `/` → 8/2 = 4
Output:
```
Result: 4
```

### Example 3 — Division by zero
Input:
```
50/
```
Output:
```
Error: Division by zero
```

### Example 4 — Invalid expression (too few operands)
Input:
```
5+
```
Output:
```
Error: Invalid postfix expression: insufficient operands
```

---

## 8. Memory Management & Edge Cases
- Uses `std::stack` which manages its internal memory automatically.
- Handles and reports:
  - Invalid characters
  - Insufficient operands for an operator
  - Division by zero
  - Leftover operands after processing (invalid expression)
- Assumes single-digit operands. To support multi-digit or negative numbers, tokenization would be required.

---

## 9. Extensions and Enhancements
- Support multi-digit integers and negative numbers by tokenizing input (space-separated tokens).
- Support unary operators and functions (e.g., negation, sin, cos).
- Provide a step-by-step trace of stack operations for educational purposes.
- Implement using linked-list stack to demonstrate pointers and dynamic allocation.

---

## 10. References
- Standard algorithm for evaluating postfix expressions (Reverse Polish Notation).
- C++ STL `stack` documentation.
- Classic data structure textbooks.

