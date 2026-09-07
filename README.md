# Flipkart E-Commerce Database System

A relational database project modeling the core data and workflows behind an e-commerce platform like Flipkart — customers, sellers, products, orders, and payments — built in MySQL/MariaDB syntax.

## 📄 Project Documents

This repository/submission contains two companion documents:

| Document | Purpose |
|---|---|
| **Requirement Analysis – E-Commerce Database System.pdf** | The design phase: business process breakdown, entity descriptions, attribute tables, and the ER diagram that the schema is built from. |
| **Creation and Insertion – Flipkart Database.pdf** | The implementation phase: actual SQL statements used to create the database, create tables, insert sample data, and run update/delete operations, with query result screenshots. |

Read the Requirement Analysis document first — it explains *why* the schema looks the way it does — then use the Creation and Insertion document as the executable reference.

## 🗂️ Database Overview

**Database name:** `FlipkartDB`

The system models five real-world business stages:
1. Customer registration and profile setup
2. Product browsing and search
3. Cart management and order placement
4. Payment processing
5. Order fulfilment, delivery, and returns

## 🧩 Entity-Relationship Summary

| Entity | Primary Key | Foreign Key(s) | Description |
|---|---|---|---|
| **Customer** | `CustomerID` | — | Registered platform users |
| **Seller** | `SellerID` | — | Merchants/vendors listing products |
| **Product** | `ProductID` | `SellerID` → Seller | Items listed for sale |
| **Orders** | `OrderID` | `CustomerID` → Customer | Purchase transactions |
| **Payment** | `PaymentID` | `OrderID` → Orders | Financial transaction per order |

> The full requirement-analysis ER diagram also models `Address`, `Review`, and `Order_Item` as extended entities for a production-scale design; the implemented schema in this project covers the core five tables (Customer, Seller, Product, Orders, Payment) with one order mapped to one total amount rather than itemized line items.

### Relationships
- A **Customer** places many **Orders** (1:N)
- A **Seller** lists many **Products** (1:N)
- An **Order** is settled by one **Payment** (1:1)

## 🛠️ Schema (DDL Summary)

```sql
CREATE DATABASE FlipkartDB;
USE FlipkartDB;

CREATE TABLE Customer (
  CustomerID INT PRIMARY KEY,
  Name VARCHAR(100),
  Email VARCHAR(100) UNIQUE,
  Phone VARCHAR(15) UNIQUE,
  Gender VARCHAR(10),
  DateOfBirth DATE
);

CREATE TABLE Seller (
  SellerID INT PRIMARY KEY AUTO_INCREMENT,
  SellerName VARCHAR(100),
  GSTIN VARCHAR(20) UNIQUE,
  ContactEmail VARCHAR(100)
);

CREATE TABLE Product (
  ProductID INT PRIMARY KEY AUTO_INCREMENT,
  SellerID INT,
  ProductName VARCHAR(150),
  Category VARCHAR(50),
  Price DECIMAL(10,2),
  StockQty INT,
  FOREIGN KEY (SellerID) REFERENCES Seller(SellerID)
);

CREATE TABLE Orders (
  OrderID INT PRIMARY KEY AUTO_INCREMENT,
  CustomerID INT,
  OrderDate DATETIME,
  OrderStatus VARCHAR(20),
  TotalAmount DECIMAL(10,2),
  FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID)
);

CREATE TABLE Payment (
  PaymentID INT PRIMARY KEY AUTO_INCREMENT,
  OrderID INT,
  PaymentMode VARCHAR(20),
  PaymentStatus VARCHAR(20),
  PaymentDate DATETIME,
  Amount DECIMAL(10,2),
  FOREIGN KEY (OrderID) REFERENCES Orders(OrderID)
);
```

## 📥 Sample Data

Each table is seeded with 10 sample records:
- **Customer**: 10 users (IDs 101–110)
- **Seller**: 10 merchants across categories like Electronics, Fashion, Books, Sports (IDs 201–210)
- **Product**: 10 products, one per seller (IDs 301–310)
- **Orders**: 10 orders, one per customer, with statuses `Placed`, `Shipped`, `Delivered`, `Cancelled` (IDs 401–410)
- **Payment**: 10 payments, one per order, with modes `UPI`, `Card`, `Net Banking`, `COD` and statuses `Success`, `Pending`, `Refunded` (IDs 501–510)

## 🔄 CRUD Operations Demonstrated

| Operation | Example in this project |
|---|---|
| **Create** | `INSERT INTO` statements populating all five tables |
| **Read** | `SELECT * FROM <table>` after each insert |
| **Update** | Renaming Customer 101 from `Ganesh` to `Ganesh Kumar` |
| **Delete** | Cascading manual deletion of Payment → Orders → Customer for OrderID 410 / CustomerID 110, respecting foreign-key order |

> Note: Deletes are performed child-table-first (Payment, then Orders, then Customer) because foreign keys prevent deleting a parent row while dependent child rows still reference it.

## ▶️ How to Run

1. Open a MySQL/MariaDB client (e.g., MySQL Workbench, phpMyAdmin, or the CLI).
2. Run the statements from **Section 1** of the Creation and Insertion document to create and select the database.
3. Run each table's `CREATE TABLE` statement **in dependency order**: `Customer` and `Seller` first, then `Product` (depends on `Seller`), then `Orders` (depends on `Customer`), then `Payment` (depends on `Orders`).
4. Run the corresponding `INSERT INTO` statements for each table.
5. Verify with `SELECT * FROM <table>;`.
6. Run the `UPDATE` and `DELETE` examples in Sections 7–8 to see modification and cascading-safe deletion in action.

## ✅ Key Design Principles Illustrated

- **Referential integrity** — foreign keys ensure an order can't reference a nonexistent customer, and a payment can't reference a nonexistent order.
- **Uniqueness constraints** — `Email`, `Phone` (Customer) and `GSTIN` (Seller) are enforced unique to prevent duplicate accounts/registrations.
- **Auto-incrementing surrogate keys** — used for `Seller`, `Product`, `Orders`, and `Payment` to simplify inserts as the system scales.
- **Separation of concerns** — each entity (Customer, Seller, Product, Orders, Payment) is normalized into its own table rather than flattened into one large table.

## 👤 Author

**Shree Pranava Ganesh N R**
II BCA, Batch I
