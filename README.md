"""
Author:  Maika Innocent
Date written: 09/22/24
GUI application designed to help users manage their monthly budgets.
The program allows users to track their income and expenses for each month, categorized by specific expense types.

"""


from tkinter import *  # Importing Tkinter for GUI elements
from tkinter import messagebox  # Importing messagebox for error handling
from PIL import ImageTk, Image  # Importing PIL for handling image loading and display

# Create the main window
root = Tk()
root.title("Budget Tracker")
root.configure(bg='light blue')  # Set the background color (light blue)

# Initialize balance and transaction data for each month
balance_per_month = {
    "January": 0, "February": 0, "March": 0, "April": 0,
    "May": 0, "June": 0, "July": 0, "August": 0,
    "September": 0, "October": 0, "November": 0, "December": 0
}
transactions_per_month = {month: [] for month in balance_per_month.keys()}

# Create a second window to show summary
def open_summary_window():
    """Open a new window to display the summary of budget for all months."""
    summary_window = Toplevel(root)  # Create a new window
    summary_window.title("Budget Summary")
    summary_window.configure(bg='#e0f7fa')  # Set background color of the summary window

    summary_label = Label(summary_window, text="Budget Summary for All Months", font=("Arial", 14), bg='#e0f7fa')
    summary_label.pack(pady=10)  # Add a label for the summary

    summary_text = Text(summary_window, width=50, height=15)  # Create a Text widget to display the summary
    summary_text.pack()

    # Loop through each month and display its balance in the text widget
    for month, balance in balance_per_month.items():
        summary_text.insert(END, f"{month}: ${balance:.2f}\n")  # Insert balance details for each month

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
    if validate_entries():
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
    if validate_entries():
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

# Function to exit the application
def exit_app():
    root.quit()

# Function to validate entry fields
def validate_entries():
    if not amount_entry.get():
        messagebox.showerror("Input Error", "Amount field cannot be empty.")
        return False
    try:
        float(amount_entry.get())
    except ValueError:
        messagebox.showerror("Input Error", "Please enter a valid number.")
        return False
    return True

# Load and display images with alternative text
# Create a frame to hold both images side by side
image_frame = Frame(root)
image_frame.pack(pady=5)

# Load and display the first image (expenses_ image)
image1 = ImageTk.PhotoImage(Image.open(r"C:\Users\innoc\OneDrive\Pictures\expenses_.png"))
image_label1 = Label(image_frame, image=image1)
image_label1.pack(side=LEFT, padx=5)  # Pack to the left with some padding

# Load and display the second image (money image)
image2 = ImageTk.PhotoImage(Image.open(r"C:\Users\innoc\OneDrive\Pictures\money.png"))
image_label2 = Label(image_frame, image=image2)
image_label2.pack(side=LEFT, padx=5)  # Pack to the left with some padding

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

# Create buttons for adding income, expenses, and viewing the summary
income_button = Button(root, text="Add Income", command=add_income)
income_button.pack(side=LEFT, padx=5, pady=5)

expense_button = Button(root, text="Add Expense", command=add_expense)
expense_button.pack(side=LEFT, padx=5, pady=5)

summary_button = Button(root, text="View Summary", command=open_summary_window)
summary_button.pack(side=LEFT, padx=5, pady=5)

exit_button = Button(root, text="Exit", command=exit_app)
exit_button.pack(side=RIGHT, padx=5, pady=5)

# Create a label for balance display
balance_label = Label(root, text="Balance for January: $0.00")
balance_label.pack()

# Create a listbox to show the transaction history
transaction_listbox = Listbox(root, width=40, height=10)
transaction_listbox.pack(padx=5, pady=5)

# Start the GUI event loop
root.mainloop()

