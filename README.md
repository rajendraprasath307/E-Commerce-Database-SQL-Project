# E-Commerce-Database-SQL-Project
understanding of the e-commerce system, facilitating better decision-making and operational management using SQL
## E-Commerce Database SQL Project

### Explanation

- **Creating the Database:** 
  - `CREATE DATABASE ECommerceDB;` creates a new database named `ECommerceDB`.

- **Selecting the Database:** 
  - `USE ECommerceDB;` sets `ECommerceDB` as the current database for subsequent operations.

### Table Creation

- **Categories Table:** 
  - Stores information about product categories, including an ID and name.

- **Users Table:** 
  - Stores user details like username, password, email, and contact information.

- **Products Table:** 
  - Contains details about products including ID, name, description, price, stock quantity, and a reference to the category.

- **Orders Table:** 
  - Stores order details including the user who placed the order, the date, status, and total amount.

- **OrderItems Table:** 
  - Stores the items in each order, including product ID, quantity, and price.

- **Payments Table:** 
  - Records payment information for orders, including amount and payment method.

- **Shipping Table:** 
  - Contains shipping details for orders, such as address, shipping date, and method.

### Data Insertion

- **Inserting Data:** 
  - Each `INSERT INTO` statement adds sample data into the respective tables. This includes users, categories, products, orders, order items, payments, and shipping information.

### Queries

- **Retrieve All Products with Category Names:** 
  - Shows details of products along with their category names.

- **Get Total Sales Amount for Each Product:** 
  - Calculates the total sales amount for each product.

- **Find the Top 5 Most Expensive Products:** 
  - Lists the top 5 products with the highest price.

- **Retrieve All Orders and Payment Status:** 
  - Shows details of orders, including payment status.

- **List All Users Who Have Placed an Order:** 
  - Lists users who have made at least one order.

- **Get the Average Order Amount per User:** 
  - Calculates the average amount spent per order for each user.

- **Retrieve Shipping Details for a Specific Order:** 
  - Shows shipping details for a specific order.

- **Find Products that Are Out of Stock:** 
  - Lists products that have no stock remaining.

- **Get Total Number of Orders Placed Each Day:** 
  - Provides a count of orders placed on each day.

- **Retrieve Details of the Latest Order for Each User:** 
  - Shows details of the most recent order placed by each user.


# E-Commerce Database

## 1. Database Creation

