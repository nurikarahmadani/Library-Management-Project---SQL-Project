# Library-Management-Project---SQL-Project

Langkah penyelesaian projek
1. Membangun database.
2. Melakukan operasi CRUD.
3. CTAS (Create Table As Select): Memanfaatkan CTAS untuk membuat tabel baru berdasarkan hasil kueri.
4. Kueri SQL Tingkat Lanjut: Kembangkan kueri kompleks untuk menganalisis dan mengambil data tertentu.

## Membangun Database
### 1. Membuat database library_management
   ```SQL
   CREATE DATABASE library management
   ```
### 2. Menyiapkan tabel berdasarkan data csv
   ```sql

      DROP TABLE IF EXISTS branch;
      CREATE TABLE branch
      (
                  branch_id VARCHAR(10) PRIMARY KEY,
                  manager_id VARCHAR(10),
                  branch_address VARCHAR(30),
                  contact_no VARCHAR(15)
      );
      
      
      -- Create table "Employee"
      DROP TABLE IF EXISTS employees;
      CREATE TABLE employees
      (
                  emp_id VARCHAR(10) PRIMARY KEY,
                  emp_name VARCHAR(30),
                  position VARCHAR(30),
                  salary DECIMAL(10,2),
                  branch_id VARCHAR(10),
                  FOREIGN KEY (branch_id) REFERENCES  branch(branch_id)
      );
      
      
      -- Create table "Members"
      DROP TABLE IF EXISTS members;
      CREATE TABLE members
      (
                  member_id VARCHAR(10) PRIMARY KEY,
                  member_name VARCHAR(30),
                  member_address VARCHAR(30),
                  reg_date DATE
      );
      
      
      
      -- Create table "Books"
      DROP TABLE IF EXISTS books;
      CREATE TABLE books
      (
                  isbn VARCHAR(50) PRIMARY KEY,
                  book_title VARCHAR(80),
                  category VARCHAR(30),
                  rental_price DECIMAL(10,2),
                  status VARCHAR(10),
                  author VARCHAR(30),
                  publisher VARCHAR(30)
      );
      
      
      
      -- Create table "IssueStatus"
      DROP TABLE IF EXISTS issued_status;
      CREATE TABLE issued_status
      (
                  issued_id VARCHAR(10) PRIMARY KEY,
                  issued_member_id VARCHAR(30),
                  issued_book_name VARCHAR(80),
                  issued_date DATE,
                  issued_book_isbn VARCHAR(50),
                  issued_emp_id VARCHAR(10),
                  FOREIGN KEY (issued_member_id) REFERENCES members(member_id),
                  FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id),
                  FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn) 
      );
      
      
      
      -- Create table "ReturnStatus"
      DROP TABLE IF EXISTS return_status;
      CREATE TABLE return_status
      (
                  return_id VARCHAR(10) PRIMARY KEY,
                  issued_id VARCHAR(30),
                  return_book_name VARCHAR(80),
                  return_date DATE,
                  return_book_isbn VARCHAR(50),
                  FOREIGN KEY (return_book_isbn) REFERENCES books(isbn)
      );

   ```
### 3. Import data csv dengan BULK INSERT
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
## EKPLORASI DAN ANALISIS
### 1. Operasi CRUD (Create, Read, Update, Delete)
   
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
### 2. Data Analysis
**1. menampilkan data buku dengan kategori tertentu**
```sql
SELECT * FROM books
WHERE category = 'Classic'
```
Result : Ditampilkan tabel data buku dengan kategori 'Classic'.

![image](https://github.com/user-attachments/assets/f04d0fcb-cd7f-49a4-8e1d-42885e5f6894)

**2. Find Total Rental Income by Category**
```sql
   --Menggunakan CTE
   WITH rental_tb AS(
   
   	SELECT issued_status.issued_book_isbn, books.book_title, books.category, books.rental_price
   	FROM issued_status
   	INNER JOIN books ON books.isbn = issued_status.issued_book_isbn
   )
   
   SELECT category, SUM(rental_price) AS rental_income 
   FROM rental_tb
   GROUP BY category
   ```
Result : Ditampilkan data jumlah income perkategori buku

![data analysis-2](https://github.com/user-attachments/assets/59f78752-4671-4f2d-88cb-bf4a87f2a15b)


