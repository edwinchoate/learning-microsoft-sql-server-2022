# Notes

## Ch. 1 Getting Started

SQL Server Instances
* Many Databases
* Many Users
	
User Roles
* System administrator - configures server, manages top-level user accounts
* Database administrator - creates and maintains db tables, performs backups, configures user permissions within the db
* Database user - accesses or modifies data in db (read-only in certain tables, write-only in certain tables, etc.)

SQL (Structured Query Language)

T-SQL (Transact-SQL) is the "dialect" that Microsoft uses 
* query
* filter
* sort
* add
* update
* retrieve

Editions of SQL Server
* Enterprise - unlimited processor and memory resources 
* Standard - limited to 24 GPU cores and 128 GB of memory per instance 
* Express - (free, even for commercial) limited to 16 processor cores and 64 GB of memory and is missing some advanced features
* Developer - Same features as Enterprise, but can only be used for development. Can also be used for educational purposes. 

Frontend interfaces
* SQL Server CLI
* SQL Server Management Studio (SSMS) from Microsoft
	
SQL Server Configuration Manager
* for server settings
* Start, Stop, Restart, Pause, etc. the server

## Ch. 2 Working with the Server

Server Names
* %hostname% (if only one instance)
* %hostname%\%instance_name% (if multiple instances)

Connecting to a Server
* File > Connect to Object Explorer...
* File > Disconnect Object Explorer
* Right click (on server) > Restart

Authentication

* Windows Authentication - uses the built-in Windows login to auth 
* SQL Server Authentication - you set up users/passwords in SQL Server
    * SSMS > Object Explorer > Right click (on server) > Properties > Security > "SQL Server and Windows Authentication mode"
* Active Directory 

Logins

* SSMS > Object Explorer > Security > Logins
* **sa** account - the system administrator
    * Right click > Properties > Status > Enabled/Disabled
    * Right click > Properties > General > Password
    * You have to enable SQL Server Authentication mode to use this account
* Right click (on Logins) > Refresh, to see updates

## Ch. 3 SQL Server Management Studio 

Right-click > Refresh - works on pretty much everything in the Object Explorer

SSMS Settings
* Tools > Options > Text Editor > Transact-SQL > Line numbers

Restoring a DB from a backup file (.bak)
* SSMS > Object Explorer > Right-click (on Databases) > Restore Database...
* SQL Server has a default directory for backup files 
    * `C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup`
* It's easiest if you just put the .bak file into the above default directory 

### Creating a New Database

SSMS > Object Explorer > Databases > Right click > New Database...

### Data Types

Text

`char(N)` - Text data of exactly N characters

`nchar(N)` - Unicode text data of exactly N characters 

`varchar(N)` - Text data with max:N characters

`nvarchar(N)` - Unicode text data with max:N characters 

Numbers

`tinyint` - an integer from 0 to 255

`int` - an integer from -2 billion to 2 billion

`decimal` - decimal point number. you can specify the significant digits you want to store before or after the decimal

Specialized 

* Date
* Currency
* Geographic coordinates 
* etc

[MS Docs: Data types (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver16)

### Creating New Tables 

Using the SSMS UI,
* Databases > Right click > New Databases
* To set the Primary Key col, selet the column and then click the key icon in the toolbar. A little key icon shoudl appear next to the column.
* Save to set the Table name
* You can get back to the table definition via Right-click (on specific table) > Design 

### Inserting Data Using the SSMS UI

* Right click (on specific table) > Edit Top 200 Rows
* Click the pencil or press enter to commit edits

### Importing Data Files

1. Right click (on database name) > Tasks > Import Flat File...
2. Choose file (ex: .csv)
3. Edit col definitions as needed

## Ch. 4 Write Transact-SQL Commands 

### SELECT

```SQL 
SELECT * FROM Sales.Orders;
```

```SQL
SELECT OrderDate, CustomerPurchaseOrderNumber, Comments
FROM Sales.Orders;
```

### Sort

```SQL 
SELECT OrderDate, CustomerPurchaseOrderNumber, Comments
FROM Sales.Orders
ORDER BY OrderDate ASC;
```

```SQL 
SELECT OrderDate, CustomerPurchaseOrderNumber, Comments
FROM Sales.Orders
ORDER BY OrderDate DESC;
```

### Filter

```SQL
SELECT * FROM Sales.Orders
WHERE CustomerID = 500;
```

### Functions

You can put calculations into your queries:

```SQL
SELECT Quantity * UnitPrice
FROM Sales.OrderLines;
```

You can give your calculations a name:

```SQL
SELECT (Quantity * UnitPrice) AS SubTotal
FROM Sales.OrderLines;
```

Formatting calculation output 

```SQL 
SELECT FORMAT(UnitPrice, 'C') -- 'C' means Currency
FROM Sales.OrderLines;
```

Count the number of rows in a query

```SQL 
SELECT COUNT(*)
FROM Sales.OrderLines
WHERE OrderID = 5;
```

[MS Docs: What are the SQL database functions?](https://learn.microsoft.com/en-us/sql/t-sql/functions/functions?view=sql-server-ver16)

### Aggregate Functions

You can do queries with aggregated data. 

* `COUNT()`
* `MIN()`
* `MAX()`
* `AVG()`
* `SUM()`

[MS Docs: Aggregate Functions (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/functions/aggregate-functions-transact-sql?view=sql-server-ver16)

If you attempt to mix aggregated data with non-aggregated data, however, you will get an error. Something like: 

> Column 'Sales.Orders.SalespersonPersonID' is invalid in the select list because it is not contained in either an aggregate function or the GROUP BY clause.

So, you have to make sure everything is aggregated in a valid way. 

```SQL
-- find the total # of invoices each customer has
SELECT CustomerID, COUNT(InvoiceID)
FROM Invoices
GROUP BY CustomerID;
```

### Insert

```SQL 
INSERT INTO Products
    (ProductName, ProductDescription, RetailPrice)
VALUES ('Rubik''s Cube', 'A 3D combination puzzle invented by Ernő Rubik.', 19.99);
```

### Joins

```SQL 
SELECT * 
FROM StockItems JOIN Suppliers
ON StockItems.SupplierID = Suppliers.SupplierID; 
```

Dealing with possible column name overlap:

```SQL 
SELECT (StockItems.SupplierID) AS SupplierID -- in case Suppliers also has a SupplierID column
FROM StockItems JOIN Suppliers
ON StockItems.SupplierID = Suppliers.SupplierID; 
```

### Updating Data

```SQL
UPDATE Sales.Deals
SET DealDescription = 'Now 50% off!', 
    EndDate = '12/31/2025'
WHERE DealID = 1;
```

### Views

You can save complex queries for reuse as a _view_. Views make complex queries that aren't tables faster and more conveninent to access. In other words, it's essential a way to "save" a really long complex query so that you can work with it as if it was a table. 

Saving a query as a _view_ object: 

```SQL 
CREATE VIEW ProductDetails AS

-- some arbitrary query
SELECT * FROM Products;
```

You can then use the view as if it was a table name:

```SQL
SELECT * FROM ProductDetails;
```

#### Creating Views with the SSMS UI 

Object Explorer > _Database Name_ > Views > Right-click > Create New View...