```sql
-- Create the database
CREATE DATABASE ECommerceDB;

-- Use the database
USE ECommerceDB;


-- Create Categories table
CREATE TABLE Categories (
    CategoryID INT AUTO_INCREMENT PRIMARY KEY,
    CategoryName VARCHAR(50)
);

-- Create Users table
CREATE TABLE Users (
    UserID INT AUTO_INCREMENT PRIMARY KEY,
    Username VARCHAR(50) UNIQUE,
    PasswordHash VARCHAR(255),
    Email VARCHAR(100) UNIQUE,
    FullName VARCHAR(100),
    Address TEXT,
    PhoneNumber VARCHAR(20)
);

-- Create Products table
CREATE TABLE Products (
    ProductID INT AUTO_INCREMENT PRIMARY KEY,
    ProductName VARCHAR(100),
    Description TEXT,
    Price DECIMAL(10, 2),
    StockQuantity INT,
    CategoryID INT,
    FOREIGN KEY (CategoryID) REFERENCES Categories(CategoryID)
);

-- Create Orders table
CREATE TABLE Orders (
    OrderID INT AUTO_INCREMENT PRIMARY KEY,
    UserID INT,
    OrderDate DATETIME,
    Status ENUM('Pending', 'Shipped', 'Delivered', 'Cancelled'),
    TotalAmount DECIMAL(10, 2),
    FOREIGN KEY (UserID) REFERENCES Users(UserID)
);

-- Create OrderItems table
CREATE TABLE OrderItems (
    OrderItemID INT AUTO_INCREMENT PRIMARY KEY,
    OrderID INT,
    ProductID INT,
    Quantity INT,
    Price DECIMAL(10, 2),
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID)
);

-- Create Payments table
CREATE TABLE Payments (
    PaymentID INT AUTO_INCREMENT PRIMARY KEY,
    OrderID INT,
    PaymentDate DATETIME,
    PaymentAmount DECIMAL(10, 2),
    PaymentMethod ENUM('Credit Card', 'PayPal', 'Bank Transfer'),
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID)
);

-- Create Shipping table
CREATE TABLE Shipping (
    ShippingID INT AUTO_INCREMENT PRIMARY KEY,
    OrderID INT,
    ShippingAddress TEXT,
    ShippingDate DATETIME,
    DeliveryDate DATETIME,
    ShippingMethod ENUM('Standard', 'Express'),
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID)
);


-- Insert data into Users
INSERT INTO Users (Username, PasswordHash, Email, FullName, Address, PhoneNumber)
VALUES 
    ('johndoe', 'hashed_password1', 'john@example.com', 'John Doe', '123 Elm Street, Springfield', '555-1234'),
    ('janesmith', 'hashed_password2', 'jane@example.com', 'Jane Smith', '456 Oak Avenue, Springfield', '555-5678');

-- Insert data into Categories
INSERT INTO Categories (CategoryName)
VALUES 
    ('Electronics'),
    ('Clothing'),
    ('Home & Kitchen');

-- Insert data into Products
INSERT INTO Products (ProductName, Description, Price, StockQuantity, CategoryID)
VALUES 
    ('Smartphone', 'Latest model smartphone with advanced features', 699.99, 50, 1),
    ('Laptop', 'High-performance laptop for gaming and work', 1199.99, 30, 1),
    ('T-shirt', 'Comfortable cotton t-shirt', 19.99, 100, 2),
    ('Blender', 'High-speed blender for smoothies', 89.99, 20, 3);

-- Insert data into Orders
INSERT INTO Orders (UserID, OrderDate, Status, TotalAmount)
VALUES 
    (1, '2024-08-01 14:30:00', 'Pending', 719.98),
    (2, '2024-08-02 10:00:00', 'Shipped', 1109.98);

-- Insert data into OrderItems
INSERT INTO OrderItems (OrderID, ProductID, Quantity, Price)
VALUES 
    (1, 1, 1, 699.99),
    (1, 4, 1, 89.99),
    (2, 2, 1, 1199.99);

-- Insert data into Payments
INSERT INTO Payments (OrderID, PaymentDate, PaymentAmount, PaymentMethod)
VALUES 
    (1, '2024-08-01 15:00:00', 719.98, 'Credit Card'),
    (2, '2024-08-02 11:00:00', 1199.99, 'PayPal');

-- Insert data into Shipping
INSERT INTO Shipping (OrderID, ShippingAddress, ShippingDate, DeliveryDate, ShippingMethod)
VALUES 
    (1, '123 Elm Street, Springfield', '2024-08-02 12:00:00', NULL, 'Standard'),
    (2, '456 Oak Avenue, Springfield', '2024-08-03 09:00:00', '2024-08-05 17:00:00', 'Express');

-- Retrieve all products with their category names
SELECT p.ProductID, p.ProductName, p.Description, p.Price, p.StockQuantity, c.CategoryName
FROM Products p
JOIN Categories c ON p.CategoryID = c.CategoryID;

-- Get the total sales amount for each product
SELECT p.ProductName, SUM(oi.Quantity * oi.Price) AS TotalSales
FROM OrderItems oi
JOIN Products p ON oi.ProductID = p.ProductID
GROUP BY p.ProductName;

-- Find the top 5 most expensive products
SELECT ProductName, Price
FROM Products
ORDER BY Price DESC
LIMIT 5;

-- Retrieve all orders and their payment status
SELECT o.OrderID, u.FullName AS Customer, o.OrderDate, o.Status AS OrderStatus, p.PaymentMethod, p.PaymentAmount
FROM Orders o
JOIN Users u ON o.UserID = u.UserID
LEFT JOIN Payments p ON o.OrderID = p.OrderID
ORDER BY o.OrderDate DESC;

-- List all users who have placed an order
SELECT DISTINCT u.UserID, u.FullName, u.Email
FROM Users u
JOIN Orders o ON u.UserID = o.UserID;

-- Get the average order amount per user
SELECT u.FullName, AVG(o.TotalAmount) AS AverageOrderAmount
FROM Orders o
JOIN Users u ON o.UserID = u.UserID
GROUP BY u.FullName;

-- Retrieve shipping details for a specific order
SELECT o.OrderID, s.ShippingAddress, s.ShippingDate, s.DeliveryDate, s.ShippingMethod
FROM Orders o
JOIN Shipping s ON o.OrderID = s.OrderID
WHERE o.OrderID = 1;

-- Find products that are out of stock
SELECT ProductName, StockQuantity
FROM Products
WHERE StockQuantity = 0;

-- Get the total number of orders placed each day
SELECT DATE(OrderDate) AS OrderDate, COUNT(*) AS NumberOfOrders
FROM Orders
GROUP BY DATE(OrderDate)
ORDER BY OrderDate;

-- Retrieve details of the latest order for each user
SELECT u.FullName, o.OrderID, o.OrderDate, o.TotalAmount
FROM Orders o
JOIN Users u ON o.UserID = u.UserID
WHERE o.OrderDate = (SELECT MAX(OrderDate) FROM Orders WHERE UserID = u.UserID);
