# Library-Management-Project---SQL-Project

Langkah penyelesaian projek
1. Membangun database.
2. Melakukan operasi CRUD.
3. CTAS (Create Table As Select): Memanfaatkan CTAS untuk membuat tabel baru berdasarkan hasil kueri.
4. Kueri SQL Tingkat Lanjut: Kembangkan kueri kompleks untuk menganalisis dan mengambil data tertentu.

## Membangun Database
1. Membuat database library_management
   ```SQL
   CREATE DATABASE library management
   ```
2. Menyiapkan tabel berdasarkan data csv
   ```sql
   CREATE TABLE books(
      isbn VARCHAR(100) PRIMARY KEY,
      book_title VARCHAR(100),
      category VARCHAR(100),
      rental_price DECIMAL(10,2), 
      status VARCHAR(100),
      author VARCHAR(100),
      publisher VARCHAR(100)
    )

   CREATE TABLE members(
    	member_id VARCHAR(100) PRIMARY KEY,
    	member_name VARCHAR(100),
    	member_address VARCHAR(100),
    	reg_date DATE 
    )

   CREATE TABLE branch(
    	branch_id VARCHAR(20) PRIMARY KEY,
    	manager_id VARCHAR(100),
    	branch_address VARCHAR(20),
    	contact_no VARCHAR(20) 
    )
   
   CREATE TABLE employees(
    	emp_id VARCHAR(20) PRIMARY KEY,
    	emp_name VARCHAR(50),
    	position VARCHAR(15),
    	salary DECIMAL(10,2),
    	branch_id VARCHAR(20)
    )
   
   CREATE TABLE issued_status(
    	issued_id VARCHAR(20) PRIMARY KEY,
    	issued_member_id VARCHAR(100),
    	issued_book_name VARCHAR(100),
    	issued_date DATE,
    	issued_book_isbn VARCHAR(100),
    	issued_emp_id VARCHAR(20)
    )  

   CREATE TABLE return_status(
    	return_id VARCHAR(20) PRIMARY KEY,
    	issued_id VARCHAR(20),
    	return_book_name VARCHAR(100),
    	return_date DATE,
    	return_book_isbn VARCHAR(20)
    )
   ```
3. Import data csv dengan BULK INSERT
   ```SQL
   BULK INSERT books
   FROM 'C:\Users\ASUS\OneDrive - Balikpapan Cerdas\Data Analyst\Project\Library System Management Project\Dataset\books.csv'
   WITH (
      FORMAT = 'CSV',
      FIRSTROW = 2,
      FIELDTERMINATOR = ',',
      ROWTERMINATOR = '\n',
      CODEPAGE = '1252'
    );
   
   BULK INSERT members
   FROM 'C:\Users\ASUS\OneDrive - Balikpapan Cerdas\Data Analyst\Project\Library System Management Project\Dataset\members.csv'
   WITH (
      FORMAT = 'CSV',
      FIRSTROW = 2,
      FIELDTERMINATOR = ',',
      ROWTERMINATOR = '\n',
      CODEPAGE = '1252'
    );
   
      BULK INSERT branch
   FROM 'C:\Users\ASUS\OneDrive - Balikpapan Cerdas\Data Analyst\Project\Library System Management Project\Dataset\branch.csv'
   WITH (
      FORMAT = 'CSV',
      FIRSTROW = 2,
      FIELDTERMINATOR = ',',
      ROWTERMINATOR = '\n',
      CODEPAGE = '1252'
    );
   
   BULK INSERT branch
   FROM 'C:\Users\ASUS\OneDrive - Balikpapan Cerdas\Data Analyst\Project\Library System Management Project\Dataset\branch.csv'
   WITH (
      FORMAT = 'CSV',
      FIRSTROW = 2,
      FIELDTERMINATOR = ',',
      ROWTERMINATOR = '\n',
      CODEPAGE = '1252'
    );
   
      BULK INSERT issued_status
   FROM 'C:\Users\ASUS\OneDrive - Balikpapan Cerdas\Data Analyst\Project\Library System Management Project\Dataset\issued_status.csv'
   WITH (
      FORMAT = 'CSV',
      FIRSTROW = 2,
      FIELDTERMINATOR = ',',
      ROWTERMINATOR = '\n',
      CODEPAGE = '1252'
    );
   
   BULK INSERT return_status
   FROM 'C:\Users\ASUS\OneDrive - Balikpapan Cerdas\Data Analyst\Project\Library System Management Project\Dataset\return_status.csv'
   WITH (
      FORMAT = 'CSV',
      FIRSTROW = 2,
      FIELDTERMINATOR = ',',
      ROWTERMINATOR = '\n',
      CODEPAGE = '1252'
    );
   ```
5. Menambahkan Foreign Key
   ```sql
    ALTER TABLE branch
    ADD CONSTRAINT manager_id
    FOREIGN KEY (manager_id) REFERENCES employees(emp_id);
    
    ALTER TABLE issued_status
    ADD CONSTRAINT issued_member_id
    FOREIGN KEY (issued_member_id) REFERENCES members(member_id);
    
    ALTER TABLE issued_status
    ADD CONSTRAINT issued_book_isbn
    FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn)
    
    ALTER TABLE issued_status
    ADD CONSTRAINT issued_emp_id
    FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id)
    
    ALTER TABLE return_status
    ADD CONSTRAINT issued_id
    FOREIGN KEY (issued_id) REFERENCES issued_status(issued_id)
    
    ALTER TABLE return_status
    ADD CONSTRAINT return_book_isbn
    FOREIGN KEY (return_book_isbn) REFERENCES books(isbn)
    
    --query pendukung
    SELECT * 
    FROM return_status
    WHERE return_book_isbn NOT IN (SELECT isbn FROM books);
    
    DELETE FROM return_status WHERE return_book_isbn NOT IN (SELECT isbn FROM books);
    
    ALTER TABLE return_status ALTER COLUMN return_book_isbn VARCHAR(100)

   ```
7. Operasi CRUD (Create, Read, Update, Delete)
   
   ```sql
   -- Task 1. Create a New Book Record 
   -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
   
   INSERT INTO books VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')
   
   --
   --Task 2: Update an Existing Member's Address
   
   UPDATE members
   SET member_address = '125 Oak St'
   WHERE member_id = 'C103';
   
   --
   --Task 3: Delete a Record from the Issued Status Table 
   -- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.
   
   DELETE FROM issued_status
   WHERE   issued_id =   'IS121';
   
   --
   --Task 4: Retrieve All Books Issued by a Specific Employee 
   -- Objective: Select all books issued by the employee with emp_id = 'E101'.
   
   SELECT * FROM issued_status
   WHERE issued_emp_id = 'E101'
   
   --
   --Task 5: List Members Who Have Issued More Than One Book 
   -- Objective: Use GROUP BY to find members who have issued more than one book.
   
   SELECT
       issued_emp_id,
       COUNT(*)
   FROM issued_status
   GROUP BY issued_emp_id
   HAVING COUNT(*) > 1;
   
   --
   --CTAS (Create Table As Select)
   
   CREATE TABLE book_issued_cnt AS
   SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS issue_count
   FROM issued_status as ist
   JOIN books as b
   ON ist.issued_book_isbn = b.isbn
   GROUP BY b.isbn, b.book_title;

   ```
9. SSJS
10. SHSGS
11. SGHSGS
12. 
