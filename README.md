# ShulaTech_Solutions_Password-Generator-Python-Tkinter-
This is 3rd Task 

import tkinter as tk
from tkinter import messagebox
import random
import string
import sqlite3

# Database setup
conn = sqlite3.connect("passwords.db")
cursor = conn.cursor()
cursor.execute("""
    CREATE TABLE IF NOT EXISTS passwords (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        password TEXT NOT NULL
    )
""")
conn.commit()

# Function to generate password
def generate_password():
    length = length_var.get()
    use_special = special_var.get()

    if length < 4:
        messagebox.showwarning("Warning", "Password length should be at least 4 characters!")
        return

    characters = string.ascii_letters + string.digits
    if use_special:
        characters += string.punctuation

    password = ''.join(random.choice(characters) for _ in range(length))
    password_var.set(password)

# Function to copy password to clipboard
def copy_to_clipboard():
    root.clipboard_clear()
    root.clipboard_append(password_var.get())
    root.update()
    messagebox.showinfo("Success", "Password copied to clipboard!")

# Function to save password
def save_password():
    password = password_var.get()
    if password:
        cursor.execute("INSERT INTO passwords (password) VALUES (?)", (password,))
        conn.commit()
        messagebox.showinfo("Success", "Password saved successfully!")
    else:
        messagebox.showwarning("Warning", "No password to save!")

# GUI Setup
root = tk.Tk()
root.title("Password Generator")
root.geometry("400x300")

# Widgets
tk.Label(root, text="Password Length:").pack(pady=5)
length_var = tk.IntVar(value=8)
length_entry = tk.Entry(root, textvariable=length_var, width=5)
length_entry.pack(pady=5)

special_var = tk.BooleanVar(value=True)
special_check = tk.Checkbutton(root, text="Include Special Characters", variable=special_var)
special_check.pack(pady=5)

generate_button = tk.Button(root, text="Generate Password", command=generate_password)
generate_button.pack(pady=5)

password_var = tk.StringVar()
password_entry = tk.Entry(root, textvariable=password_var, width=30, font=("Arial", 12), justify="center")
password_entry.pack(pady=5)

copy_button = tk.Button(root, text="Copy to Clipboard", command=copy_to_clipboard)
copy_button.pack(pady=5)

save_button = tk.Button(root, text="Save Password", command=save_password)
save_button.pack(pady=5)

# Run GUI
root.mainloop()
