# 📚 Library Management System (SQL Project - P2)

### 🧩 Project Overview
This project demonstrates the implementation of a **Library Management System** using **SQL**.  
It covers database creation, CRUD operations, CTAS, stored procedures, and advanced SQL queries.

- **Project Title:** Library Management System  
- **Database Name:** `library_db`  
 

## 🎯 **Objectives**
- **Database Setup:** Create and populate tables for branches, employees, members, books, issued and return status.  
- **CRUD Operations:** Perform Create, Read, Update, and Delete actions.  
- **CTAS (Create Table As Select):** Generate new tables from query results.  
- **Advanced Queries:** Develop analytical SQL queries for reporting and insights.  


## 🧱 **Project Structure**

### 1️⃣ Database Setup
Created the database `library_db` with the following tables:
- **branch** – stores branch details  
- **employees** – employee details linked to branches  
- **members** – library members with registration details  
- **books** – book catalog with rental info  
- **issued_status** – records of books issued to members  
- **return_status** – records of returned books  

```sql
CREATE DATABASE library_db;
CREATE TABLE branch ( branch_id VARCHAR(10) PRIMARY KEY,
            manager_id VARCHAR(10),
            branch_address VARCHAR(30),
            contact_no VARCHAR(15));

CREATE TABLE employees ( emp_id VARCHAR(10) PRIMARY KEY,
            emp_name VARCHAR(30),
            position VARCHAR(30),
            salary DECIMAL(10,2),
            branch_id VARCHAR(10),
            FOREIGN KEY (branch_id) REFERENCES  branch(branch_id));

CREATE TABLE members (member_id VARCHAR(10) PRIMARY KEY,
            member_name VARCHAR(30),
            member_address VARCHAR(30),
            reg_date DATE);

CREATE TABLE books ( isbn VARCHAR(50) PRIMARY KEY,
            book_title VARCHAR(80),
            category VARCHAR(30),
            rental_price DECIMAL(10,2),
            status VARCHAR(10),
            author VARCHAR(30),
            publisher VARCHAR(30));

CREATE TABLE issued_status ( issued_id VARCHAR(10) PRIMARY KEY,
            issued_member_id VARCHAR(30),
            issued_book_name VARCHAR(80),
            issued_date DATE,
            issued_book_isbn VARCHAR(50),
            issued_emp_id VARCHAR(10),
            FOREIGN KEY (issued_member_id) REFERENCES members(member_id),
            FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id),
            FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn));

CREATE TABLE return_status (         return_id VARCHAR(10) PRIMARY KEY,
            issued_id VARCHAR(30),
            return_book_name VARCHAR(80),
            return_date DATE,
            return_book_isbn VARCHAR(50),
            FOREIGN KEY (return_book_isbn) REFERENCES books(isbn));

2. CRUD Operations
Create: Inserted sample records into the books table.
Read: Retrieved and displayed data from various tables.
Update: Updated records in the employees table.
Delete: Removed records from the members table as needed.
Task 1. Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
