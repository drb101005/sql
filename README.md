# sql

-- ===============================================
-- 1. ER DIAGRAM FOR HOTEL MANAGEMENT SYSTEM
-- ===============================================
-- Draw ER Diagram with:
-- Entities: Guest, Room, Booking, Employee, Payment
-- Relationships: Makes, Assigned_to, Processes
-- Attributes as explained above


-- ===============================================
-- 2. STUDENTS TABLE
-- ===============================================
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    Name VARCHAR(50),
    Age INT,
    Email VARCHAR(100)
);

-- Add Gender column
ALTER TABLE Students ADD Gender VARCHAR(10);

-- Modify Age to SMALLINT
ALTER TABLE Students MODIFY Age SMALLINT;

-- Drop Email column
ALTER TABLE Students DROP COLUMN Email;

-- Rename table
RENAME TABLE Students TO StudentRecords;


-- ===============================================
-- 3. PRODUCTS TABLE
-- ===============================================
CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    ProductName VARCHAR(50),
    Category VARCHAR(50),
    Quantity INT,
    Price DECIMAL(10,2)
);

-- Add SupplierName
ALTER TABLE Products ADD SupplierName VARCHAR(50);

-- Change Quantity to SMALLINT
ALTER TABLE Products MODIFY Quantity SMALLINT;

-- Drop Category
ALTER TABLE Products DROP COLUMN Category;

-- Rename table
RENAME TABLE Products TO Inventory;


-- ===============================================
-- 4. COURSES TABLE
-- ===============================================
CREATE TABLE Courses (
    CourseID INT PRIMARY KEY,
    CourseName VARCHAR(50),
    Credits INT,
    Instructor VARCHAR(50)
);

-- Add Semester
ALTER TABLE Courses ADD Semester VARCHAR(20);

-- Change Credits to FLOAT
ALTER TABLE Courses MODIFY Credits FLOAT;

-- Remove Instructor
ALTER TABLE Courses DROP COLUMN Instructor;

-- Rename table
RENAME TABLE Courses TO AvailableCourses;


-- ===============================================
-- 5. COLLEGEDB AND EMP TABLE
-- ===============================================
CREATE DATABASE CollegeDB;
USE CollegeDB;

CREATE TABLE emp (
    EmpID INT PRIMARY KEY,
    EmpName VARCHAR(50),
    Position VARCHAR(50),
    Salary DECIMAL(10,2)
);

-- Insert multiple records in single command
INSERT INTO emp (EmpID, EmpName, Position, Salary)
VALUES 
    (1, 'John', 'Assistant Professor', 12000),
    (2, 'Mary', 'Lecturer', 10000),
    (3, 'David', 'Professor', 20000),
    (4, 'Sarah', 'Assistant Professor', 11000),
    (5, 'Tom', 'Lecturer', 9000);

-- Update salary for Assistant Professor
UPDATE emp 
SET Salary = 15000 
WHERE Position = 'Assistant Professor';

-- Delete Lecturer records
DELETE FROM emp 
WHERE Position = 'Lecturer';


-- ===============================================
-- 6. EMPLOYEES AND DEPARTMENT TABLES
-- ===============================================
CREATE TABLE Employees (
    EmpID INT PRIMARY KEY,
    EmpName VARCHAR(50),
    DeptID INT,
    Salary DECIMAL(10,2)
);

CREATE TABLE Department (
    DeptID INT PRIMARY KEY,
    DeptName VARCHAR(50)
);

-- Insert data
INSERT INTO Employees VALUES 
    (1, 'Alice', 101, 50000.00),
    (2, 'Bob', 102, 60000.00),
    (3, 'Charlie', 101, 55000.00),
    (4, 'David', 103, 45000.00),
    (5, 'Eva', 102, 65000.00);

INSERT INTO Department VALUES 
    (101, 'HR'),
    (102, 'IT'),
    (103, 'Finance');

-- Count total employees
SELECT COUNT(*) AS TotalEmployees FROM Employees;

-- Find maximum and minimum salary
SELECT MAX(Salary) AS MaxSalary, MIN(Salary) AS MinSalary FROM Employees;

-- Average salary per department
SELECT DeptID, AVG(Salary) AS AvgSalary 
FROM Employees 
GROUP BY DeptID;

-- UNION ALL
SELECT EmpName FROM Employees WHERE DeptID = 101
UNION ALL
SELECT EmpName FROM Employees WHERE DeptID = 102;

-- INNER JOIN
SELECT e.EmpName, e.Salary, d.DeptName
FROM Employees e
INNER JOIN Department d ON e.DeptID = d.DeptID;


-- ===============================================
-- 7. VIEWS
-- ===============================================
CREATE TABLE employee (
    EmpID INT PRIMARY KEY,
    EmpName VARCHAR(50),
    Salary DECIMAL(10,2)
);

INSERT INTO employee VALUES (1, 'John', 50000), (2, 'Mary', 60000);

