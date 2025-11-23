# Assignment 39: Product Inventory Management System using Search Tree (BST by Product Name)
**Author:** Prasad Ramdas Hargude

---

## Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (pseudocode + complexity)  
5. C++ Implementation (complete, production-ready, menu-driven)  
6. Compilation & Run Instructions  
7. Example Runs and Test Cases  
8. Memory Management & Edge Cases  
9. Extensions and Enhancements  
10. References

---

## 1. Problem Statement
Implement a **product inventory management system** for a shop using a **search tree** (Binary Search Tree) organized by **product name**.

Each product record stores:
- **Unique Product Code** (string or integer)  
- **Product Name** (string) — used as BST key (lexicographic order)  
- **Price** (double)  
- **Quantity in Stock** (integer)  
- **Date Received** (YYYY-MM-DD string)  
- **Expiration Date** (YYYY-MM-DD string)

Operations required:
1. Insert a product into the tree (organized by product name).  
2. Display all items in the inventory using **inorder traversal** (sorted by product name).  
3. List **expired items** in **preorder** order of their names (visit node, then left, then right), relative to current date.

Notes:
- Dates are stored as ISO strings (`YYYY-MM-DD`) so string comparison reflects chronological order.
- If a product with the same product code already exists, insertion is rejected.
- If a product with the same name exists, product names are considered unique for BST keys. Duplicate names will be rejected.

---

## 2. System Requirements
- **Language:** C++ (C++11 or later)  
- **Data structure:** Binary Search Tree keyed by `product_name_prh` (string)  
- **Interface:** Console-based menu-driven program  
- **Portability:** Should compile with `g++` on Linux and MinGW on Windows

---

## 3. Data Structure & Design

### Product Node
```cpp
struct Product_prh {
    std::string code_prh;          // unique product code
    std::string name_prh;          // product name (BST key)
    double price_prh;
    int quantity_prh;
    std::string date_received_prh; // "YYYY-MM-DD"
    std::string expiry_prh;        // "YYYY-MM-DD"

    Product_prh* left_prh;
    Product_prh* right_prh;

    Product_prh(const std::string &code, const std::string &name, double price,
                int qty, const std::string &recv, const std::string &exp)
      : code_prh(code), name_prh(name), price_prh(price), quantity_prh(qty),
        date_received_prh(recv), expiry_prh(exp), left_prh(nullptr), right_prh(nullptr) {}
};
```

### Why BST by name?
- Inorder traversal gives products sorted by name.
- Preorder allows listing in a "name-centric" tree order (as requested).

We also maintain an auxiliary `unordered_set` of product codes to ensure uniqueness.

---

## 4. Algorithms (pseudocode + complexity)

### Insert (by name)
```
insert(node, product):
    if node == NULL: return new node
    if product.name < node.name: node.left = insert(node.left, product)
    else if product.name > node.name: node.right = insert(node.right, product)
    else: duplicate name -> reject
```
Time: O(h) where h = tree height.

### Inorder display (sorted by name)
```
inorder(node):
    if node == NULL: return
    inorder(node.left)
    print product data
    inorder(node.right)
```
Time: O(n)

### Preorder expired-list (visit root first)
```
preorderExpired(node, today):
    if node == NULL: return
    if node.expiry < today: print node
    preorderExpired(node.left, today)
    preorderExpired(node.right, today)
```
Time: O(n)

Date comparison: using `std::string` lexicographical compare because format is YYYY-MM-DD.

---

## 5. C++ Implementation (complete, production-ready, menu-driven)

