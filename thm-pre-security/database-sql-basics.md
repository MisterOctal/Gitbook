---
icon: database
---

# Database SQL Basics

Written on: 15.03.2026

**Room Category:** Walkthrough

**Core Concept:** An introduction to relational databases, tables, and basic SQL queries.

Understanding how data is stored and retrieved is a fantastic skill to pick up. Most applications rely on a database backend to function, and knowing how to interact with that data directly gives you a much better grasp of how the application works as a whole. Getting comfortable with basic queries now will definitely be helpful when analyzing data or looking for injection vulnerabilities later on.

***

### Task 1: Introduction

This room introduces the concept of databases, which are essentially organized collections of data. While there are many types of databases, this room focuses on relational databases, which organize data into structured tables. Structured Query Language (SQL) is the standard language used to communicate with these relational databases.

Question: I am ready to dive into the database!

> **Answer:** No answer needed

***

### Task 2: Understanding Tables, Rows, and Columns

Relational databases store data in tables, which look very similar to standard spreadsheets. Each table is made up of columns (which define the type of data, like a name or a price) and rows (which represent an individual record or entry). Understanding this grid structure is key to figuring out how to retrieve the exact piece of information you need from a database.

Question: Inside databases, what is the term for the "spreadsheets" that store the information?

> **Answer:** table

***

### Task 3: Writing Your First SQL Query

Now we get to actually write some SQL. The most common command is `SELECT`, which allows you to retrieve data. You can pair it with `FROM` to specify the table, `WHERE` to filter results, and `ORDER BY` to sort them.

Step 1: Try this query to see every order currently stored in the database.

```sql
SELECT * FROM Orders;
```

Step 2: Show Only Specific Columns (Select Drink, Price). Sometimes we do not need every column. We can choose specific columns by listing them after `SELECT`. Try this query:

```sql
SELECT drink, price FROM Orders;
```

This will display only the drink and price columns.

Step 3: Filter Results (Where). The `WHERE` keyword filters rows. It keeps only rows that match a condition. Try filtering by drink name:

```sql
SELECT * FROM Orders WHERE drink = 'Coffee';
```

If the database contains coffee orders, you will now see only those rows.

Hint: If you are not sure which drink names exist, run:

```sql
SELECT * FROM Menu;
```

Step 4: Sort Results (Order By). The `ORDER BY` keyword sorts results by a column. By default, results are sorted in ascending order (lowest to highest). Try sorting orders by price (lowest first):

```sql
SELECT * FROM Orders ORDER BY price;
```

To sort in reverse order (highest to lowest), add `DESC`. Try sorting orders by price (highest first):

```sql
SELECT * FROM Orders ORDER BY price DESC;
```

Step 5: Combine Filtering + Sorting. Most real queries combine parts together. Here, we filter to keep only one drink type and then sort by price. Try this query:

```sql
SELECT * FROM Orders WHERE drink = 'Coffee' ORDER BY price DESC;
```

Question: When you showed all orders, how many rows were returned?

> **Answer:** 50

Question: When you sorted orders by price from cheapest to most expensive, which drink appeared first?

> **Answer:** Tea

Question: When you sorted the menu by price from most expensive to cheapest, which drink appeared first?

> **Answer:** Latte

***

### Conclusion

This room provided a solid, hands on introduction to interacting with relational databases. Understanding tables, rows, columns, and how to write basic SELECT queries forms the foundation for almost all database interactions. Knowing how to efficiently query and sort data is a highly practical skill that will absolutely come in handy.
