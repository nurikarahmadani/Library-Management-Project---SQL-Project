# Library-Management-Project---SQL-Project

Source : https://github.com/najirh/Library-System-Management---P2

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
**Result :** Ditampilkan tabel data buku dengan kategori 'Classic'.

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
**Result :** Ditampilkan data jumlah income perkategori buku

![data analysis-2](https://github.com/user-attachments/assets/59f78752-4671-4f2d-88cb-bf4a87f2a15b)

**2. List Members Who Registered in The Last 1000 Days**
```sql
SELECT * 
FROM members
WHERE reg_date >= DATEADD(DAY, -1000, CAST(GETDATE() AS DATE));
``` 
Digunakan DATEADD untuk melakukan operasi terhadap DATE. Disini dilakukan operasi pengurangan DATE hari ini (diperoleh dengan GETDATE) dengan DAY sejumlah 1000 hari.
**Result:** Ditampilkan data members yang regis di 1000 hari terakhir.

![image](https://github.com/user-attachments/assets/04e81062-33da-4b8a-93ad-2d5de5abad9d)

**3. List Employees with Their Branch Manager's Name and Their Branch Details**
```sql
SELECT e1.emp_name, e1.position, e1.salary, branch.branch_id, branch.branch_address, branch.manager_id, e2.emp_name as manager
FROM employees as e1
INNER JOIN branch ON branch.branch_id = e1.branch_id
INNER JOIN employees as e2 ON branch.manager_id = e2.emp_id
```
**Result :** Ditampilkan data karyawan dengan detail branch tempat bekerja tiap karyawan.

![image](https://github.com/user-attachments/assets/96e36d7f-f333-4b36-9255-3e4b75f57425)

**4. Retrieve the List of Books Not Yet Returned**
```SQL
SELECT * 
FROM issued_status as ist
LEFT JOIN return_status rt ON ist.issued_id = rt.issued_id
WHERE rt.issued_id IS NULL;
```
**Result :** ditampilkan data buku yang belum dikembalikan

![image](https://github.com/user-attachments/assets/3f2fdf13-eabd-4db0-95ef-6dd22d1f87dd)

**5. Menampilkan Data Members dan Data Peminjaman yang Terlambat Mengembalikan Buku**
Catatan : buku wajib dikembalikan dalam 30 hari setelah dipinjam
```sql
SELECT 
	m.member_id, 
	m.member_name, 
	ist.issued_id, 
	ist.issued_book_name, 
	ist.issued_date,
	DATEDIFF(DAY, DATEADD(DAY, 30, ist.issued_date), GETDATE()) AS late_days
FROM issued_status AS ist
INNER JOIN members AS m ON m.member_id = ist.issued_member_id
WHERE DATEADD(DAY, 30, ist.issued_date) < GETDATE()
```
**Result :** ditampilkan data members dan issued_status (status pengembalian)

![image](https://github.com/user-attachments/assets/c5971e2a-4a18-4179-8d2c-ebe29470ba75)

**6. Membuat Branch Performance Report**
Create a query that generates a performance report for each branch, showing the number of books issued, 
the number of books returned, and the total revenue generated from book rentals.
```sql
WITH report AS(
	SELECT 
		br.branch_id,
		emp.emp_name,
		ist.issued_id,
		rtn.return_id,
		b.rental_price
	FROM branch AS br
	INNER JOIN employees AS emp ON br.branch_id = emp.branch_id
	INNER JOIN issued_status AS ist ON ist.issued_emp_id = emp.emp_id
	LEFT JOIN return_status AS rtn ON rtn.issued_id = ist.issued_id
	INNER JOIN books AS b ON b.isbn = ist.issued_book_isbn

)
SELECT 
	branch_id,
	COUNT(issued_id) AS issued_total,
	COUNT(return_id) AS return_total,
	SUM(rental_price) AS revenue_total
FROM report
GROUP BY branch_id
```

**Result :** ditampilkan data report total peminjaman, total pengembalian, dan total pendapatan untuk tiap branch

![image](https://github.com/user-attachments/assets/0ee0fc80-6949-40af-ba62-0134cff4803d)










