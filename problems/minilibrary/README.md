# 📚 MiniLibrary (Version 2.1 Upgrade)

MiniLibrary has evolved from a simple book tracker into a robust **Borrowing Management System**. The latest update focuses on user accountability, borrowing history, and automated policy enforcement.

---

## 🚀 What's New in v2.1?

The major shift in this version is the transition from just tracking "what books we have" to **"who has them and for how long."**

### 1. Enhanced Borrower Tracking
In previous versions, we only tracked the book's status. Now, every borrowed book is linked to a **Person's Name**. 
- The `books.dat` schema has been expanded to store the borrower's identity and the exact date of the transaction.

### 2. The 14-Day Loan Policy
To ensure fair use of library resources, a strict **14-day return policy** is now hardcoded into the system logic.
- **Borrowing:** When a book is issued, the system automatically calculates and displays a due date.
- **Returning:** Upon return, the system compares the current date with the due date to detect overdue items.

### 3. Automated Penalty & Blacklist System
We've introduced a database-backed strike system to handle late returns:
- **Late Count Tracking:** Every time a user returns a book after the 14-day window, their unique "late count" is incremented in `borrowers.dat`.
- **The Blacklist (3-Strike Rule):** If a user accumulates **3 late returns**, the system automatically flags them.
- **Access Control:** Blacklisted users are strictly prohibited from borrowing any further books until their status is manually cleared.

---

## 📂 Updated Data Schema

The system now manages internal states across four specialized files:

| File | Responsibility |
| :--- | :--- |
| `books.dat` | Stores book metadata + **Current Borrower** + **Loan Date**. |
| `borrowers.dat` | Stores a history of **Late Return Counts** per user. |
| `blacklist.dat` | A list of users banned from borrowing. |
| `requests.dat` | Aggregates community requests for new titles. |

---

## 🛠️ Updated Command Logic

### `minilibrary borrow <id> <person>`
Now requires a name. It checks if the person is in `blacklist.dat` before allowing the transaction.

### `minilibrary return <id>`
Does more than just updating status. It performs a "Policy Check":
```python
if today > (borrow_date + 14_days):
    update_late_count(person)
    if late_count >= 3:
        add_to_blacklist(person)
