# Budget-Tracker
from tkinter import *
from tkinter import messagebox

# Create the main window
root = Tk()
root.title("Budget Tracker")

# Initialize balance and transaction data for each month
balance_per_month = {
    "January": 0, "February": 0, "March": 0, "April": 0,
    "May": 0, "June": 0, "July": 0, "August": 0,
    "September": 0, "October": 0, "November": 0, "December": 0
}
transactions_per_month = {month: [] for month in balance_per_month.keys()}


# Function to update the balance and transaction list for the selected month
def update_balance_and_list():
    selected_month = month_var.get()
    balance_label.config(text=f"Balance for {selected_month}: ${balance_per_month[selected_month]:.2f}")

    # Update the listbox with new transactions for the selected month
    transaction_listbox.delete(0, END)
    for transaction in transactions_per_month[selected_month]:
        transaction_listbox.insert(END, transaction)


# Function to add income to the selected month
def add_income():
    try:
        amount = float(amount_entry.get())
        if amount <= 0:
            raise ValueError("Income must be greater than 0.")
        selected_month = month_var.get()
        balance_per_month[selected_month] += amount
        transactions_per_month[selected_month].append(f"Income: +${amount:.2f}")
        update_balance_and_list()
    except ValueError:
        messagebox.showerror("Invalid Input", "Please enter a valid amount.")


# Function to add expense with category to the selected month
def add_expense():
    try:
        amount = float(amount_entry.get())
        if amount <= 0:
            raise ValueError("Expense must be greater than 0.")
        selected_month = month_var.get()
        category = expense_category.get()
        balance_per_month[selected_month] -= amount
        transactions_per_month[selected_month].append(f"Expense: -${amount:.2f} ({category})")
        update_balance_and_list()
    except ValueError:
        messagebox.showerror("Invalid Input", "Please enter a valid amount.")


# Create a label to show the current balance for the selected month
balance_label = Label(root, text="Balance for January: $0.00", font=("Arial", 14))
balance_label.pack(pady=10)

# Create a dropdown for selecting the month
month_label = Label(root, text="Select Month:")
month_label.pack()
month_var = StringVar(root)
month_var.set("January")  # Default month
month_menu = OptionMenu(root, month_var, *balance_per_month.keys(), command=lambda _: update_balance_and_list())
month_menu.pack()

# Create a label and entry field for the amount
amount_label = Label(root, text="Enter Amount:")
amount_label.pack()
amount_entry = Entry(root)
amount_entry.pack()

# Create a dropdown for expense categories
category_label = Label(root, text="Select Expense Category:")
category_label.pack()
expense_category = StringVar(root)
expense_category.set("Select Category")  # Default value
categories = ["Food", "Transport", "Entertainment", "Bills", "Shopping", "Other"]
category_menu = OptionMenu(root, expense_category, *categories)
category_menu.pack()

# Create buttons for adding income and expenses
income_button = Button(root, text="Add Income", command=add_income)
income_button.pack(side=LEFT, padx=5, pady=5)

expense_button = Button(root, text="Add Expense", command=add_expense)
expense_button.pack(side=RIGHT, padx=5, pady=5)

# Create a listbox to show the transaction history
transaction_listbox = Listbox(root, width=40, height=10)
transaction_listbox.pack(pady=10)

# Start the GUI event loop
root.mainloop()
