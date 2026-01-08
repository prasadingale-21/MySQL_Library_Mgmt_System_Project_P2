# MySQL_Library_Mgmt_System_Project_P2

**Project Overview**  
**Project Title:** Library Management System   
**Database:** 'project_002'  

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries.  

<img width="611" height="408" alt="Library pic" src="https://github.com/user-attachments/assets/b1860dc9-241e-4057-8713-e55a52b74848" />


**Objectives**:

1. Set up the Library Management System Database: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.  
2. CRUD Operations: Perform Create, Read, Update, and Delete operations on the data.  
3. CTAS (Create Table As Select): Utilize CTAS to create new tables based on query results.  
4. Advanced SQL Queries: Develop complex queries to analyze and retrieve specific data.  

##  **Project Structure**  

1. **Database Setup**

2. <img width="1376" height="851" alt="ERD_Library_Mgmt" src="https://github.com/user-attachments/assets/7d38a0dd-b1cc-45ce-9cea-5aaa6afcdd8a" />

  **Database Creation:** Created a database schema named 'project_002'
  **Table Creation:** uploaded CSV files of tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

        use project_002;
      
      select * from branch;
      
      Alter table branch
      modify branch_id VARCHAR(10) PRIMARY KEY,
      modify manager_id VARCHAR(10),
      modify branch_address VARCHAR(55),
      modify contact_no VARCHAR(15);
      
      desc branch;
      
      -- Import table_2 'employees' to 'project_002' database
      
      select * from employees;
      
      Alter table employees
      modify emp_id VARCHAR(10) PRIMARY KEY,
      modify emp_name VARCHAR(25),
      modify position VARCHAR(15),
      modify salary INT,
      modify branch_id VARCHAR(25);
      
      DESC employees;
      
      -- Import table_3 'books' to 'project_002' database
      SELECT * FROM books;
      
      Alter table books
      modify isbn VARCHAR(25) PRIMARY KEY,
      modify book_title VARCHAR(75),
      modify category VARCHAR(25),
      modify rental_price FLOAT,
      modify status VARCHAR(10),
      modify author VARCHAR(35),
      modify publisher VARCHAR(55);
      
      desc books;
      
      -- Import table_4 'members' to 'project_002' database
      
      SELECT * FROM members;
      
      Alter table members
      modify member_id VARCHAR(10) PRIMARY KEY,
      modify member_name VARCHAR(25),
      modify member_address VARCHAR(25),
      modify reg_date DATE;
      
      DESC members;
      
      -- Import table_5 'issued_status' to 'project_002' database
      
      SELECT * FROM issued_status;
      
      Alter table issued_status
      modify issued_id varchar(10) PRIMARY KEY,
      modify issued_member_id varchar(10),
      modify issued_book_name varchar(75),
      modify issued_date DATE,
      modify issued_book_isbn varchar(25),
      modify issued_emp_id varchar(10);
      
      DESC issued_status;
      
      -- Import table_6 'return_status' to 'project_002' database
      
      SELECT * FROM return_status;
      
      Alter table return_status
      modify return_id varchar(10) PRIMARY KEY,
      modify issued_id varchar(10),
      modify return_book_name varchar(75),
      modify return_date DATE,
      modify return_book_isbn varchar(25);
      
      DESC return_status;

  Adding constraint of Foreign key to manage the relationship in between the tables.

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
      
      DESC issued_status;
      
      ALTER TABLE employees
      ADD CONSTRAINT fk_branch
      FOREIGN KEY (branch_id)
      REFERENCES branch(branch_id);
      
      DESC employees;
      
      DESC return_status;
      
      SELECT * FROM return_status
      WHERE issued_id NOT IN (SELECT issued_id FROM issued_status);
      
      DELETE FROM return_status
      WHERE issued_id NOT IN (SELECT issued_id FROM issued_status);
      
      ALTER TABLE return_status
      ADD FOREIGN KEY (issued_id)
      REFERENCES issued_status(issued_id);
      
      DESC return_status;


2. **CRUD Operations**

    Create: Inserted sample records into the books table.  
    Read: Retrieved and displayed data from various tables.  
    Update: Updated records in the employees table.  
    Delete: Removed records from the members table as needed.


