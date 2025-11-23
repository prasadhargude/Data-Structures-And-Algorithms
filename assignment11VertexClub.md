# Assignment 11: Vertex Club Membership Management (Singly Linked List)

**Author:** Prasad Hargude  

## 📌 Problem Statement  
The Department of Computer Engineering has a student club named **‘Vertex Club’** for second, third, and final year students.  
- The **first member** is the **President**.  
- The **last member** is the **Secretary**.  

We need to design a C++ program using **Singly Linked List** to:  
- Add/Delete members (President, Secretary, or normal member).  
- Count total members.  
- Display the list of members.  
- Search a member by PRN.  
- Sort the members by PRN.  
- Reverse the list.  
- Concatenate two club lists.  

---

## 📘 Algorithm  

### 1. Add President  
- Create a new node.  
- Insert it at the beginning of the list.  

### 2. Add Secretary  
- Create a new node.  
- Traverse to the end of the list.  
- Insert the node at the end.  

### 3. Add Member  
- Create a new node.  
- Insert it **after President** (second position).  

### 4. Delete by PRN  
- If PRN matches head → delete head.  
- Else traverse the list to find PRN.  
- Delete the node by adjusting links.  

### 5. Count Members  
- Initialize `cnt = 0`.  
- Traverse list while incrementing counter.  

### 6. Display Members  
- Traverse list and print `[PRN, Name]`.  

### 7. Search by PRN  
- Traverse list.  
- If PRN matches, print details.  

### 8. Sort by PRN  
- Apply **Bubble Sort** swapping PRNs and names.  

### 9. Reverse List  
- Use 3-pointer method (`prev`, `curr`, `next`) to reverse links.  

### 10. Concatenate  
- Traverse to last node of List A.  
- Point it to head of List B.  

---

## 📖 Theory  

- **Singly Linked List** consists of nodes with two fields:  
  1. `PRN` and `Name` (data).  
  2. Pointer to the next node.  

- **Head pointer** points to the first node (President).  
- Last node (Secretary) always points to `NULL`.  
- This structure allows **dynamic memory allocation** and efficient insertion/deletion.  

---

## ⚙️ Operations Implemented  

1. **Add President** → Insert at beginning.  
2. **Add Secretary** → Insert at end.  
3. **Add Member** → Insert after President.  
4. **Delete Member** → By PRN.  
5. **Count Members**.  
6. **Display Members**.  
7. **Search Member** → By PRN.  
8. **Sort Members** → Bubble Sort on PRN.  
9. **Reverse List** → Iterative reversal.  
10. **Concatenate Lists** → Join Club B to Club A.  

---
## 💻 Program (C++ with Functions)

