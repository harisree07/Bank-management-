```python

# BANK SYSTEM USING MYSQL CONNECTOR

import mysql.connector

mydb = mysql.connector.connect(host="localhost", user="root", passwd="root")
mycursor = mydb.cursor()

mycursor.execute("create database if not exists bank")
mycursor.execute("use bank")
mycursor.execute("create table if not exists bank_table(accno int primary key, name varchar(25), mob bigint, address varchar(25), balance float)")
print("Welcome to Bank")
print("1.New account")
print("2.Deposit amount")
print("3.Withdraw amount")
print("4.Display account")
print("5.Exit")

def menu():
    print("Enter your choice:")
menu()

def newacc():
    accno = int(input("Enter account number: "))
    name = input("Enter name: ")
    mob = int(input("Enter mobile no: "))
    address = input("Enter address: ")
    balance = float(input("Enter balance: "))
    val = (accno, name, mob, address, balance)
    sql = "insert into bank_table values(%s, %s, %s, %s, %s)"
    mycursor.execute(sql, val)
    mydb.commit()
    print("Account created successfully! Account number is:", accno)

def deposit():
    accno = int(input("Enter account number: "))
    amt = float(input("Enter amount to be deposited: "))
    mycursor.execute("select balance from bank_table where accno=%s", (accno,))
    result = mycursor.fetchone()
    total = result[0] + amt
    mycursor.execute("update bank_table set balance=%s where accno=%s", (total, accno))
    mydb.commit()
    print("Amount has been deposited successfully!")

def withdraw():
    accno = int(input("Enter account number: "))
    amt = float(input("Enter amount to be withdrawn: "))
    mycursor.execute("select balance from bank_table where accno=%s", (accno,))
    result = mycursor.fetchone()
    if result[0] >= amt:
        total = result[0] - amt
        mycursor.execute("update bank_table set balance=%s where accno=%s", (total, accno))
        mydb.commit()
        print("Amount has been withdrawn successfully!")
    else:
        print("Insufficient balance!")

def display():
    accno = int(input("Enter account number: "))
    mycursor.execute("select * from bank_table where accno=%s", (accno,))
    result = mycursor.fetchone()
    print("Account number:", result[0])
    print("Name:", result[1])
    print("Mobile number:", result[2])
    print("Address:", result[3])
    print("Balance:", result[4])

while True:
    ch = int(input("Enter your choice: "))
    if ch == 1:
        newacc()
    elif ch == 2:
        deposit()
    elif ch == 3:
        withdraw()
    elif ch == 4:
        display()
    elif ch == 5:
        break
    else:
        print("Invalid choice")