-- Create view
CREATE VIEW EmployeeView AS
SELECT EmpID, EmpName, Salary
FROM employee
WHERE Salary > 50000;

-- Update view
UPDATE EmployeeView
SET Salary = 55000
WHERE EmpID = 1;

-- Delete from view
DELETE FROM EmployeeView
WHERE EmpID = 2;

-- Drop view
DROP VIEW EmployeeView;


-- ===============================================
-- 8. TCL - CUSTOMER TABLE
-- ===============================================
CREATE TABLE Customer (
    CustID INT PRIMARY KEY,
    Name VARCHAR(50),
    City VARCHAR(50),
    Balance DECIMAL(10,2)
);

-- Insert and commit
INSERT INTO Customer VALUES (1, 'Alice', 'Mumbai', 5000.00);
INSERT INTO Customer VALUES (2, 'Bob', 'Delhi', 7000.00);
INSERT INTO Customer VALUES (3, 'Charlie', 'Pune', 6000.00);
COMMIT;

-- ROLLBACK example
DELETE FROM Customer WHERE CustID = 1;
ROLLBACK;

-- SAVEPOINT example
INSERT INTO Customer VALUES (4, 'David', 'Bangalore', 8000.00);
SAVEPOINT SP1;

INSERT INTO Customer VALUES (5, 'Eva', 'Chennai', 9000.00);
SAVEPOINT SP2;

INSERT INTO Customer VALUES (6, 'Frank', 'Kolkata', 7500.00);

-- Rollback to second savepoint
ROLLBACK TO SP2;

-- Commit
COMMIT;


-- ===============================================
-- 9. TCL - ORDERS TABLE
-- ===============================================
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerName VARCHAR(50),
    ProductName VARCHAR(50),
    Amount DECIMAL(10,2)
);

-- Insert 3 orders and commit
INSERT INTO Orders VALUES (1, 'John', 'Laptop', 50000);
INSERT INTO Orders VALUES (2, 'Mary', 'Phone', 30000);
INSERT INTO Orders VALUES (3, 'Tom', 'Tablet', 20000);
COMMIT;

-- Delete and rollback
DELETE FROM Orders WHERE OrderID = 2;
ROLLBACK;

-- Insert with savepoints
INSERT INTO Orders VALUES (4, 'Alice', 'Monitor', 15000);
SAVEPOINT SP1;

INSERT INTO Orders VALUES (5, 'Bob', 'Keyboard', 2000);
SAVEPOINT SP2;

INSERT INTO Orders VALUES (6, 'Charlie', 'Mouse', 1000);

-- Rollback to first savepoint
ROLLBACK TO SP1;

-- Commit and show final
COMMIT;
SELECT * FROM Orders;


-- ===============================================
-- 10. STORED PROCEDURE
-- ===============================================
DELIMITER //
CREATE PROCEDURE GetCustomers()
BEGIN
    SELECT * FROM Customer;
END //
DELIMITER ;

-- Call procedure
CALL GetCustomers();


-- ===============================================
-- 11. FUNCTION
-- ===============================================
DELIMITER //
CREATE FUNCTION CheckSalary(sal DECIMAL(10,2))
RETURNS VARCHAR(20)
DETERMINISTIC
BEGIN
    IF sal > 10000 THEN
        RETURN 'Above 10000';
    ELSE
        RETURN 'Below 10000';
    END IF;
END //
DELIMITER ;

-- Use function
SELECT Name, Balance, CheckSalary(Balance) AS SalaryStatus
FROM Customer;


-- ===============================================
-- 12. CURSOR
-- ===============================================
DELIMITER //
CREATE PROCEDURE DisplayHighEarners()
BEGIN
    DECLARE done INT DEFAULT FALSE;
    DECLARE emp_name VARCHAR(50);
    DECLARE emp_salary DECIMAL(10,2);
    
    DECLARE emp_cursor CURSOR FOR 
        SELECT EmpName, Salary FROM Employees WHERE Salary > 50000;
    
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
    
    OPEN emp_cursor;
    
    read_loop: LOOP
        FETCH emp_cursor INTO emp_name, emp_salary;
        IF done THEN
            LEAVE read_loop;
        END IF;
        SELECT emp_name, emp_salary;
    END LOOP;
    
    CLOSE emp_cursor;
END //
DELIMITER ;

-- Call procedure
CALL DisplayHighEarners();


START TRANSACTION;
DELETE FROM Customer WHERE CustID = 1;
ROLLBACK;

-- Example 2: With SAVEPOINT
START TRANSACTION;
INSERT INTO Customer VALUES (4, 'David', 'Bangalore', 8000.00);
SAVEPOINT SP1;
INSERT INTO Customer VALUES (5, 'Eva', 'Chennai', 9000.00);
SAVEPOINT SP2;
INSERT INTO Customer VALUES (6, 'Frank', 'Kolkata', 7500.00);
ROLLBACK TO SP2;  -- Undo only last insert
COMMIT;