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
CREATE TABLE branch (...);
CREATE TABLE employees (...);
CREATE TABLE members (...);
CREATE TABLE books (...);
CREATE TABLE issued_status (...);
CREATE TABLE return_status (...);
