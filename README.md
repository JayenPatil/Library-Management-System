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

```
### 2. CRUD Operations
-**Create**: Inserted sample records into the books table.
-**Read**: Retrieved and displayed data from various tables.
-**Update**: Updated records in the employees table.
-**Delete**: Removed records from the members table as needed.

**Task 1. Create a New Book Record**
-- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher) 
VALUES ('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```

**Task 2: Update an Existing Member's Address**
```sql
update members
set member_address = '999 New St, Suite 10'
where member_id = 'c119';
```

**Task 3: Delete a Record from the Issued Status Table**
-- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.
```sql
Delete from issued_status
where issued_id = 'IS121';
```

**Task 4: Retrieve All Books Issued by a Specific Employee**
-- Objective: Select all books issued by the employee with emp_id = 'E101'.
```sql
select issued_book_name from issued_status
where issued_emp_id = 'E104';
```
**Task 5: List Members Who Have Issued More Than One Book**
-- Objective: Use GROUP BY to find members who have issued more than one book.
```sql
select issued_emp_id, count(*) as issued_book 
from issued_status
group by issued_emp_id
having count(*) > 1;
```
## 3. CTAS (Create Table As Select)

**Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**
```sql
create table book_issued_cnt as
select b.isbn,b.book_title,count(ist.issued_id) as count_issued
from issued_status as ist
join books as b on ist.issued_book_isbn = b.isbn
group by b.isbn,b.book_title;

select * from book_issued_cnt;
```

## 4. Data Analysis & Findings
The following SQL queries were used to address specific questions:

**Task 7. Retrieve All Books in a Specific Category:**
```sql
select * from books
where category = 'History';
```

**Task 8: Find Total Rental Income by Category:**
```sql
select category,sum(rental_price) as total_renatl,
count(*) as book_count
from books as b
join issued_status as ist
on ist.issued_book_isbn = b.isbn
group by category;
```

**Task 9:List Members Who Registered in the Last 180 Days:**
```sql
select * from members
where reg_date = date_sub(curdate(),interval 180 day);
 ```

**Task 10:List Employees with Their Branch Manager's Name and their branch details:**
```sql
select e.emp_id as employee_id,
e.emp_name as employee_name,
b.branch_id as branch_id,
m.emp_name as manager
from employees  e
join branch  b
on e.branch_id = b.branch_id
join employees  m
on m.emp_id = b.manager_id;
```

**Task 11. Create a Table of Books with Rental Price Above a Certain Threshold:**
```sql
create table expense_books as
select * from books
where rental_price > 7.00;

select * from expense_books;
```

**Task 12: Retrieve the List of Books Not Yet Returned**
```sql
 select *from issued_status as  ist
left join return_status as rs
on ist.issued_id = rs.issued_id
where rs.return_id is null;
```
## Advanced SQL Operations

**Task 13: Identify Members with Overdue Books**
Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.
```sql
select ist.issued_member_id,m.member_name,b.book_title,ist.issued_date,
date_sub(current_date(),interval 30 day) as over_due_days
from issued_status as ist
join members as m on m.member_id = ist.issued_member_id
join books as  b on b.isbn = ist.issued_book_isbn
left join return_status as rs on rs.issued_id = ist.issued_id
where rs.return_date is null
and date_sub(current_date(),interval 30 day)  > 30
order by ist.issued_member_id,m.member_name,b.book_title,ist.issued_date;
```

**Task 14: Branch Performance Report**
Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.
```sql
create table branch_reports as
select b.branch_id,b.manager_id,
count(ist.issued_id) as number_book_issued,
count(rs.return_id) as number_of_book_return,
sum(bk.rental_price) as total_revenue
from issued_status as ist
join employees as e on e.emp_id = ist.issued_emp_id
join branch as b on e.branch_id = b.branch_id
left join return_status as rs on rs.issued_id = ist.issued_id
join books as bk on ist.issued_book_isbn = bk.isbn
group by b.branch_id,b.manager_id;

select * from branch_reports;
```
**Task 15: CTAS: Create a Table of Active Members**
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 2 months.
```sql
create table active_members as
select distinct m.member_id,m.member_name,m.member_address,m.reg_date 
from members as m
join issued_status as ist on m.member_id = ist.issued_member_id
where ist.issued_date >= date_sub(current_date,interval 2 month);

select * from active_members;
```

**Task 16: Find Employees with the Most Book Issues Processed**
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.
```sql
select e.emp_name,b.branch_address as branch,
count(ist.issued_id) as no_book_issued
from issued_status as ist
join employees as e on e.emp_id = ist.issued_emp_id
join branch as b on e.branch_id = b.branch_id
group by e.emp_name,branch
order by count(ist.issued_id) desc limit 3;
```

**Task 17:Identify Members Issuing High-Risk Books** Write a query to identify members who have issued books more than twice with the status "damaged" in the books table. Display the member name, book title, and the number of times they've issued damaged books.
```sql
select m.member_name,b.book_title,
count(*) as damaged_issue_count
from issued_status as ist
join members as m on ist.issued_member_id = m.member_id
join books as b on ist.issued_book_isbn = b.isbn
where b.status = 'damaged'
group by m.member_name,b.book_title
having count(*) > 2;
```

**Description**: Write a CTAS query to create a new table that lists each member and the books they have issued but not returned within 30 days. The table should include: The number of overdue books.The number of books issued by each member. The resulting table should show: Member ID Number of overdue books Total fines

**Reports**
Database Schema: Detailed table structures and relationships.
Data Analysis: Insights into book categories, employee salaries, member registration trends, and issued books.
Summary Reports: Aggregated data on high-demand books and employee performance.

**Conclusion**
This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.


