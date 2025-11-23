# Assignment 40: Product Inventory System — Deletion Operations (BST)
**Author:** Prasad Ramdas Hargude  
**Course:** Data Structures  

---

# Table of Contents
1. Problem Statement  
2. System Requirements  
3. Data Structure & Design  
4. Algorithms (With Pseudocode & Complexity)  
5. Complete C++ Program  
6. Expected Output (Sample Runs)  
7. Memory Management & Edge Cases  
8. Conclusion  
9. References  

---

# 1. Problem Statement
A shop maintains an **inventory of products**, where each product contains:  

- **Unique Product Code**  
- **Product Name (BST Key)**  
- **Price**  
- **Quantity in Stock**  
- **Date Received**  
- **Expiration Date**

The inventory is stored using a **Binary Search Tree (BST)**, organized by **product name**.  

You must implement the following features:

1. **Delete a product using its unique product code.**  
2. **Delete all expired products**, based on a user-provided current date.

---

# 2. System Requirements

- **Language:** C++  
- **Compiler:** g++ (C++11 or later)  
- **Data Structure:** Binary Search Tree (BST) keyed by product name  
- **Menus:** Console-based  
- **Memory:** Proper dynamic memory handling  

---

# 3. Data Structure & Design

Each product is stored in a node of a BST.

### Product Node Structure
```cpp
struct Product_prh {
    string code_prh;
    string name_prh;
    double price_prh;
    int quantity_prh;
    string date_received_prh;
    string expiry_prh;
    Product_prh* left_prh;
    Product_prh* right_prh;
};
```

### Additional Structures Used
- `unordered_set<string> codes_prh` – ensures product code uniqueness  
- `unordered_map<string,string> code_to_name_prh` – maps product code → product name  
  (This allows deletion using product code even though BST is based on product name.)

---

# 4. Algorithms

## A. Delete Product by Unique Code (Pseudocode)
```
deleteByCode(code):
    if code NOT in code_to_name_prh:
        return false

    name = code_to_name_prh[code]

    root = deleteByName(root, name)

    erase code from codes_prh
    erase code from code_to_name_prh

    return true
```

### Complexity  
- Lookup in map = **O(1)**  
- BST deletion = **O(h)**  

---

## B. Delete All Expired Products (Pseudocode)
```
deleteExpired(today):
    expiredCodes = []

    collectExpired(root, today, expiredCodes)

    for each code in expiredCodes:
        deleteByCode(code)

    return number of deleted items
```

### Complexity  
- Collect expired = **O(n)**  
- Delete each one = **O(h)**  

---

# 5. C++ Implementation (Full Program)