```cpp
#include <iostream>
#include <string>
using namespace std;

class Node_prh {
public:
    int prn_prh;
    string name_prh;
    Node_prh* next_prh;

    Node_prh(int prn, string name) {
        prn_prh = prn;
        name_prh = name;
        next_prh = nullptr;
    }
};

class SinglyLinkedList_prh {
private:
    Node_prh* head_prh;

public:
    SinglyLinkedList_prh() {
        head_prh = nullptr;
    }

    // Add President
    void addPresident_prh(int prn, string name) {
        Node_prh* newNode_prh = new Node_prh(prn, name);
        newNode_prh->next_prh = head_prh;
        head_prh = newNode_prh;
    }

    // Add Secretary
    void addSecretary_prh(int prn, string name) {
        Node_prh* newNode_prh = new Node_prh(prn, name);
        if (head_prh == nullptr) {
            head_prh = newNode_prh;
            return;
        }
        Node_prh* temp_prh = head_prh;
        while (temp_prh->next_prh != nullptr)
            temp_prh = temp_prh->next_prh;
        temp_prh->next_prh = newNode_prh;
    }

    // Add Member after President
    void addMember_prh(int prn, string name) {
        if (head_prh == nullptr) {
            cout << "Add President first!\n";
            return;
        }
        Node_prh* newNode_prh = new Node_prh(prn, name);
        newNode_prh->next_prh = head_prh->next_prh;
        head_prh->next_prh = newNode_prh;
    }

    // Delete by PRN
    void deleteByPRN_prh(int prn) {
        if (head_prh == nullptr) {
            cout << "List empty!\n";
            return;
        }
        if (head_prh->prn_prh == prn) {
            Node_prh* temp = head_prh;
            head_prh = head_prh->next_prh;
            delete temp;
            cout << "Deleted successfully!\n";
            return;
        }
        Node_prh* curr = head_prh;
        Node_prh* prev = nullptr;
        while (curr != nullptr && curr->prn_prh != prn) {
            prev = curr;
            curr = curr->next_prh;
        }
        if (curr == nullptr) {
            cout << "PRN not found!\n";
            return;
        }
        prev->next_prh = curr->next_prh;
        delete curr;
        cout << "Deleted successfully!\n";
    }

    // Count
    int count_prh() {
        int cnt = 0;
        Node_prh* temp = head_prh;
        while (temp != nullptr) {
            cnt++;
            temp = temp->next_prh;
        }
        return cnt;
    }

    // Display
    void display_prh() {
        if (head_prh == nullptr) {
            cout << "No members!\n";
            return;
        }
        Node_prh* temp = head_prh;
        cout << "Club Members:\n";
        while (temp != nullptr) {
            cout << "[PRN: " << temp->prn_prh << ", Name: " << temp->name_prh << "] -> ";
            temp = temp->next_prh;
        }
        cout << "NULL\n";
    }

    // Search
    void searchByPRN_prh(int prn) {
        Node_prh* temp = head_prh;
        while (temp != nullptr) {
            if (temp->prn_prh == prn) {
                cout << "Found -> PRN: " << temp->prn_prh << ", Name: " << temp->name_prh << "\n";
                return;
            }
            temp = temp->next_prh;
        }
        cout << "Not found!\n";
    }

    // Sort
    void sortByPRN_prh() {
        if (head_prh == nullptr) return;
        for (Node_prh* i = head_prh; i->next_prh != nullptr; i = i->next_prh) {
            for (Node_prh* j = i->next_prh; j != nullptr; j = j->next_prh) {
                if (i->prn_prh > j->prn_prh) {
                    swap(i->prn_prh, j->prn_prh);
                    swap(i->name_prh, j->name_prh);
                }
            }
        }
        cout << "Sorted successfully!\n";
    }

    // Reverse
    void reverse_prh() {
        Node_prh* prev = nullptr;
        Node_prh* curr = head_prh;
        Node_prh* next = nullptr;
        while (curr != nullptr) {
            next = curr->next_prh;
            curr->next_prh = prev;
            prev = curr;
            curr = next;
        }
        head_prh = prev;
        cout << "List reversed!\n";
    }

    // Concatenate
    void concatenate_prh(SinglyLinkedList_prh& other) {
        if (head_prh == nullptr) {
            head_prh = other.head_prh;
            return;
        }
        Node_prh* temp = head_prh;
        while (temp->next_prh != nullptr)
            temp = temp->next_prh;
        temp->next_prh = other.head_prh;
        cout << "Concatenated successfully!\n";
    }
};

// ---------------- MAIN ----------------
int main() {
    SinglyLinkedList_prh clubA_prh, clubB_prh;
    int choice, prn;
    string name;

    do {
        cout << "\n--- Vertex Club Menu ---\n";
        cout << "1. Add President\n";
        cout << "2. Add Secretary\n";
        cout << "3. Add Member\n";
        cout << "4. Delete by PRN\n";
        cout << "5. Count Members\n";
        cout << "6. Display Members\n";
        cout << "7. Search by PRN\n";
        cout << "8. Sort by PRN\n";
        cout << "9. Reverse List\n";
        cout << "10. Concatenate Club B into Club A\n";
        cout << "0. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
        case 1:
            cout << "Enter PRN & Name of President: ";
            cin >> prn >> name;
            clubA_prh.addPresident_prh(prn, name);
            break;
        case 2:
            cout << "Enter PRN & Name of Secretary: ";
            cin >> prn >> name;
            clubA_prh.addSecretary_prh(prn, name);
            break;
        case 3:
            cout << "Enter PRN & Name of Member: ";
            cin >> prn >> name;
            clubA_prh.addMember_prh(prn, name);
            break;
        case 4:
            cout << "Enter PRN to delete: ";
            cin >> prn;
            clubA_prh.deleteByPRN_prh(prn);
            break;
        case 5:
            cout << "Total Members: " << clubA_prh.count_prh() << "\n";
            break;
        case 6:
            clubA_prh.display_prh();
            break;
        case 7:
            cout << "Enter PRN to search: ";
            cin >> prn;
            clubA_prh.searchByPRN_prh(prn);
            break;
        case 8:
            clubA_prh.sortByPRN_prh();
            break;
        case 9:
            clubA_prh.reverse_prh();
            break;
        case 10:
            cout << "Adding dummy Club B (2 members)...\n";
            clubB_prh.addPresident_prh(200, "DummyPres");
            clubB_prh.addSecretary_prh(201, "DummySec");
            clubA_prh.concatenate_prh(clubB_prh);
            break;
        case 0:
            cout << "Exiting...\n";
            break;
        default:
            cout << "Invalid choice!\n";
        }
    } while (choice != 0);

    return 0;
}

```
---
## 🖥️ Sample Output  

```
--- Vertex Club Menu ---
1. Add President
2. Add Secretary
3. Add Member
4. Delete by PRN
5. Count Members
6. Display Members
7. Search by PRN
8. Sort by PRN
9. Reverse List
10. Concatenate Club B into Club A
0. Exit
Enter choice: 1
Enter PRN & Name of President: 101 John

Enter choice: 2
Enter PRN & Name of Secretary: 105 Alice

Enter choice: 3
Enter PRN & Name of Member: 103 Bob

Enter choice: 6
Club Members:
[PRN: 101, Name: John] -> [PRN: 103, Name: Bob] -> [PRN: 105, Name: Alice] -> NULL

Enter choice: 5
Total Members: 3

Enter choice: 7
Enter PRN to search: 103
Found -> PRN: 103, Name: Bob

Enter choice: 8
Sorted successfully!

Enter choice: 9
List reversed!

Enter choice: 10
Adding dummy Club B (2 members)...
Concatenated successfully!

Enter choice: 0
Exiting...
```

---

## 🔄 Dry Run Diagram  

### Example:  
Insert **President (101, John)** → **Member (103, Bob)** → **Secretary (105, Alice)**  

```
Head
 ↓
[PRN: 101 | John] -> [PRN: 103 | Bob] -> [PRN: 105 | Alice] -> NULL
```

### After Reversing:  

```
Head
 ↓
[PRN: 105 | Alice] -> [PRN: 103 | Bob] -> [PRN: 101 | John] -> NULL
```

### After Concatenating Club B (200, DummyPres → 201, DummySec):  

```
Club A + Club B:
[101 | John] -> [103 | Bob] -> [105 | Alice] -> [200 | DummyPres] -> [201 | DummySec] -> NULL
```

---

✅ **End of Document**
