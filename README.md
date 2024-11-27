# PostgrSQL_Library_Management_System
![screenshot](5u92qg9i.png)  

## Project Overview

**Project Title**: Library Management System And Analysis  
**Database**: `Library_database`

Through this project I will demonstrate my SQL skills and techniques to create a database, create tables, populate them with data obtained from a small regional library.  I will then explore, clean, and analyze available data. I will be performing exploratory data analysis, and answering specific business questions through SQL queries.  The programming language I am using is PostgreSQL through PGAdmin4.

## Objectives

This project showcases the development of a Library Management System utilizing SQL.   
It involves designing and managing tables, carrying out CRUD operations, and executing complex SQL queries.   
The aim is to highlight my skills in in database structure, data handling, and advanced querying techniques.  

**Library Management System Implementation Steps:**  
1- **Database Setup:** Establish the Library Management System database by creating and populating tables for branches, employees, members, books, issued status, and return status.  
2- **CRUD Operations:** Execute Create, Read, Update, and Delete operations to manage and manipulate the stored data effectively.  
3- **CTAS (Create Table As Select):** Leverage the CTAS feature to generate new tables dynamically based on the results of existing SQL queries.  
4- **Advanced SQL Queries:** Construct sophisticated queries to extract and analyze targeted information, enabling detailed insights and reporting.  


## Project Structure

**PART 1: DATABASE AND TABLES CREATION**

I will now begin with the first part involving the creation of a database.  

```sql
CREATE DATABASE library_Database;
```
Then I will create the tables and specify the columns and their data type.  The primary keys of each table will be a column with non null and unique values.

```sql
-- Create table "branch"
DROP TABLE IF EXISTS branch;
CREATE TABLE branch
(
            branch_id VARCHAR(10) PRIMARY KEY,
            manager_id VARCHAR(10),
            branch_address VARCHAR(55),
            contact_no VARCHAR(15)
);


-- Create table "employee"
DROP TABLE IF EXISTS employees;
CREATE TABLE employees
(
            emp_id VARCHAR(10) PRIMARY KEY,
            emp_name VARCHAR(25),
            position VARCHAR(15),
            salary INT,
            branch_id VARCHAR(25)    -- This is a foreign key
);



-- Create table "books"
DROP TABLE IF EXISTS books;
CREATE TABLE books
(
            isbn VARCHAR(50) PRIMARY KEY,
            book_title VARCHAR(75),
            category VARCHAR(30),
            rental_price FLOAT,
            status VARCHAR(15),
            author VARCHAR(35),
            publisher VARCHAR(55)
);



-- Create table "members"
DROP TABLE IF EXISTS members;
CREATE TABLE members
(
            member_id VARCHAR(10) PRIMARY KEY,
            member_name VARCHAR(35),
            member_address VARCHAR(75),
            reg_date DATE
);



-- Create table "issue_status"
DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status
(
            issued_id VARCHAR(10) PRIMARY KEY,
            issued_member_id VARCHAR(10),  -- This is a foreign key
            issued_book_name VARCHAR(75),
            issued_date DATE,
            issued_book_isbn VARCHAR(50),  -- This is a foreign key
            issued_emp_id VARCHAR(10)      -- This is a foreign key
);


-- Create table "return_status"
DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status
(
            return_id VARCHAR(10) PRIMARY KEY,
            issued_id VARCHAR(10),
            return_book_name VARCHAR(75),
            return_date DATE,
            return_book_isbn VARCHAR(50)    -- This is a foreign key
            
);
```
So now that all the tables are created, I identified all the primary keys and foreigh keys, and I will start creating connections between the tables.

```sql
ALTER TABLE issued_status
ADD CONSTRAINT fk_members
FOREIGN KEY (issued_member_id)
REFERENCES members(member_id);


ALTER TABLE issued_status
ADD CONSTRAINT fk_books
FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn);


ALTER TABLE issued_status
ADD CONSTRAINT fk_employees
FOREIGN KEY (issued_emp_id)
REFERENCES employees(emp_id);


ALTER TABLE employees
ADD CONSTRAINT fk_branch
FOREIGN KEY (branch_id)
REFERENCES branch(branch_id);

ALTER TABLE return_status
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_status(issued_id);
```
Using PGAdmin4's EDR tool, I am generating an EDR containing all the table relationships I wish to have.  
The file called 'Lbrary Database EDR.pgerd' is attached in the list of files in the current repository.

