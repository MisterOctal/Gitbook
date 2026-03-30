---
icon: database
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/66c513e4445cb5649e636a36/room-content/66c513e4445cb5649e636a36-1727686685940.png
coverY: 0
coverHeight: 138
---

# SQL Fundamentals

**Date:** 30.03.2024

**Room Category:** Walkthrough

**Core Concept:** Understanding relational database architecture, SQL syntax, and basic data manipulation.

Cyber security is a broad topic, but few subjects are as ubiquitous as databases. Whether you’re securing a web application, working in a SOC using a SIEM, or analyzing malware, you will rely on databases. On the offensive side, understanding SQL helps us exploit vulnerabilities like SQL Injection; on the defensive side, it allows us to navigate logs and find suspicious activity.

***

## Task 1: Introduction

<figure><img src="../.gitbook/assets/image (91).png" alt="" width="372"><figcaption></figcaption></figure>

Databases are organized collections of structured information. This room serves as a first step into understanding how data is stored, retrieved, and managed using Structured Query Language (SQL).

Question: Teach me the basics of SQL!

> **Answer:** No answer needed

***

## Task 2: Databases 101

Databases are organized collections of data. While there are many types, we focus on the two primary categories:

* **Relational Databases (SQL):** Store structured data in tables with rows and columns. Ideal for consistent formats like e-commerce transactions.
* **Non-Relational Databases (NoSQL):** Store data in non-tabular formats (like JSON documents). Ideal for varying data types like social media content.

<figure><img src="../.gitbook/assets/image (92).png" alt="" width="563"><figcaption></figcaption></figure>

### Tables, Rows and Columns

In a relational database, data is stored in **Tables**. Each table has **Columns** (defining the data type, like Integers or Strings) and **Rows** (representing individual records).

<figure><img src="../.gitbook/assets/image (93).png" alt="" width="563"><figcaption></figcaption></figure>

### Primary and Foreign Keys

* **Primary Key:** A unique identifier for a record in a table (e.g., a Student ID).
* **Foreign Key:** A column that provides a link between two tables, creating a relationship.

<figure><img src="../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

Question: What type of database should you consider using if the data you're going to be storing will vary greatly in its format?

> **Answer:** Non-relational databases

Question: What type of database should you consider using if the data you're going to be storing will reliably be in the same structured format?

> **Answer:** Relational databases

Question: In our example, once a record of a book is inserted into our "Books" table, it would be represented as a \_\_\_ in that table?

> **Answer:** row

Question: Which type of key provides a link from one table to another?

> **Answer:** Foreign Key

Question: which type of key ensures a record is unique within a table?

> **Answer:** Primary Key

***

## Task 3: SQL

SQL (Structured Query Language) is the standard language used to interact with relational databases. Interactions are handled by a **Database Management System (DBMS)**, such as MySQL, PostgreSQL, or MS SQL Server.

<figure><img src="../.gitbook/assets/image (95).png" alt="" width="375"><figcaption></figcaption></figure>

### Benefits of SQL

* **Speed:** Can return massive batches of data almost instantaneously.
* **Easy to Learn:** Uses plain English syntax.
* **Reliability:** Ensures data accuracy through strict structures.

<figure><img src="../.gitbook/assets/image (96).png" alt="" width="375"><figcaption></figcaption></figure>

Question: What serves as an interface between a database and an end user?

> **Answer:** DBMS

Question: What query language can be used to interact with a relational database?

> **Answer:** SQL

***

## Task 4: Database and Table Statements

This task focuses on the initial setup of a relational database environment. Before data can be queried or inserted, the structures (databases and tables) must be defined.

#### Database Operations

1. **CREATE DATABASE**: Used to initialize a new database container.
   * `CREATE DATABASE thm_bookmarket_db;`
2. **SHOW DATABASES**: Lists all databases available on the current MySQL server.
3. **USE**: Sets the "context" for subsequent commands.
   * `USE thm_bookmarket_db;`
4. **DROP DATABASE**: Deletes a database and all its contents permanently.

#### Table Operations

To store data, we create tables with defined columns and data types.

**Creating the Inventory Table:**

```sql
CREATE TABLE book_inventory (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    book_name VARCHAR(255) NOT NULL,
    publication_date DATE
);
```

