# ATM.OCTANET


import tkinter as tk
from tkinter import messagebox
import datetime
import random

class ATM:
    def __init__(self, root):
        self.root = root
        self.root.title("ATM Machine Simulation")
        
        self.accounts = {}
        self.current_account = None
        self.load_sample_accounts()

        self.create_login_screen()

    def load_sample_accounts(self):
        # Sample accounts for demonstration
        self.accounts['1234'] = {'pin': '1111', 'balance': 1000, 'transactions': []}
        self.accounts['5678'] = {'pin': '2222', 'balance': 2000, 'transactions': []}

    def check_pin(self, account_number, pin):
        return self.accounts.get(account_number, {}).get('pin') == pin

    def login(self):
        account_number = self.account_number_var.get()
        pin = self.pin_var.get()

        if self.check_pin(account_number, pin):
            self.current_account = account_number
            messagebox.showinfo("Success", "Login successful!")
            self.create_main_menu()
        else:
            messagebox.showerror("Error", "Invalid account number or PIN.")
            self.current_account = None

    def balance_inquiry(self):
        balance = self.accounts[self.current_account]['balance']
        messagebox.showinfo("Balance Inquiry", f"Your current balance is: ${balance}")
        self.add_transaction("Balance inquiry")

    def cash_withdrawal(self):
        amount = float(self.amount_var.get())
        if amount > self.accounts[self.current_account]['balance']:
            messagebox.showerror("Error", "Insufficient funds!")
        else:
            self.accounts[self.current_account]['balance'] -= amount
            messagebox.showinfo("Success", f"${amount} has been withdrawn. Your new balance is: ${self.accounts[self.current_account]['balance']}")
            self.add_transaction(f"Withdrawal: ${amount}")

    def cash_deposit(self):
        amount = float(self.amount_var.get())
        self.accounts[self.current_account]['balance'] += amount
        messagebox.showinfo("Success", f"${amount} has been deposited. Your new balance is: ${self.accounts[self.current_account]['balance']}")
        self.add_transaction(f"Deposit: ${amount}")

    def change_pin(self):
        new_pin = self.new_pin_var.get()
        self.accounts[self.current_account]['pin'] = new_pin
        messagebox.showinfo("Success", "PIN has been changed successfully!")
        self.add_transaction("PIN changed")

    def transaction_history(self):
        transactions = self.accounts[self.current_account]['transactions']
        if not transactions:
            messagebox.showinfo("Transaction History", "No transactions yet.")
        else:
            history = "\n".join(transactions)
            messagebox.showinfo("Transaction History", history)

    def add_transaction(self, transaction_type):
        transaction_time = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        self.accounts[self.current_account]['transactions'].append(f"{transaction_time} - {transaction_type}")

    def money_transfer(self):
        target_account = self.target_account_var.get()
        amount = float(self.amount_var.get())
        if target_account not in self.accounts:
            messagebox.showerror("Error", "Target account does not exist.")
        elif amount > self.accounts[self.current_account]['balance']:
            messagebox.showerror("Error", "Insufficient funds!")
        else:
            self.accounts[self.current_account]['balance'] -= amount
            self.accounts[target_account]['balance'] += amount
            messagebox.showinfo("Success", f"${amount} has been transferred to account {target_account}.")
            self.add_transaction(f"Transfer: ${amount} to {target_account}")

    def create_login_screen(self):
        self.clear_screen()

        tk.Label(self.root, text="Account Number:").pack()
        self.account_number_var = tk.StringVar()
        tk.Entry(self.root, textvariable=self.account_number_var).pack()

        tk.Label(self.root, text="PIN:").pack()
        self.pin_var = tk.StringVar()
        tk.Entry(self.root, textvariable=self.pin_var, show='*').pack()

        tk.Button(self.root, text="Login", command=self.login).pack()

    def create_main_menu(self):
        self.clear_screen()

        tk.Button(self.root, text="Balance Inquiry", command=self.balance_inquiry).pack()
        tk.Button(self.root, text="Cash Withdrawal", command=self.create_withdrawal_screen).pack()
        tk.Button(self.root, text="Cash Deposit", command=self.create_deposit_screen).pack()
        tk.Button(self.root, text="Change PIN", command=self.create_change_pin_screen).pack()
        tk.Button(self.root, text="Transaction History", command=self.transaction_history).pack()
        tk.Button(self.root, text="Money Transfer", command=self.create_transfer_screen).pack()
        tk.Button(self.root, text="Logout", command=self.create_login_screen).pack()

    def create_withdrawal_screen(self):
        self.clear_screen()

        tk.Label(self.root, text="Enter amount to withdraw:").pack()
        self.amount_var = tk.StringVar()
        tk.Entry(self.root, textvariable=self.amount_var).pack()

        tk.Button(self.root, text="Withdraw", command=self.cash_withdrawal).pack()
        tk.Button(self.root, text="Back to Menu", command=self.create_main_menu).pack()

    def create_deposit_screen(self):
        self.clear_screen()

        tk.Label(self.root, text="Enter amount to deposit:").pack()
        self.amount_var = tk.StringVar()
        tk.Entry(self.root, textvariable=self.amount_var).pack()

        tk.Button(self.root, text="Deposit", command=self.cash_deposit).pack()
        tk.Button(self.root, text="Back to Menu", command=self.create_main_menu).pack()

    def create_change_pin_screen(self):
        self.clear_screen()

        tk.Label(self.root, text="Enter new PIN:").pack()
        self.new_pin_var = tk.StringVar()
        tk.Entry(self.root, textvariable=self.new_pin_var, show='*').pack()

        tk.Button(self.root, text="Change PIN", command=self.change_pin).pack()
        tk.Button(self.root, text="Back to Menu", command=self.create_main_menu).pack()

    def create_transfer_screen(self):
        self.clear_screen()

        tk.Label(self.root, text="Enter target account number:").pack()
        self.target_account_var = tk.StringVar()
        tk.Entry(self.root, textvariable=self.target_account_var).pack()

        tk.Label(self.root, text="Enter amount to transfer:").pack()
        self.amount_var = tk.StringVar()
        tk.Entry(self.root, textvariable=self.amount_var).pack()

        tk.Button(self.root, text="Transfer", command=self.money_transfer).pack()
        tk.Button(self.root, text="Back to Menu", command=self.create_main_menu).pack()

    def clear_screen(self):
        for widget in self.root.winfo_children():
            widget.destroy()

if __name__ == "__main__":
    root = tk.Tk()
    atm = ATM(root)
    root.mainloop()
