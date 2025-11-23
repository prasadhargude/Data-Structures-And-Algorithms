# Assignment 22: Infix to Postfix Conversion Using Stack (Step-by-step) (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement
2. System Requirements
3. Data Structure & Design
4. Algorithm (pseudocode + complexity)
5. Step-by-step Conversion Example (a-b*c-d/e+f)
6. C++ Implementation (complete, production-ready)
7. Compilation & Run Instructions
8. Example Runs and Test Cases
9. Memory Management & Edge Cases
10. Extensions and Enhancements
11. References

---

## 1. Problem Statement
Convert a given **infix** arithmetic expression to its **postfix** (Reverse Polish) notation using a stack, and show the operations step-by-step.

Example expression:  
`a-b*c-d/e+f`

You must show the stack and the output at each token processed.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Data structure:** Stack (implemented using `std::vector` or `std::stack`)  
- **Input/Output:** Console-based; shows step-by-step trace for the given expression  
- **Operators supported:** `+`, `-`, `*`, `/`, `^` and parentheses `(` `)`  
- **Associativity:** `^` is right-associative; `*`, `/`, `+`, `-` are left-associative

---

## 3. Data Structure & Design
We use a stack to hold operators and parentheses. The output is built as a string (space-separated tokens) or vector.

Key helpers:
- `precedence_prh(op)` returns integer precedence (higher = tighter-binding)
- `isOperator_prh(ch)` checks operators
- Shunting-yard style logic: when an operator token is read, pop from the stack to output while the operator at the top of the stack has **higher precedence**, or **equal precedence and left-associative**; then push the current operator.

We will also print a trace table showing:
- Current token
- Stack contents (bottom → top)
- Output so far

---

## 4. Algorithm (pseudocode + complexity)

```
infixToPostfix(expr):
    output = empty list
    stack = empty stack

    for each token in expr (operands, operators, parentheses):
        if token is operand:
            append token to output
        else if token is '(':
            push '(' on stack
        else if token is ')':
            while stack not empty and top != '(':
                pop top -> append to output
            pop '(' from stack
        else if token is operator op1:
            while stack not empty and top is operator op2 and
                  (precedence(op2) > precedence(op1) OR
                   (precedence(op2) == precedence(op1) and op1 is left-assoc)):
                pop top -> append to output
            push op1 on stack

    while stack not empty:
        pop -> append to output

    return output (as space-separated string)
```

**Time complexity:** O(n) where n is length of expression (each token pushed/popped at most once).  
**Space complexity:** O(n) for stack and output.

---

## 5. Step-by-step Conversion Example — `a-b*c-d/e+f`

We show token-by-token trace. For clarity, stack is shown with bottom at left and top at right.

Initial: Output = `""`, Stack = `[]`

| Step | Token | Action | Stack (bottom→top) | Output |
|------|-------|--------|--------------------|--------|
| 0 | (start) | — | `[]` | ` ` |
| 1 | `a` | operand → output | `[]` | `a` |
| 2 | `-` | push `-` | `[-]` | `a` |
| 3 | `b` | operand → output | `[-]` | `a b` |
| 4 | `*` | `*` has higher precedence than top `-` → push `*` | `[- *]` | `a b` |
| 5 | `c` | operand → output | `[- *]` | `a b c` |
| 6 | `-` | encounter `-`: pop `*` (prec 2 >= 1) → output; then pop `-` (prec 1 >= 1 and left-assoc) → output; push new `-` | `[-]` | `a b c * -` |
| 7 | `d` | operand → output | `[-]` | `a b c * - d` |
| 8 | `/` | `/` has higher precedence than `-` → push `/` | `[- /]` | `a b c * - d` |
| 9 | `e` | operand → output | `[- /]` | `a b c * - d e` |
| 10 | `+` | on `+`: pop `/` (prec2) → output; pop `-` (prec1 >=1) → output; push `+` | `[+]` | `a b c * - d e / -` |
| 11 | `f` | operand → output | `[+]` | `a b c * - d e / - f` |
| End | (done) | pop remaining operators: pop `+` → output | `[]` | `a b c * - d e / - f +` |

Final postfix: **`a b c * - d e / - f +`**

(Without spaces commonly written: `abc*-de/-f+`)

---

## 6. C++ Implementation (complete, production-ready)

The code below implements:
- Tokenization (handles multi-character operands if needed — here we treat contiguous letters/digits as one operand)
- Full shunting-yard rules with precedence and associativity
- A trace mode that prints step-by-step stack/output for a given expression
- Interactive demo with the sample expression preloaded

