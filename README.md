# Ved-Upadhye-26043010103
Ved Upadhye 26043010103

import csv
import json
import os
from datetime import datetime


class ExpenseTracker:

    def __init__(self, filename="expenses.json"):
        self.filename = filename
        self.data = self._load_data()

    def _load_data(self):
        """Load persistent transaction and budget data from JSON."""
        if not os.path.exists(self.filename):
            return {"budget": 0.0, "transactions": []}
        try:
            with open(self.filename, "r") as f:
                return json.load(f)
        except (json.JSONDecodeError, IOError):
            print("Warning: Failed to load existing file. Starting fresh.")
            return {"budget": 0.0, "transactions": []}

    def _save_data(self):
        """Save data back to JSON storage."""
        with open(self.filename, "w") as f:
            json.dump(self.data, f, indent=4)

    def set_budget(self, amount: float):
        """Set or update the monthly spending threshold."""
        self.data["budget"] = round(amount, 2)
        self._save_data()
        print(
import csv
import json
import os
from datetime import datetime


class ExpenseTracker:

    def __init__(self, filename="expenses.json"):
        self.filename = filename
        self.data = self._load_data()

    def _load_data(self):
        """Load persistent transaction and budget data from JSON."""
        if not os.path.exists(self.filename):
            return {"budget": 0.0, "transactions": []}
        try:
            with open(self.filename, "r") as f:
                return json.load(f)
        except (json.JSONDecodeError, IOError):
            print("Warning: Failed to load existing file. Starting fresh.")
            return {"budget": 0.0, "transactions": []}

    def _save_data(self):
        """Save data back to JSON storage."""
        with open(self.filename, "w") as f:
            json.dump(self.data, f, indent=4)

    def set_budget(self, amount: float):
        """Set or update the monthly spending threshold."""
        self.data["budget"] = round(amount, 2)
        self._save_data()
        print(f"\n✓ Monthly budget set to ${self.data['budget']:.2f}")

    def add_transaction(
        self, t_type: str, amount: float, category: str, description: str = ""
    ):
        """Add a new income or expense entry."""
        transaction = {
            "id": len(self.data["transactions"]) + 1,
            "date": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            "type": t_type,  # 'income' or 'expense'
            "amount": round(amount, 2),
            "category": category.strip().title(),
            "description": description.strip(),
        }
        self.data["transactions"].append(transaction)
        self._save_data()
        print(
            f"\n✓ Recorded {t_type}: ${amount:.2f} under category '{transaction['category']}'"
        )
        if t_type == "expense":
            self._check_budget_warning()

    def _check_budget_warning(self):
        """Alert user if total expenses approach or exceed the monthly budget."""
        budget = self.data.get("budget", 0.0)
        if budget <= 0:
            return

        total_expense = sum(
            t["amount"]
            for t in self.data["transactions"]
            if t["type"] == "expense"
        )
        if total_expense > budget:
            print(
                f"\n⚠️  ALERT: You have exceeded your budget by ${total_expense - budget:.2f}!"
            )
            print(
                f"   Total Spent: ${total_expense:.2f} | Budget limit: ${budget:.2f}"
            )
        elif total_expense >= budget * 0.85:
            pct = (total_expense / budget) * 100
            print(
                f"\n⚠️  NOTICE: You have used {pct:.1f}% of your budget (${total_expense:.2f} / ${budget:.2f})."
            )

    def list_transactions(self, filter_type: str = None):
        """Display all recorded transactions in a tabular view."""
        txs = self.data["transactions"]
        if filter_type:
            txs = [t for t in txs if t["type"] == filter_type]

        if not txs:
            print("\nNo transaction records found.")
            return

        print("\n" + "=" * 75)
        print(
            f"{'ID':<4} | {'Date & Time':<19} | {'Type':<8} | {'Amount ($)':<10} | {'Category':<15} | {'Description'}"
        )
        print("-" * 75)
        for t in txs:
            print(
                f"{t['id']:<4} | {t['date']:<19} | {t['type'].capitalize():<8} | {t['amount']:<10.2f} | {t['category']:<15} | {t['description']}"
            )
        print("=" * 75)

    def summary_report(self):
        """Generate a summary report with income, expense, net savings, and category breakdowns."""
        txs = self.data["transactions"]
        total_income = sum(
            t["amount"] for t in txs if t["type"] == "income"
        )
        total_expense = sum(
            t["amount"] for t in txs if t["type"] == "expense"
        )
        net_savings = total_income - total_expense

        print("\n" + "=" * 45)
        print("          FINANCIAL SUMMARY REPORT           ")
        print("=" * 45)
        print(f"  Total Income    : ${total_income:10.2f}")
        print(f"  Total Expenses  : ${total_expense:10.2f}")
        print(f"  Net Savings     : ${net_savings:10.2f}")
        print(f"  Monthly Budget  : ${self.data.get('budget', 0.0):10.2f}")

        # Category Breakdown for Expenses
        categories = {}
        for t in txs:
            if t["type"] == "expense":
                cat = t["category"]
                categories[cat] = categories.get(cat, 0.0) + t["amount"]

        if categories:
            print("\n  --- Expense Distribution by Category ---")
            for cat, amt in sorted(
                categories.items(), key=lambda x: x[1], reverse=True
            ):
                pct = (amt / total_expense * 100) if total_expense > 0 else 0
                print(f"   • {cat:<15}: ${amt:8.2f} ({pct:5.1f}%)")
        print("=" * 45)

    def export_csv(self, export_filename="expense_report.csv"):
        """Export transaction data to a standard CSV file."""
        txs = self.data["transactions"]
        if not txs:
            print("\nNo records available to export.")
            return

        try:
            with open(export_filename, "w", newline="", encoding="utf-8") as f:
                writer = csv.DictWriter(
                    f,
                    fieldnames=[
                        "id",
                        "date",
                        "type",
                        "amount",
                        "category",
                        "description",
                    ],
                )
                writer.writeheader()
                writer.writerows(txs)
            print(
                f"\n✓ Successfully exported {len(txs)} transactions to '{export_filename}'"
            )
        except IOError as e:
            print(f"\n❌ Error exporting CSV: {e}")


def prompt_float(prompt_text: str) -> float:
    """Helper function to validate positive numeric input."""
    while True:
        try:
            val = float(input(prompt_text))
            if val < 0:
                print("Please enter a positive value.")
                continue
            return val
        except ValueError:
            print("Invalid input. Please enter a valid number.")


def main():
    tracker = ExpenseTracker()

    while True:
        print("\n==========================================")
        print("    PERSONAL FINANCE & EXPENSE TRACKER    ")
        print("==========================================")
        print(" 1. Add Income")
        print(" 2. Add Expense")
        print(" 3. View All Transactions")
        print(" 4. Set Monthly Budget")
        print(" 5. View Financial Summary & Category Breakdown")
        print(" 6. Export Data to CSV")
        print(" 7. Exit")
        print("==========================================")

        choice = input("Select an option (1-7): ").strip()

        if choice == "1":
            amt = prompt_float("Enter income amount ($): ")
            cat = input("Category (e.g., Salary, Freelance): ").strip()
            desc = input("Description (optional): ").strip()
            tracker.add_transaction("income", amt, cat or "General", desc)

        elif choice == "2":
            amt = prompt_float("Enter expense amount ($): ")
            cat = input(
                "Category (e.g., Rent, Groceries, Utilities): "
            ).strip()
            desc = input("Description (optional): ").strip()
            tracker.add_transaction("expense", amt, cat or "Miscellaneous", desc)

        elif choice == "3":
            tracker.list_transactions()

        elif choice == "4":
            b_amt = prompt_float("Enter monthly target budget ($): ")
            tracker.set_budget(b_amt)

        elif choice == "5":
            tracker.summary_report()

        elif choice == "6":
            filename = (
                input(
                    "Enter export filename [default: expense_report.csv]: "
                ).strip()
                or "expense_report.csv"
            )
            tracker.export_csv(filename)

        elif choice == "7":
            print("\nThank you for using Expense Tracker. Goodbye!")
            break

        else:
            print("\nInvalid selection. Please choose an option from 1 to 7.")


if __name__ == "__main__":
    main()