```cpp
// assignment39_inventory_product_prasad_hargude.cpp
// C++11 — Product Inventory Management with BST keyed by product name
// Author: Prasad Ramdas Hargude

#include <iostream>
#include <string>
#include <queue>
#include <unordered_set>
#include <iomanip>
#include <limits>

using namespace std;

struct Product_prh {
    string code_prh;
    string name_prh;
    double price_prh;
    int quantity_prh;
    string date_received_prh;
    string expiry_prh;
    Product_prh* left_prh;
    Product_prh* right_prh;
    Product_prh(const string &code, const string &name, double price,
                int qty, const string &recv, const string &exp)
      : code_prh(code), name_prh(name), price_prh(price), quantity_prh(qty),
        date_received_prh(recv), expiry_prh(exp), left_prh(nullptr), right_prh(nullptr) {}
};

class Inventory_prh {
private:
    Product_prh* root_prh;
    unordered_set<string> codes_prh; // to ensure unique product codes

    Product_prh* insertRec_prh(Product_prh* node, Product_prh* prod, bool &ok) {
        if (!node) { ok = true; return prod; }
        if (prod->name_prh < node->name_prh) node->left_prh = insertRec_prh(node->left_prh, prod, ok);
        else if (prod->name_prh > node->name_prh) node->right_prh = insertRec_prh(node->right_prh, prod, ok);
        else { ok = false; } // duplicate product name
        return node;
    }

    void inorderRec_prh(Product_prh* node) const {
        if (!node) return;
        inorderRec_prh(node->left_prh);
        printProduct_prh(node);
        inorderRec_prh(node->right_prh);
    }

    void preorderExpiredRec_prh(Product_prh* node, const string &today) const {
        if (!node) return;
        if (!node->expiry_prh.empty() && node->expiry_prh < today) printProduct_prh(node);
        preorderExpiredRec_prh(node->left_prh, today);
        preorderExpiredRec_prh(node->right_prh, today);
    }

    void printProduct_prh(Product_prh* p) const {
        cout << left << setw(12) << p->code_prh
             << left << setw(25) << p->name_prh
             << right << setw(8) << fixed << setprecision(2) << p->price_prh
             << right << setw(10) << p->quantity_prh
             << "  " << p->date_received_prh
             << "  " << p->expiry_prh << "\n";
    }

    void clearRec_prh(Product_prh* node) {
        if (!node) return;
        clearRec_prh(node->left_prh);
        clearRec_prh(node->right_prh);
        delete node;
    }

public:
    Inventory_prh(): root_prh(nullptr) {}
    ~Inventory_prh() { clear_prh(); }

    // Insert product; returns true if inserted, false if duplicate code or name
    bool insert_prh(const string &code, const string &name, double price,
                    int qty, const string &recv, const string &exp) {
        if (codes_prh.find(code) != codes_prh.end()) return false;
        Product_prh* prod = new Product_prh(code, name, price, qty, recv, exp);
        bool ok = false;
        root_prh = insertRec_prh(root_prh, prod, ok);
        if (!ok) {
            delete prod;
            return false; // duplicate name
        }
        codes_prh.insert(code);
        return true;
    }

    void displayInorder_prh() const {
        cout << "\nInventory (sorted by name)\n";
        cout << left << setw(12) << "Code" << setw(25) << "Name" << setw(8) << "Price" << setw(10) << "Quantity"
             << "  DateRecv  Expiry\n";
        cout << string(80, '-') << "\n";
        inorderRec_prh(root_prh);
    }

    void listExpiredPreorder_prh(const string &today) const {
        cout << "\nExpired items (preorder) as of " << today << "\n";
        cout << left << setw(12) << "Code" << setw(25) << "Name" << setw(8) << "Price" << setw(10) << "Quantity"
             << "  DateRecv  Expiry\n";
        cout << string(80, '-') << "\n";
        preorderExpiredRec_prh(root_prh, today);
    }

    void clear_prh() {
        clearRec_prh(root_prh);
        root_prh = nullptr;
        codes_prh.clear();
    }
};

static void flushStdin_prh() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

int main() {
    cout << "Assignment 39: Product Inventory Management (BST by product name)\n";
    Inventory_prh inv;
    while (true) {
        cout << "\nMenu:\n";
        cout << "1. Insert product\n";
        cout << "2. Display all items (inorder - sorted by name)\n";
        cout << "3. List expired items (preorder order)\n";
        cout << "4. Clear inventory\n";
        cout << "5. Exit\n";
        cout << "Enter choice: ";
        int ch;
        if (!(cin >> ch)) { cout << "Invalid input\n"; flushStdin_prh(); continue; }
        flushStdin_prh();

        if (ch == 1) {
            string code, name, recv, exp;
            double price; int qty;
            cout << "Product code (unique): "; getline(cin, code);
            cout << "Product name (unique): "; getline(cin, name);
            cout << "Price: "; if (!(cin >> price)) { cout << "Invalid price\n"; flushStdin_prh(); continue; }
            cout << "Quantity: "; if (!(cin >> qty)) { cout << "Invalid quantity\n"; flushStdin_prh(); continue; }
            flushStdin_prh();
            cout << "Date Received (YYYY-MM-DD): "; getline(cin, recv);
            cout << "Expiration Date (YYYY-MM-DD) (empty if none): "; getline(cin, exp);

            bool ok = inv.insert_prh(code, name, price, qty, recv, exp);
            if (ok) cout << "Inserted product: " << name << "\n";
            else cout << "Insert failed: duplicate product code or product name.\n";

        } else if (ch == 2) {
            inv.displayInorder_prh();

        } else if (ch == 3) {
            string today;
            cout << "Enter today's date (YYYY-MM-DD) to check expiry: "; getline(cin, today);
            inv.listExpiredPreorder_prh(today);

        } else if (ch == 4) {
            inv.clear_prh();
            cout << "Inventory cleared.\n";

        } else if (ch == 5) {
            cout << "Exiting...\n"; break;
        } else {
            cout << "Invalid choice\n";
        }
    }
    return 0;
}
```

---

## 6. Compilation & Run Instructions

Compile with g++ (C++11):

```bash
g++ -std=c++11 -O2 assignment39_inventory_product_prasad_hargude.cpp -o assignment39_inventory
./assignment39_inventory
```

---

## 7. Example Runs and Test Cases

### Insert products
- Insert: Code=P001, Name=Milk, Price=45.00, Quantity=10, DateReceived=2025-11-01, Expiry=2025-11-10
- Insert: Code=P002, Name=Bread, Price=30.00, Quantity=20, DateReceived=2025-11-05, Expiry=2025-11-08
- Insert: Code=P003, Name=Soap, Price=25.00, Quantity=50, DateReceived=2025-10-20, Expiry= (empty)

### Display (Inorder)
Shows products sorted by name: Bread, Milk, Soap.

### List expired (Preorder)
If today's date = 2025-11-12, Bread and Milk will be listed (their expiry < today) in preorder traversal order.

---

## 8. Memory Management & Edge Cases
- All dynamically allocated nodes are deleted by `clear_prh()` and destructor.
- Date strings are compared lexicographically (works for YYYY-MM-DD format).
- Duplicate product code or product name insertion is rejected.
- Empty expiry date means non-expiring product (not considered expired).

---

## 9. Extensions and Enhancements
- Support update product (modify quantity/price) and delete by code/name.
- Persist inventory to file (CSV/JSON) and load on startup.
- Add search by partial name (prefix) or filter by low-stock threshold.
- Balance tree (AVL/Red-Black) to guarantee O(log n) operations for large inventories.
- GUI or web-based interface.

---

## 10. References
- BST fundamentals and traversal orders  
- ISO date format comparison properties  
- C++ reference (I/O, containers)