I will move on to importing the tables data.
While importing the data into the "emplooyee" table, I get the following error: 'ERROR: invalid input syntax for type integer: "60000.00"'
I can work around this by changing the data type in the colun concerned ('Salary') into a Float

```sql
ALTER TABLE employees
ALTER COLUMN salary TYPE FLOAT;
```

Now that all the data is imported I will verify that it looks as expected. I will run the following commands one line at the time and look at the data. 

```sql
SELECT *  FROM books;
SELECT *  FROM branch;
SELECT *  FROM employees;
SELECT *  FROM members;
SELECT *  FROM issued_status;
SELECT *  FROM return_status;
```



**PART 2:  PROJECT TASKS**

**CRUD Operations (Create, Read, Update, Delete)**


**Task 1:  CREATE:** The library obtained a new book!  I need to create a record for it. I will insert the information into the books table.
 Here is the information provided: "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
Then I will verify that the table has been updated.

```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```

**Task 2: UPDATE:** A beloved library member has moved.  I need to update this existing member's address. This involves retrieving and displaying data from various tables.

```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```

**Task 3: DELETE:** I am asked to delete a record from the issued_status table. It is the record with issued_id = 'IS121' from the issued_status table.

```sql
DELETE FROM issued_status
 WHERE issued_id = 'IS121';
```

**Task 4: READ:** We are looking to retrieve All Books issued by a specific employee. I will select all books issued by the employee with emp_id = 'E101'.

```sql
SELECT * FROM issued_status
WHERE issued_emp_id = 'E101'
```

**Task 5: READ:**  I am asked for a list of all members who have issued more than one book. I can do this by using the GROUP BY clause.

```sql
SELECT
    issued_emp_id,
    COUNT(*)
FROM issued_status
GROUP BY 1
HAVING COUNT(*) > 1
```


**PART 2:  DATA ANALYSIS**

**In order to address specific questions from the stakeholders, I will now run a series of SQL queries.**

**Task 6:**  What are all the book categories we have the most? The least?

```sql
SELECT category, 
		COUNT(*) AS num_cat
FROM books
GROUP BY category
ORDER BY num_cat;
```

**Task 7:** What is the total rental income by category?

```sql
SELECT 
    b.author,
    SUM(b.rental_price) as author_income,
    COUNT(*)
FROM 
issued_status as c
JOIN
books as b
ON b.isbn = c.issued_book_isbn
GROUP BY 1
ORDER BY author_income DESC;
```

**Task 8:** Which members registered in the last 200 Days?

```sql
SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '200 days';
```

**Task 9:** What are the employees and their branch manager's names and branch details?
I will join 3 different tables to achieve this:

```sql
SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name as manager
FROM employees as e1
JOIN 
branch as b
ON e1.branch_id = b.branch_id    
JOIN
employees as e2
ON e2.emp_id = b.manager_id
```

**Task 10:**  What are the books with rental prices above $7? Create a table to store that information.

```sql
CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 7.00;

SELECT * FROM expensive_books
```

**Task 11:** Which books have not yet been returned?
I need to use a left join here because some books are out and some are returned.

```sql
SELECT * FROM issued_status as a
LEFT JOIN
return_status as b
ON b.issued_id = a.issued_id
WHERE b.return_id IS NULL;
```

**PART 4:  COMPLEX QUERIES**


**Task 12:** I get asked to run this query often, and it can get repetitive to run the same query.
So I am going to use CTAS (Create Table As Select) to generate a new table based on query results.
I am interested in each book and the total book_issued count for it.