* **INT AUTO\_INCREMENT**: Automatically assigns a unique number to each new row.
* **PRIMARY KEY**: Uniquely identifies each record.
* **VARCHAR(255)**: Variable character string with a 255-character limit.
* **NOT NULL**: Ensures the field cannot be left empty.

#### Modification and Inspection

* **DESCRIBE**: Shows the structure (schema) of a table, including types and keys.
  * `DESCRIBE book_inventory;`
* **ALTER TABLE**: Modifies an existing table structure.
  * `ALTER TABLE book_inventory ADD page_count INT;`
* **DROP TABLE**: Removes a table from the active database.

**Question:** Create a database named `thm_bookmarket_db`. What is the flag?

> **Answer:** THM{575a947132312f97b30ee5aeebba629b723d30f9}

**Question:** Create a table named `book_inventory` with the columns specified in the task. What is the flag?

> **Answer:** THM{692aa7eaec2a2a827f4d1a8bed1f90e5e49d2410}

***

## Task 5: CRUD Operations

CRUD stands for Create, Read, Update, and Delete. These are the four essential functions of persistent storage.

* **INSERT INTO**: Adds new rows to a table.
* **SELECT**: Retrieves data from a table. Use `*` for all columns or specify column names.
* **UPDATE**: Modifies existing records. Always use a `WHERE` clause to avoid updating all rows.
* **DELETE**: Removes records from a table.

Question: Using the tools\_db database, what is the name of the tool in the hacking\_tools table that can be used to perform man-in-the-middle attacks on wireless networks?

> **Answer:** Wi-Fi Pineapple

Question: Using the tools\_db database, what is the shared category for both USB Rubber Ducky and Bash Bunny?

> **Answer:** USB attacks

***

## Task 6: Clauses

Clauses provide specific criteria for data manipulation and retrieval, allowing for sorting and filtering.

* **DISTINCT**: Returns only unique values, filtering out duplicates.
* **GROUP BY**: Collects data across multiple records and groups the results by one or more columns. Often used with aggregate functions like `COUNT()`.
* **ORDER BY**: Sorts the result set in ascending (`ASC`) or descending (`DESC`) order.
* **HAVING**: Similar to `WHERE`, but used to filter groups created by the `GROUP BY` clause.

Question: Using the tools\_db database, what is the total number of distinct categories in the hacking\_tools table?

> **Answer:** 6

Question: Using the tools\_db database, what is the first tool (by name) in ascending order from the hacking\_tools table?

> **Answer:** Bash Bunny

Question: Using the tools\_db database, what is the first tool (by name) in descending order from the hacking\_tools table?

> **Answer:** Wi-Fi Pineapple

***

## Task 7: Operators

Operators allow for complex logic and comparisons within SQL queries.

* **Logical Operators**: `LIKE` (pattern matching with `%`), `AND` (all conditions must be true), `OR` (at least one condition true), `NOT` (reverses logic), and `BETWEEN` (range testing).
* **Comparison Operators**: `=`, `!=`, `<`, `>`, `<=`, `>=`.

Question: Using the tools\_db database, which tool falls under the Multi-tool category and is useful for pentesters and geeks?

> **Answer:** Flipper Zero

Question: Using the tools\_db database, what is the category of tools with an amount greater than or equal to 300?

> **Answer:** RFID cloning

Question: Using the tools\_db database, which tool falls under the Network intelligence category with an amount less than 100?

> **Answer:** Lan Turtle

***

## Task 8: Functions

Functions are built-in tools used to process and calculate data within the database.

* **String Functions**: `CONCAT()` (combine strings), `GROUP_CONCAT()` (combine rows into one string), `SUBSTRING()`, and `LENGTH()`.
* **Aggregate Functions**: `COUNT()`, `SUM()`, `MAX()`, and `MIN()`.

Question: Using the tools\_db database, what is the tool with the longest name based on character length?

> **Answer:** USB Rubber Ducky

Question: Using the tools\_db database, what is the total sum of the amount column?

> **Answer:** 1444

Question: Using the `tools_db` database, what are the tool names where the amount does not end in 0, and group the tool names concatenated by " & ".

> **Answer:** Flipper Zero & iCopy-XS

***

## Conclusion

We have mastered the foundational elements of SQL, from basic CRUD operations and clauses to complex logic with operators and data processing with functions. These skills are essential for interacting with any relational database.
