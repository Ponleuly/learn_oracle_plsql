# Learning Oracle Database 19c: PL/SQL 

- [Learning Oracle Database 19c: PL/SQL](#learning-oracle-database-19c-plsql)
  - [1-Introduction](#1-introduction)
  - [2-PL/SQL Elements](#2-plsql-elements)

--- 

**Goals:** learn pl/sql to know its strucuture and its objective to use to write a block of script for query rows count from table partition. Main objects want to understand and use:
- Run sql block script
- Declare variables
- Loops
- Outout
- Error exception
- Get reselt

--- 



## 1-Introduction

- You will learn to use PL/SQL, Oracle's procedural language, to write efficient programs that integrate closely with the database, enabling automation and enforcement of business rules.
- The course covers essential programming constructs such as loops, conditional statements, exception handling, and how to create and use procedures and functions.
- It also addresses advanced topics like PL/SQL security models, running SQL and DDL statements within PL/SQL, and leveraging user-defined types for complex data handling.

## 2-PL/SQL Elements

**What is Oracle 19c PL/SQL?**

- **PL/SQL(Procedural Language):** is a programming language built in integrated with SQL in Oracle Database 19c.

- **PL/SQL works closely with SQL:**
  - All data types are interchange: used by one are available to outher.
  - Using %TYPE or %ROWTYPE with structure that often changes or unsure at run time.

- Process rows one at a time 
- Integrate PL/SQL function in WITH cluase
- Support static and dynamic SQL statments

**SQL vs PL/SQL?**

- **SQL is declarative:** "Tell database what to do!"

- **PL/SQL is procedural:** "Tell database how to do it step-by-step!"

**PL/SQL feature includes:**
  - Procedures: process zero or more arguments in a stored block of code
  - Functions: process zero or more variables and return a variable of any data type
  - Variable declarations: common data types or cursors
  - Loops such as FOR, WHILE, IF-THEN-ELSE
  - Exception handling to find errors

**PL/SQL offers several key benefits:**

- It gives precise control over how database operations are performed, allowing you to write complex, step-by-step logic beyond simple SQL queries.
- Automate repetitive tasks and enforce business rules consistently through stored procedures and functions.
- Exception handling in PL/SQL helps manage errors gracefully, making your database applications more robust.
- It supports dynamic SQL execution and tight integration with SQL, improving flexibility and performance.