```sql
CREATE TABLE book_issued_cnt AS
SELECT b.isbn, b.book_title, COUNT(c.issued_id) AS issue_count
FROM issued_status as c
JOIN books as b
ON c.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;

SELECT * FROM book_issued_cnt
```


**Task 13:** Which members have overdue books (more than 30-day out)? Which ones have had books out the longest?
I need to display the member's_id, member's name, book title, issue date, and days overdue.
I will need to create a variable called overdue_days, and I will need to join 3 tables to retrieve the information I need.

```sql
SELECT 
    ist.issued_member_id,
    m.member_name,
    bk.book_title,
    ist.issued_date,
    -- rs.return_date,
    CURRENT_DATE - ist.issued_date as overdue_days
FROM issued_status as ist
JOIN 
members as m
    ON m.member_id = ist.issued_member_id
JOIN 
books as bk
ON bk.isbn = ist.issued_book_isbn
LEFT JOIN 
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE 
    rs.return_date IS NULL
    AND
    (CURRENT_DATE - ist.issued_date) > 30
ORDER BY overdue_days DESC
```

**Task 14:** Write a query that generates a performance report for each branch. What are the top branches by revenue? We should be able to reuse it.
Include the number of books issued, the number of books returned, and the total revenue generated from book rentals.

```sql
CREATE TABLE branch_reports
AS
SELECT 
    b.branch_id,
    b.manager_id,
    COUNT(c.issued_id) as number_book_issued,
    COUNT(rs.return_id) as number_of_book_returned,
    SUM(bk.rental_price) as total_revenue
FROM issued_status as c
JOIN 
employees as e
ON e.emp_id = c.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
LEFT JOIN
return_status as rs
ON rs.issued_id = c.issued_id
JOIN 
books as bk
ON c.issued_book_isbn = bk.isbn
GROUP BY 1, 2;

SELECT * FROM branch_reports
ORDER BY total_revenue DESC;
```

**Task 15:** Create a Table of Active Members containing members who have issued at least one book in the last 2 months.

```sql
CREATE TABLE active_members
AS
SELECT * FROM members
WHERE member_id IN (SELECT 
                        DISTINCT issued_member_id   
                    FROM issued_status
                    WHERE 
                        issued_date >= CURRENT_DATE - INTERVAL '2 month'
                    );

SELECT * FROM active_members;
```

**Task 16:** What are the employees with the most book issues processed?
We are interested in the top 3 employees who have processed the most book issues. 
I will display the employee name, number of books processed, and their branch.

```sql
SELECT 
    e.emp_name,
    b.*,
    COUNT(c.issued_id) as books_issued
FROM issued_status as c
JOIN
employees as e
ON e.emp_id = c.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
GROUP BY 1, 2
ORDER BY books_issued DESC
LIMIT 3
```


**PART 5: CONCLUSIONS**

-- We only had 1 new member registration in the last 200 days.   
-- We have a lot more titles in the classics and history categories, but only 1 title in science fiction and no romance.  Science fiction and romance are very popular, and we should remedy to that.  
-- Our most lucrative authors are George Orwell, F. Scott Fitzgerald, and J.K. Rowling.  We should capitalize on that.  
-- Daniel Anderson's branches have more employees than Laura Martinez's branches.  Reassigning more employees to Laura's branch might prove helpful.  
-- The Great Gatsby, Harry Potter and the Sorcerers Stone, Animal Farm are some of the titles issued the most often.  
-- There are 20 members that have jkept books for over 200 days.  This is detrimental to the library.  
-- There are 5 branches.  Branch B001 is the most successful with a revenue of 111.5; B002 is the least successful with a revenue of 12.  Merging B002 with a neighboring branch might be helpful.  
-- Michelle Ramirez and Laura Martinez are the employess who have issued the most books.


  
## Author - Audrey Tam

This project is part of my portfolio, showcasing the SQL skills needed for data analyst roles. Please reach out with any questions or feedback.

### Reach me

Website: https://datawithaudrey.com  
Email: auddie7@gmail.com
LinkdIn: www.linkedin.com/in/audrey-tam-2771341a5


Thank you for stopping by, and I look forward to connecting with you.