```cpp
#include <iostream>
#include <string>
#include <unordered_set>
#include <unordered_map>
#include <vector>
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
    Product_prh *left_prh, *right_prh;

    Product_prh(string c, string n, double p, int q, string r, string e)
        : code_prh(c), name_prh(n), price_prh(p), quantity_prh(q),
          date_received_prh(r), expiry_prh(e), left_prh(nullptr), right_prh(nullptr) {}
};

class Inventory_prh {
private:
    Product_prh *root_prh;
    unordered_set<string> codes_prh;
    unordered_map<string, string> code_to_name_prh;

    Product_prh* insertRec(Product_prh* node, Product_prh* prod, bool &ok) {
        if (!node) { ok = true; return prod; }
        if (prod->name_prh < node->name_prh)
            node->left_prh = insertRec(node->left_prh, prod, ok);
        else if (prod->name_prh > node->name_prh)
            node->right_prh = insertRec(node->right_prh, prod, ok);
        else
            ok = false;
        return node;
    }

    Product_prh* deleteByName(Product_prh* node, string name, bool &deleted) {
        if (!node) return nullptr;

        if (name < node->name_prh)
            node->left_prh = deleteByName(node->left_prh, name, deleted);
        else if (name > node->name_prh)
            node->right_prh = deleteByName(node->right_prh, name, deleted);
        else {
            deleted = true;

            // Case 1: Leaf
            if (!node->left_prh && !node->right_prh) {
                delete node;
                return nullptr;
            }
            // Case 2: One child
            if (!node->left_prh) {
                Product_prh *temp = node->right_prh;
                delete node;
                return temp;
            }
            if (!node->right_prh) {
                Product_prh *temp = node->left_prh;
                delete node;
                return temp;
            }
            // Case 3: Two children
            Product_prh *succ = node->right_prh;
            while (succ->left_prh) succ = succ->left_prh;

            node->code_prh = succ->code_prh;
            node->name_prh = succ->name_prh;
            node->price_prh = succ->price_prh;
            node->quantity_prh = succ->quantity_prh;
            node->date_received_prh = succ->date_received_prh;
            node->expiry_prh = succ->expiry_prh;

            node->right_prh = deleteByName(node->right_prh, succ->name_prh, deleted);
        }
        return node;
    }

    void collectExpired(Product_prh* node, const string &today, vector<string> &expired) {
        if (!node) return;
        if (!node->expiry_prh.empty() && node->expiry_prh < today)
            expired.push_back(node->code_prh);
        collectExpired(node->left_prh, today, expired);
        collectExpired(node->right_prh, today, expired);
    }

    void inorder(Product_prh* node) {
        if (!node) return;
        inorder(node->left_prh);
        printProduct(node);
        inorder(node->right_prh);
    }

    void printProduct(Product_prh* p) {
        cout << left << setw(12) << p->code_prh
             << setw(20) << p->name_prh
             << setw(8) << p->price_prh
             << setw(8) << p->quantity_prh
             << setw(12) << p->date_received_prh
             << setw(12) << p->expiry_prh << endl;
    }

    void clear(Product_prh* node) {
        if (!node) return;
        clear(node->left_prh);
        clear(node->right_prh);
        delete node;
    }

public:
    Inventory_prh() : root_prh(nullptr) {}

    ~Inventory_prh() {
        clear(root_prh);
    }

    bool insert(string code, string name, double price, int qty,
                string recv, string exp) {
        if (codes_prh.count(code)) return false;

        Product_prh *prod = new Product_prh(code, name, price, qty, recv, exp);
        bool ok = false;
        root_prh = insertRec(root_prh, prod, ok);

        if (!ok) { delete prod; return false; }

        codes_prh.insert(code);
        code_to_name_prh[code] = name;
        return true;
    }

    bool deleteByCode(string code) {
        if (!code_to_name_prh.count(code)) return false;

        string name = code_to_name_prh[code];
        bool deleted = false;
        root_prh = deleteByName(root_prh, name, deleted);

        if (deleted) {
            codes_prh.erase(code);
            code_to_name_prh.erase(code);
        }
        return deleted;
    }

    int deleteExpired(string today) {
        vector<string> expired;
        collectExpired(root_prh, today, expired);
        int removed = 0;

        for (string code : expired)
            if (deleteByCode(code)) removed++;

        return removed;
    }

    void display() {
        cout << "
--- Inventory (Sorted by Name) ---
";
        inorder(root_prh);
    }
};

int main() {
    Inventory_prh inv;
    int ch;

    while (true) {
        cout << "
1. Insert Product
";
        cout << "2. Display Inventory
";
        cout << "3. Delete Product by Code
";
        cout << "4. Delete Expired Products
";
        cout << "5. Exit
";
        cout << "Enter choice: ";
        cin >> ch;

        if (ch == 1) {
            string code, name, recv, exp;
            double price;
            int qty;

            cout << "Enter Code: ";
            cin >> code;
            cout << "Enter Name: ";
            cin >> name;
            cout << "Enter Price: ";
            cin >> price;
            cout << "Enter Quantity: ";
            cin >> qty;
            cout << "Enter Date Received: ";
            cin >> recv;
            cout << "Enter Expiry Date: ";
            cin >> exp;

            if (inv.insert(code, name, price, qty, recv, exp))
                cout << "Inserted Successfully
";
            else
                cout << "Insert Failed (Duplicate Code/Name)
";
        }
        else if (ch == 2) inv.display();
        else if (ch == 3) {
            string code;
            cout << "Enter Product Code: ";
            cin >> code;
            cout << (inv.deleteByCode(code) ? "Deleted
" : "Not Found
");
        }
        else if (ch == 4) {
            string today; 
            cout << "Enter Today (YYYY-MM-DD): ";
            cin >> today;
            int n = inv.deleteExpired(today);
            cout << "Deleted " << n << " expired products.
";
        }
        else break;
    }
    return 0;
}
```

---

# 6. Sample Output

### **Insert**
```
Enter Code: P01
Enter Name: Milk
Enter Price: 45
Enter Quantity: 10
Enter Date Received: 2025-01-01
Enter Expiry Date: 2025-01-10
Inserted Successfully
```

### **Display**
```
--- Inventory (Sorted by Name) ---
P02      Bread      40    20   2025-01-05   2025-01-08
P01      Milk       45    10   2025-01-01   2025-01-10
```

### **Delete by Code**
```
Enter Product Code: P02
Deleted
```

### **Delete Expired**
```
Enter Today: 2025-01-12
Deleted 2 expired products.
```

---

# 7. Memory Management & Edge Cases
✔ Handles deletion of:  
- Leaf nodes  
- Single-child nodes  
- Two-child nodes (successor replacement)  

✔ Prevents:  
- Duplicate product codes  
- Duplicate product names  

✔ Fully frees memory using postorder destructor  

---

# 8. Conclusion
This assignment successfully demonstrates how BSTs can be used to manage and delete inventory records based on product code and expiry criteria.  

---

# 9. References
- C++ Standard Library  
- Binary Search Tree deletion algorithms  
- ISO date string lexicographical comparison  