TASK 1:  **Create a New Book Record** -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"  

      INSERT INTO books (isbn, book_title, category, rental_price, status, author, publisher)
      VALUES ('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
      
      Select * from books
      WHERE isbn = '978-1-60129-456-2';
      
      Select * from books;

Task 2: Update an Existing Member's ie C101 Address FROM '123 Main St' TO '125 Main St'  

      UPDATE members
      SET member_address = '125 Main St'
      where member_id = 'C101';
      
      Select * from members;

Task 3: Delete a Record from the Issued Status Table   
   Objective: Delete the record with issued_id = 'IS103' from the issued_status table.  

      Select * from issued_status
      WHERE issued_id = IS103;
      
      DELETE FROM issued_status
      WHERE issued_id = IS103;

Task 4: Retrieve All Books Issued by a Specific Employee   
   Objective: Select all books issued by the employee with emp_id = 'E101'.  

      Select * from issued_status;
      
      SELECT * from issued_status
      WHERE issued_emp_id = 'E101';

Task 5: List Members Who Have Issued More Than One Book     
  Objective: Use GROUP BY to find members who have issued more than one book.  

      Select * from members;
      Select * from issued_status;
      
      SELECT issued_emp_id, COUNT(issued_emp_id) AS total_books_issued
      FROM issued_status 
      GROUP BY issued_emp_id
      HAVING COUNT(issued_id) > 1;

Task 6: Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt      

      CREATE TABLE book_issued_cnt AS
      SELECT b.isbn, b.book_title, count(ist.issued_id) AS issued_count
      FROM issued_status ist
      JOIN books b on ist.issued_book_isbn = b.isbn
      GROUP BY b.isbn, b.book_title;
      
      SELECT * FROM book_issued_cnt;

Task 7. Retrieve All Books in a Specific Category:  

      SELECT * from books;
      
      SELECT * FROM books 
      WHERE category = 'Classic';

Task 8: Find Total Rental Income by Category:  

      select b.category, SUM(b.rental_price) AS rental_income_categorywise
      FROM issued_status ist
      JOIN books b on ist.issued_book_isbn = b.isbn
      GROUP BY b.category;

TASK 9 - List Members Who Registered in the Last 180 Days:  

      INSERT INTO members 
      values('C117', 'SAM', '145 Main St', '2025-12-06');
      
      SELECT * FROM members
      WHERE  reg_date > DATE_SUB(current_date(), INTERVAL 180 DAY);

TASK 10 - List Employees with Their Branch Manager's Name and their branch details:  

    SELECT * from employees;
    SELECT * from branch;
    SELECT e1.emp_id, e1.emp_name, e1.position, e1.salary,
        b.*, e2.emp_name as manager
    FROM employees e1
    JOIN branch b
    ON e1.branch_id = b.branch_id    
    JOIN employees e2 ON e2.emp_id = b.manager_id;

TASK 11 - Create a Table of Books with Rental Price Above a Certain Threshold:  
    CREATE TABLE expensive_books AS
    SELECT * FROM books
    WHERE rental_price > 7.00;
    SELECT * from expensive_books;

Task 12: Retrieve the List of Books Not Yet Returned  

      SELECT DISTINCT ist.issued_book_name 
      FROM issued_status as ist
      LEFT JOIN
      return_status as rs
      ON rs.issued_id = ist.issued_id
      WHERE rs.return_id IS NULL;

Task 13: Identify Members with Overdue Books  
   Write a query to identify members who have overdue books (assume a 30-day return period).Display the member's_id, member's name, book title, issue date, and days overdue.

      -- issued_status == members == books == return_status
      -- filter books which is return
      -- overdue > 30 
      -- consider current date = 2024-08-24
      
      SELECT ist.issued_member_id, m.member_name, bk.book_title, ist.issued_date, datediff('2024-08-24', ist.issued_date) as over_dues_days
      from issued_status ist
      JOIN members m
      ON m.member_id = ist.issued_member_id
      JOIN books bk ON bk.isbn = ist.issued_book_isbn
      LEFT JOIN return_status rs ON rs.issued_id = ist.issued_id
      WHERE rs.return_date IS NULL AND datediff('2024-08-24', ist.issued_date) > 30
      ORDER BY ist.issued_member_id;


Task 14: Update Book Status on Return  
  Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table).  

      DELIMITER &&
      CREATE PROCEDURE add_return_records(
      	IN p_return_id VARCHAR(10),
      	IN p_issued_id VARCHAR(10)
      )
      
      BEGIN
      
      DECLARE v_isbn VARCHAR(50);
      DECLARE v_book_name VARCHAR(80);
      
       -- Insert into return_status table
      
      INSERT INTO return_status(return_id, issued_id, return_date)
      VALUES (p_return_id, p_issued_id, CURRENT_DATE());
      
       -- Fetch ISBN and Book Name
      
       SELECT 
              issued_book_isbn,
              issued_book_name
          INTO 
              v_isbn,
              v_book_name
          FROM issued_status
          WHERE issued_id = p_issued_id;
         
      -- Update book status
         
          UPDATE books
          SET status = 'yes'
          WHERE isbn = v_isbn;
      
      -- Display message (replacement for RAISE NOTICE)
      
          SELECT CONCAT('Thank you for returning the book: ', v_book_name) AS message;
      END &&
      DELIMITER ;
      
      -- Example
      CALL add_return_records('R101', 'IS135');
      
      SELECT * 
      from books bk
      JOIN issued_status ist ON ist.issued_book_isbn = bk.isbn
      where ist.issued_id = 'IS140';
      
      UPDATE books
      SET status = 'No'
      WHERE isbn = '978-0-330-25864-8';
      
      CALL add_return_records('RS148', 'IS140');
      
      SELECT * FROM return_status
      WHERE return_id = 'RS148';


