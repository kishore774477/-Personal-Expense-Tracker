[Personal Expense Tracker.txt.txt](https://github.com/user-attachments/files/18605441/Personal.Expense.Tracker.txt.txt)Personal Expense Tracker:

Code:

import json
from datetime import datetime

# Function to load existing expenses from the file
def load_expenses():
    """
    Load expenses from the JSON file. If the file doesn't exist or is empty, return an empty dictionary.
    """
    try:
        with open('expenses.json', 'r') as file:
            return json.load(file)  # Load and return the JSON data as a dictionary
    except (FileNotFoundError, json.JSONDecodeError):
        return {"expenses": [], "budget": 0}  # Return an empty structure if file doesn't exist or is corrupted

# Function to save expenses to the file
def save_expenses(expenses):
    """
    Save the current expenses data to a JSON file.
    """
    with open('expenses.json', 'w') as file:
        json.dump(expenses, file, indent=4)  # Write expenses data to the file with pretty formatting

# Function to set or update the monthly budget
def set_budget(expenses):
    """
    Set or update the monthly budget. This allows the user to set their spending limit.
    """
    try:
        budget = float(input("Enter your budget for this month: $"))
        expenses['budget'] = budget  # Update the budget in the expenses dictionary
        print(f"Budget set to ${budget:.2f}")  # Confirm the new budget
        save_expenses(expenses)  # Save the updated budget to the file
    except ValueError:
        print("Invalid input. Please enter a valid number.")  # Handle non-numeric input

# Function to add a new expense
def add_expense(expenses):
    """
    Add a new expense with a category, amount, and date.
    """
    category = input("Enter expense category (e.g., food, transport): ").lower()  # Get the expense category from the user
    try:
        amount = float(input("Enter amount: $"))  # Get the amount spent
        date = datetime.now().strftime('%Y-%m-%d %H:%M:%S')  # Get the current date and time

        # Add a new entry to the expenses list
        expenses['expenses'].append({'category': category, 'amount': amount, 'date': date})
        
        # Save the updated expenses to the file
        save_expenses(expenses)
        
        print(f"Added ${amount:.2f} to {category} on {date}.")  # Show a confirmation message
        
        # Calculate total spending to check if it exceeds the budget
        total_spent = sum(expense['amount'] for expense in expenses['expenses'])
        
        # Check if the total spending exceeds the set budget
        if total_spent > expenses['budget']:
            print(f"Warning: You have exceeded your budget of ${expenses['budget']:.2f}. Total spent: ${total_spent:.2f}")
        
    except ValueError:
        print("Invalid amount entered. Please try again.")  # Handle invalid amount input

# Function to view all expenses, sorted by category or amount
def view_expenses(expenses, sort_by='category'):
    """
    Display all expenses. Optionally, sort by category or amount.
    """
    if not expenses['expenses']:  # Check if there are no expenses
        print("No expenses recorded yet.")
        return
    
    # Sort expenses by category or amount (default: category)
    if sort_by == 'category':
        expenses['expenses'].sort(key=lambda x: x['category'])  # Sort by category alphabetically
    elif sort_by == 'amount':
        expenses['expenses'].sort(key=lambda x: x['amount'], reverse=True)  # Sort by amount (largest first)
    
    print("\n--- Your Expenses ---")
    total_spent = 0
    # Loop through each expense and display its details
    for expense in expenses['expenses']:
        print(f"{expense['category'].capitalize()} | ${expense['amount']:.2f} | {expense['date']}")
        total_spent += expense['amount']  # Add to the total spent
    
    # Show the total spending
    print(f"\nTotal spent: ${total_spent:.2f}")

# Function to delete an expense by category
def delete_expense(expenses):
    """
    Delete an expense based on the category. If the category exists, remove it.
    """
    category = input("Enter the category of expense to delete: ").lower()  # Get category to delete
    found = False  # Flag to check if the category is found
    for expense in expenses['expenses']:
        if expense['category'] == category:  # Check if category matches
            expenses['expenses'].remove(expense)  # Remove the matching expense
            found = True
            print(f"Deleted {category} expenses.")  # Confirmation message
            save_expenses(expenses)  # Save updated expenses to the file
            break
    
    if not found:  # If the category was not found
        print("Category not found.")

# Main function to run the expense tracker
def main():
    """
    Main program function that handles the user's interactions with the expense tracker.
    """
    expenses = load_expenses()  # Load the saved expenses from the file

    while True:
        # Display the main menu with options
        print("\n--- Personal Expense Tracker ---")
        print("1. Add Expense")
        print("2. View Expenses")
        print("3. Delete Expense")
        print("4. Set or Update Budget")
        print("5. Exit")
        
        # Get the user's choice
        choice = input("Choose an option: ")

        if choice == '1':
            add_expense(expenses)  # Call the add_expense function
        elif choice == '2':
            sort_option = input("Sort by (category/amount)? ").lower()  # Ask how to sort
            if sort_option not in ['category', 'amount']:
                print("Invalid sort option. Sorting by category by default.")  # Default sort by category
                sort_option = 'category'
            view_expenses(expenses, sort_by=sort_option)  # View the expenses
        elif choice == '3':
            delete_expense(expenses)  # Delete an expense
        elif choice == '4':
            set_budget(expenses)  # Set or update the budget
        elif choice == '5':
            print("Goodbye!")  # Exit the program
            break
        else:
            print("Invalid choice. Please try again.")  # Handle invalid choices

if __name__ == "__main__":
    main()  # Run the main function when the script is executed

