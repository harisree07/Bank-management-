```python

# main.py
import mysql.connector

myconn = mysql.connector.connect(
    host="localhost",
    user="root",
    passwd="root"
)

mycursor = myconn.cursor()

mycursor.execute("CREATE DATABASE IF NOT EXISTS bank")
mycursor.execute("USE bank")
mycursor.execute("""
    CREATE TABLE IF NOT EXISTS bank_details (
        accno INT PRIMARY KEY,
        name VARCHAR(20),
        mob_no BIGINT,
        address VARCHAR(30),
        balance FLOAT
    )
""")


def menu():
    print("\n--- Bank Management System ---")
    print("1. New account entry")
    print("2. Deposit amount")
    print("3. Withdraw amount")
    print("4. Display account")
    print("5. Exit")
    return int(input("Enter your choice: "))


def newacc():
    accno = int(input("Enter account number: "))
    name = input("Enter account holder name: ")
    mob_no = int(input("Enter mobile number: "))
    address = input("Enter address: ")
    balance = float(input("Enter balance: "))
    val = (accno, name, mob_no, address, balance)
    sql = "INSERT INTO bank_details VALUES (%s, %s, %s, %s, %s)"
    mycursor.execute(sql, val)
    myconn.commit()
    print("Account created successfully!")


def deposit():
    accno = int(input("Enter account number: "))
    amt = float(input("Enter deposit amount: "))
    mycursor.execute("SELECT balance FROM bank_details WHERE accno=%s", (accno,))
    result = mycursor.fetchone()
    if result:
        new_amt = result[0] + amt
        mycursor.execute("UPDATE bank_details SET balance=%s WHERE accno=%s", (new_amt, accno))
        myconn.commit()
        print("Amount deposited successfully!")
    else:
        print("Account number not found.")


def withdraw():
    accno = int(input("Enter account number: "))
    amt = float(input("Enter withdrawal amount: "))
    mycursor.execute("SELECT balance FROM bank_details WHERE accno=%s", (accno,))
    result = mycursor.fetchone()
    if result:
        if result[0] >= amt:
            new_amt = result[0] - amt
            mycursor.execute("UPDATE bank_details SET balance=%s WHERE accno=%s", (new_amt, accno))
            myconn.commit()
            print("Amount withdrawn successfully!")
        else:
            print("Insufficient balance.")
    else:
        print("Account number not found.")


def display():
    accno = int(input("Enter account number: "))
    mycursor.execute("SELECT * FROM bank_details WHERE accno=%s", (accno,))
    result = mycursor.fetchone()
    if result:
        print("\n--- Account Details ---")
        print("Account number:", result[0])
        print("Name:", result[1])
        print("Mobile number:", result[2])
        print("Address:", result[3])
        print("Balance:", result[4])
    else:
        print("Account number not found.")

if __name__ == "__main__":
    while True:
        try:
            ch = menu()
            if ch == 1:
                newacc()
            elif ch == 2:
                deposit()
            elif ch == 3:
                withdraw()
            elif ch == 4:
                display()
            elif ch == 5:
                print("Exiting... Thank you!")
                break
            else:
                print("Invalid choice. Try again.")
        except Exception as e:
            print("Error:", e)