Task 15: Branch Performance Report  
   Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

      -- COUNT(issued_id), COUNT(return_id), SUM(rental)
      -- branch (branch_id) == issued_status(issued_id) == books(

      SELECT * FROM branch;
      SELECT * FROM books;
      SELECT * FROM issued_status;
      SELECT * FROM employees;
      
      CREATE TABLE branch_reports AS
      SELECT 
      	b.branch_id, b.manager_id, 
      	COUNT(ist.issued_id) AS no_of_books_issued, 
      	COUNT(rs.return_id) AS no_of_books_returned, 
      	SUM(bk.rental_price) as total_revenue
      FROM issued_status ist
      JOIN employees e ON e.emp_id = ist.issued_emp_id
      JOIN branch b ON b.branch_id = e.branch_id
      JOIN books bk ON bk.isbn = ist.issued_book_isbn
      LEFT JOIN return_status rs ON rs.issued_id = ist.issued_id
      GROUP BY b.branch_id, b.manager_id;
      
      SELECT * FROM branch_reports
      ORDER BY branch_id;

Task 16: Create a Table of Active Members  
  Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 5 months. Consider current date 2024-08-24 .

      CREATE TABLE active_members AS
      SELECT * FROM members
      WHERE member_id IN 
      	(SELECT DISTINCT issued_member_id FROM issued_status
            WHERE issued_date > DATE_SUB( '2024-08-24', INTERVAL 5 MONTH));

      SELECT * FROM active_members;

Task 17: Find Employees with the Most Book Issues Processed   
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.  

      SELECT 
      	e.emp_name, 
      	b.branch_id,
      	COUNT(ist.issued_id) AS no_of_books_issued
      FROM issued_status ist 
      JOIN employees e ON e.emp_id = ist.issued_emp_id
      JOIN branch b ON b.branch_id = e.branch_id
      GROUP BY e.emp_name, b.branch_id
      ORDER BY no_of_books_issued desc
      LIMIT 3;

TASK 18 - Stored Procedure Objective: Create a stored procedure to manage the status of books in a library system.  
    Description: Write a stored procedure that updates the status of a book in the library based on its issuance. 
    The procedure should function as follows: The stored procedure should take the book_id as an input parameter. 
    The procedure should first check if the book is available (status = 'yes'). 
    If the book is available, it should be issued, and the status in the books table should be updated to 'no'. 
    If the book is not available (status = 'no'), the procedure should return an error message indicating 
    that the book is currently not available.

      SELECT * FROM branch;
      SELECT * FROM books;
      SELECT * FROM issued_status;
      SELECT * FROM employees;
      SELECT * FROM members;
      
      DELIMITER &&
      
      CREATE PROCEDURE issue_book(
          IN p_issued_id VARCHAR(10),
          IN p_issued_member_id VARCHAR(30),
          IN p_issued_book_isbn VARCHAR(30),
          IN p_issued_emp_id VARCHAR(10)
      )
      BEGIN
          -- variable declaration
          DECLARE v_status VARCHAR(10);
      
          -- get book availability status
          SELECT status
          INTO v_status
          FROM books
          WHERE isbn = p_issued_book_isbn;
      
          IF v_status = 'yes' THEN
      
              INSERT INTO issued_status (
                  issued_id,
                  issued_member_id,
                  issued_date,
                  issued_book_isbn,
                  issued_emp_id
              )
              VALUES (
                  p_issued_id,
                  p_issued_member_id,
                  CURRENT_DATE(),
                  p_issued_book_isbn,
                  p_issued_emp_id
              );
      
              UPDATE books
              SET status = 'no'
              WHERE isbn = p_issued_book_isbn;
      
              -- success message
              SELECT CONCAT(
                  'Book records added successfully for book isbn : ',
                  p_issued_book_isbn
              ) AS message;
      
          ELSE
              -- unavailable message
              SELECT CONCAT(
                  'Sorry to inform you the book you have requested is unavailable. book_isbn: ', p_issued_book_isbn) AS message;
          END IF;
      
      END && 
      DELIMITER ;
      
      -- Test
      
      SELECT * FROM books;
      
      CALL issue_book('IS155', 'C108', '978-0-553-29698-2', 'E104');
      CALL issue_book('IS156', 'C108', '978-0-375-41398-8', 'E104');
      
      SELECT * FROM issued_status;
      
      SELECT * FROM books
      WHERE isbn = '978-0-375-41398-8';
