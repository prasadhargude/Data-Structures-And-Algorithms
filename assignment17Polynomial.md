# Assignment 17: Addition of Two Polynomials Using Singly Linked List (In-Depth)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement
2. System Requirements
3. Data Structure & Design
4. Algorithms (pseudocode + complexity)
5. C++ Implementation (complete, production-ready)
6. Compilation & Run Instructions
7. Example Runs and Test Cases (with expected outputs)
8. Memory Management & Edge Cases
9. Extensions and Enhancements
10. References

---

## 1. Problem Statement
Write a C++ program to perform addition of two polynomials using **singly linked lists**.  
Each polynomial is represented as a linked list where each node contains a coefficient and exponent (power). Implement functions to:

- Create polynomial from user input.
- Display a polynomial in human-readable format.
- Add two polynomials and return the resulting polynomial.
- (Optional) Simplify and sort terms by descending power.

Example polynomials:
- P1(x) = 5x^3 + 4x^2 + 2
- P2(x) = 3x^3 + 2x + 1
Result: P1 + P2 = 8x^3 + 4x^2 + 2x + 3

---

## 2. System Requirements
- **Language:** C++ (C++11 compatible)
- **Data structure:** Singly linked list for polynomial terms
- **Input/Output:** Console-based, interactive
- **Portability:** Compiles with `g++` (Linux) and MinGW (Windows)

---

## 3. Data Structure & Design

### Node structure
Each term is represented as:
```cpp
struct Term_prh {
    int coeff_prh;      // coefficient
    int pow_prh;        // exponent/power
    Term_prh* next_prh; // pointer to next term (lower power)
    Term_prh(int c, int p): coeff_prh(c), pow_prh(p), next_prh(nullptr) {}
};
```

### Polynomial representation
- Each polynomial is a singly linked list with terms sorted in **descending** order of powers (largest power first).  
- Functions:
  - `insertTerm_prh(poly, coeff, power)` — inserts term in correct position, combining like terms.
  - `addPolynomials_prh(p1, p2)` — adds two polynomials and returns a new polynomial.
  - `display_prh(poly)` — prints polynomial.

Design decisions:
- Storing in descending order simplifies addition by merging lists similar to merging sorted lists.
- `insertTerm_prh` combines coefficients when powers match and removes zero-coefficient terms.

---

## 4. Algorithms (pseudocode + complexity)

### Insert Term (descending order)
```
insertTerm(poly, coeff, power):
    if coeff == 0: return
    if poly.head == NULL or power > poly.head.power:
        insert at head
        return
    traverse to find position where current.power > power >= next.power
    if existing term with same power: add coeff (delete if zero)
    else: insert new node
```
Time complexity: O(n) per insertion

### Add Polynomials
```
add(p1, p2):
    result = empty
    t1 = p1.head, t2 = p2.head
    while t1 and t2:
        if t1.pow == t2.pow:
            sum = t1.coeff + t2.coeff
            insertTerm(result, sum, t1.pow)
            t1 = t1.next; t2 = t2.next
        else if t1.pow > t2.pow:
            insertTerm(result, t1.coeff, t1.pow); t1 = t1.next
        else:
            insertTerm(result, t2.coeff, t2.pow); t2 = t2.next
    append remaining terms
    return result
```
Time complexity: O(n + m) where n/m sizes of polynomials.

---

## 5. C++ Implementation (complete, production-ready)

