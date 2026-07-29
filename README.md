import sqlite3

# Connect to database
conn = sqlite3.connect("expenses.db")
cursor = conn.cursor()

# Create table
cursor.execute("""
CREATE TABLE IF NOT EXISTS expenses(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    category TEXT,
    item TEXT,
    amount REAL
)
""")
conn.commit()


def add_expense():
    category = input("Enter Category: ")
    item = input("Enter Expense Name: ")

    try:
        amount = float(input("Enter Amount: "))
    except ValueError:
        print("Invalid amount!")
        return

    cursor.execute(
        "INSERT INTO expenses(category, item, amount) VALUES (?, ?, ?)",
        (category, item, amount)
    )
    conn.commit()
    print("\nExpense Added Successfully!")


def view_expenses():
    cursor.execute("SELECT * FROM expenses")
    rows = cursor.fetchall()

    if not rows:
        print("\nNo expenses found.")
        return

    print("\n-------------------------------")
    print("ID | Category | Item | Amount")
    print("-------------------------------")

    for row in rows:
        print(row)


def delete_expense():
    expense_id = input("Enter Expense ID to delete: ")

    cursor.execute("DELETE FROM expenses WHERE id = ?", (expense_id,))
    conn.commit()

    if cursor.rowcount > 0:
        print("Expense Deleted Successfully!")
    else:
        print("Expense ID not found.")


def total_expense():
    cursor.execute("SELECT SUM(amount) FROM expenses")
    total = cursor.fetchone()[0]

    if total is None:
        total = 0

    print(f"\nTotal Expense = ₹{total:.2f}")


# Main Menu
while True:
    print("\n========== Expense Tracker ==========")
    print("1. Add Expense")
    print("2. View Expenses")
    print("3. Delete Expense")
    print("4. Total Expenses")
    print("5. Exit")

    choice = input("Enter your choice: ")

    if choice == "1":
        add_expense()

    elif choice == "2":
        view_expenses()

    elif choice == "3":
        delete_expense()

    elif choice == "4":
        total_expense()

    elif choice == "5":
        print("Thank you for using Expense Tracker!")
        break

    else:
        print("Invalid Choice! Please try again.")

conn.close()