```cpp
// assignment22_infix_to_postfix_prasad_hargude.cpp
// C++11 — Infix to Postfix conversion with step-by-step trace
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <vector>
#include <cctype>
#include <sstream>
#include <algorithm>

using namespace std;

// precedence: higher number = higher precedence
int precedence_prh(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    if (op == '^') return 3;
    return 0;
}

bool isOperator_prh(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/' || c == '^';
}

// '^' is right-associative; others are left-associative
bool isRightAssociative_prh(char op) {
    return op == '^';
}

// Tokenize: produce operands (multi-char) and single-char operators/parentheses
vector<string> tokenize_prh(const string &expr) {
    vector<string> tokens;
    int n = (int)expr.size();
    for (int i = 0; i < n; ) {
        if (isspace((unsigned char)expr[i])) { i++; continue; }
        if (isalpha((unsigned char)expr[i]) || isdigit((unsigned char)expr[i])) {
            // operand (multi-char)
            int j = i;
            while (j < n && (isalpha((unsigned char)expr[j]) || isdigit((unsigned char)expr[j]))) j++;
            tokens.push_back(expr.substr(i, j - i));
            i = j;
        } else {
            // single-char token: operator or parenthesis
            tokens.push_back(string(1, expr[i]));
            i++;
        }
    }
    return tokens;
}

string stackToString_prh(const vector<string> &stk) {
    if (stk.empty()) return "[]";
    string s = "[";
    for (size_t i = 0; i < stk.size(); ++i) {
        if (i) s += " ";
        s += stk[i];
    }
    s += "]";
    return s;
}

// Convert infix tokens to postfix (tokens). If trace==true, print step-by-step.
vector<string> infixToPostfix_prh(const vector<string> &tokens, bool trace = false) {
    vector<string> output;
    vector<string> opstack; // operator stack (strings, single-char operators or "(")

    auto peekOp = [&]() -> char {
        if (opstack.empty()) return '\0';
        return opstack.back()[0];
    };

    if (trace) {
        cout << "Step-by-step trace:\n";
        cout << "Token | Stack (bottom->top) | Output\n";
        cout << "-------------------------------------\n";
    }

    int step = 0;
    for (const string &tok : tokens) {
        step++;
        if (tok.empty()) continue;
        char c = tok[0];
        bool isOperand = isalnum((unsigned char)tok[0]); // operand (variables/numbers)
        if (isOperand) {
            output.push_back(tok);
            if (trace) {
                cout << step << " (" << tok << ")"
                     << " | " << stackToString_prh(opstack)
                     << " | " << [&]() { // inline lambda to format output
                        string o;
                        for (size_t i=0;i<output.size();++i){ if (i) o += ' '; o += output[i]; }
                        return o;
                     }() << "\n";
            }
        } else if (tok == "(") {
            opstack.push_back(tok);
            if (trace) {
                cout << step << " (" << tok << ")"
                     << " | " << stackToString_prh(opstack)
                     << " | " << [&]() { string o; for (size_t i=0;i<output.size();++i){ if (i) o += ' '; o += output[i]; } return o; }() << "\n";
            }
        } else if (tok == ")") {
            // pop until '('
            while (!opstack.empty() && opstack.back() != "(") {
                output.push_back(opstack.back());
                opstack.pop_back();
            }
            if (!opstack.empty() && opstack.back() == "(") opstack.pop_back();
            else {
                // mismatched parenthesis
                cerr << "Error: mismatched parentheses\n";
                return {};
            }
            if (trace) {
                cout << step << " ())"
                     << " | " << stackToString_prh(opstack)
                     << " | " << [&]() { string o; for (size_t i=0;i<output.size();++i){ if (i) o += ' '; o += output[i]; } return o; }() << "\n";
            }
        } else if (isOperator_prh(c)) {
            char op1 = c;
            while (!opstack.empty() && isOperator_prh(opstack.back()[0])) {
                char op2 = opstack.back()[0];
                int p2 = precedence_prh(op2);
                int p1 = precedence_prh(op1);
                if ( (p2 > p1) || (p2 == p1 && !isRightAssociative_prh(op1)) ) {
                    // pop op2 to output
                    output.push_back(opstack.back());
                    opstack.pop_back();
                } else break;
            }
            opstack.push_back(string(1, op1));
            if (trace) {
                cout << step << " (" << tok << ")"
                     << " | " << stackToString_prh(opstack)
                     << " | " << [&]() { string o; for (size_t i=0;i<output.size();++i){ if (i) o += ' '; o += output[i]; } return o; }() << "\n";
            }
        } else {
            // unknown token
            cerr << "Warning: unknown token '" << tok << "' skipped\n";
        }
    }

    // Pop remaining operators
    while (!opstack.empty()) {
        if (opstack.back() == "(" || opstack.back() == ")") {
            cerr << "Error: mismatched parentheses\n";
            return {};
        }
        output.push_back(opstack.back());
        opstack.pop_back();
    }

    if (trace) {
        cout << "-------------------------------------\n";
        cout << "Final Postfix: ";
        for (size_t i = 0; i < output.size(); ++i) { if (i) cout << ' '; cout << output[i]; }
        cout << "\n";
    }

    return output;
}

int main() {
    cout << "Assignment 22: Infix to Postfix Conversion (Trace)\n";
    cout << "Sample expression: a-b*c-d/e+f\n\n";

    string expr = "a-b*c-d/e+f";
    auto tokens = tokenize_prh(expr);
    auto postfix = infixToPostfix_prh(tokens, true);

    cout << "\nPostfix (space-separated): ";
    for (size_t i = 0; i < postfix.size(); ++i) {
        if (i) cout << ' ';
        cout << postfix[i];
    }
    cout << "\n";

    // Interactive mode
    cout << "\nYou can also enter your own expression (or press Enter to exit):\n";
    while (true) {
        cout << "Enter infix expression: ";
        string line;
        if (!std::getline(cin, line)) break;
        if (line.empty()) break;
        auto toks = tokenize_prh(line);
        auto post = infixToPostfix_prh(toks, true);
        cout << "Postfix: ";
        for (size_t i = 0; i < post.size(); ++i) {
            if (i) cout << ' ';
            cout << post[i];
        }
        cout << "\n\n";
    }

    cout << "Exiting...\n";
    return 0;
}