```cpp
// assignment17_polynomial_prasad_hargude.cpp
// C++11 — Polynomial addition using singly linked list
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <limits>

using namespace std;

struct Term_prh {
    int coeff_prh;
    int pow_prh;
    Term_prh* next_prh;
    Term_prh(int c, int p): coeff_prh(c), pow_prh(p), next_prh(nullptr) {}
};

class Polynomial_prh {
public:
    Term_prh* head_prh;
    Polynomial_prh(): head_prh(nullptr) {}
    ~Polynomial_prh() { clear_prh(); }

    void clear_prh() {
        Term_prh* cur = head_prh;
        while (cur) {
            Term_prh* nxt = cur->next_prh;
            delete cur;
            cur = nxt;
        }
        head_prh = nullptr;
    }

    // Insert term in descending power order and combine like terms
    void insertTerm_prh(int coeff, int power) {
        if (coeff == 0) return;
        if (!head_prh || power > head_prh->pow_prh) {
            Term_prh* node = new Term_prh(coeff, power);
            node->next_prh = head_prh;
            head_prh = node;
            return;
        }
        Term_prh* cur = head_prh;
        Term_prh* prev = nullptr;
        while (cur && cur->pow_prh > power) {
            prev = cur;
            cur = cur->next_prh;
        }
        if (cur && cur->pow_prh == power) {
            // combine
            cur->coeff_prh += coeff;
            if (cur->coeff_prh == 0) {
                // remove node
                if (!prev) head_prh = cur->next_prh;
                else prev->next_prh = cur->next_prh;
                delete cur;
            }
            return;
        }
        // insert between prev and cur
        Term_prh* node = new Term_prh(coeff, power);
        if (!prev) {
            node->next_prh = head_prh;
            head_prh = node;
        } else {
            node->next_prh = cur;
            prev->next_prh = node;
        }
    }

    // Display polynomial
    void display_prh() const {
        if (!head_prh) {
            cout << "0";
            return;
        }
        Term_prh* cur = head_prh;
        bool first = true;
        while (cur) {
            if (!first) {
                cout << (cur->coeff_prh >= 0 ? " + " : " - ");
            } else {
                if (cur->coeff_prh < 0) cout << "-";
            }
            int absCoeff = abs(cur->coeff_prh);
            if (absCoeff != 1 || cur->pow_prh == 0) cout << absCoeff;
            if (cur->pow_prh != 0) {
                cout << "x";
                if (cur->pow_prh != 1) cout << "^" << cur->pow_prh;
            }
            first = false;
            cur = cur->next_prh;
        }
    }

    // Read polynomial from user: number of terms then coeff and power pairs
    void read_prh() {
        clear_prh();
        int n;
        cout << "Enter number of terms: ";
        while (!(cin >> n) || n < 0) {
            cout << "Invalid. Enter non-negative integer: ";
            cin.clear(); cin.ignore(numeric_limits<streamsize>::max(), '\n');
        }
        cout << "Enter terms as coefficient power (e.g. 5 3 for 5x^3):\n";
        for (int i = 0; i < n; ++i) {
            int c, p;
            cin >> c >> p;
            insertTerm_prh(c, p);
        }
    }

    // Add two polynomials and return result
    static Polynomial_prh add_prh(const Polynomial_prh &A, const Polynomial_prh &B) {
        Polynomial_prh R;
        Term_prh* p = A.head_prh;
        Term_prh* q = B.head_prh;
        while (p && q) {
            if (p->pow_prh == q->pow_prh) {
                R.insertTerm_prh(p->coeff_prh + q->coeff_prh, p->pow_prh);
                p = p->next_prh; q = q->next_prh;
            } else if (p->pow_prh > q->pow_prh) {
                R.insertTerm_prh(p->coeff_prh, p->pow_prh);
                p = p->next_prh;
            } else {
                R.insertTerm_prh(q->coeff_prh, q->pow_prh);
                q = q->next_prh;
            }
        }
        while (p) { R.insertTerm_prh(p->coeff_prh, p->pow_prh); p = p->next_prh; }
        while (q) { R.insertTerm_prh(q->coeff_prh, q->pow_prh); q = q->next_prh; }
        return R;
    }
};

int main() {
    cout << "Assignment 17: Polynomial Addition using Singly Linked List\n";
    Polynomial_prh P1, P2;
    cout << "\nEnter polynomial P1:\n";
    P1.read_prh();
    cout << "Enter polynomial P2:\n";
    P2.read_prh();

    cout << "\nP1(x) = ";
    P1.display_prh();
    cout << "\nP2(x) = ";
    P2.display_prh();

    Polynomial_prh R = Polynomial_prh::add_prh(P1, P2);
    cout << "\n\nP1(x) + P2(x) = ";
    R.display_prh();
    cout << "\n";

    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile and run using g++:

```bash
g++ -std=c++11 -O2 assignment17_polynomial_prasad_hargude.cpp -o assignment17_polynomial
./assignment17_polynomial
```

---

## 7. Example Runs and Test Cases (with expected outputs)

### Example 1
Input (interactive):
```
P1: number of terms = 3
Terms:
5 3
4 2
2 0

P2: number of terms = 3
Terms:
3 3
2 1
1 0
```

Explanation:
- P1 = 5x^3 + 4x^2 + 2
- P2 = 3x^3 + 2x + 1

Expected Output:
```
P1(x) = 5x^3 + 4x^2 + 2
P2(x) = 3x^3 + 2x + 1
P1(x) + P2(x) = 8x^3 + 4x^2 + 2x + 3
```

### Example 2 (with negative coefficients and zero result terms)
Input:
```
P1 terms:
2
3 2
-3 1

P2 terms:
2
-3 2
3 1
```

Expected Output:
```
P1(x) = 3x^2 - 3x
P2(x) = -3x^2 + 3x
P1 + P2 = 0
```

### Example 3 (different powers)
Input:
```
P1: 2 terms -> 4x^5 + 2x^2
P2: 2 terms -> 3x^4 + x^2
```

Expected:
```
P1 + P2 = 4x^5 + 3x^4 + 3x^2
```

---

## 8. Memory Management & Edge Cases
- Destructor `clear_prh()` frees all allocated nodes.
- `insertTerm_prh` ignores zero coefficients to keep lists minimal.
- Handles negative coefficients and removal of terms when combined coefficient becomes zero.
- Works if one polynomial is empty.

---

## 9. Extensions and Enhancements
- Implement polynomial multiplication using convolution-like list merging or FFT for dense polynomials.
- Support polynomial evaluation at a given x (Horner's method).
- Persistent storage: save/load polynomials to/from files.
- Sparse polynomial optimizations for very large exponents.

---

## 10. References
- Data structures textbooks: linked lists and polynomial representations
- C++ reference (std::cin, std::cout, memory management)

---
