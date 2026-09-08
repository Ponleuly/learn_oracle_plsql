# Procedures and Functions

## 1-Built-in Functions

### **Single-Row Functions**

**5 basic categories of the built in functions in Oracle 19c:**
- Character 
- Numberic
- Date (arithmetic-Add or subtract time)
- Conversion
- General

**Where Single-Row Functions are used?**
- SELECT cluase in the column list
- WHERE clause
- START WITH/CONNECT BY clause
- GROUP BY clause
- HAVING clause: after an explicit or implicit GROUP BY clause

### 1. Character Functions

Definition:
Character functions manipulate character data and return either a character or numeric value.

Common functions:
- UPPER: Converts text to uppercase.
- LOWER: Converts text to lowercase.
- INITCAP: Capitalizes the first letter of every word.
- SUBSTR: Extracts part of a string.
- LENGTH: Returns the number of characters.
- INSTR: Returns the position of a character or substring.


### 2. Numeric Functions

Definition:
Numeric functions accept numeric input and return numeric values.

Common functions:
- ROUND: Rounds a number.
- TRUNC: Removes decimal digits without rounding.
- MOD: Returns the division remainder.
- ABS: Returns the absolute value.
- CEIL: Returns the smallest integer greater than or equal to a number.
- FLOOR: Returns the largest integer less than or equal to a number.

### 3. Datetime Functions

Definition:
Datetime functions operate on DATE, TIMESTAMP, and INTERVAL values.

Common functions:
- SYSDATE: Returns the database server's current date and time.
- ADD_MONTHS: Adds or subtracts months.
- MONTHS_BETWEEN: Returns the number of months between two dates.
- LAST_DAY: Returns the last day of a month.
- NEXT_DAY: Returns the next specified weekday.
Note:
The displayed result depends on the session's NLS_DATE_FORMAT.

### 4. Conversion Functions

Definition:
Conversion functions convert a value from one datatype to another datatype.

Common functions:
- TO_CHAR: Converts a date or number to character data.
- TO_DATE: Converts character data to a DATE.
- TO_NUMBER: Converts character data to a number.
- CAST: Converts an expression to a specified datatype.

`
NUMBER <-> CHARACTER <-> DATE
`

### 5. General

Definition:
This basic learning category combines functions that handle NULL values, compare values, decode data, or return environment information.

- General comparison functions
- Large object functions
- Collection functions
- XML functions
- JSON functions
- Encoding and decoding functions

**Learn more from Oracle Official 19c doc:** https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Single-Row-Functions.html#GUID-AC0E8A99-5097-4147-8295-C88EAC5AA362

----

## 2-Built-in PL/SQL Procedures

Built-in PL/SQL procedures are predefined programs supplied by Oracle Database to perform specific database operations.

Most built-in procedures are organized inside Oracle-supplied packages and are called using:
`
package_name.procedure_name
`

### Common Built-in PL/SQL Procedures

- DBMS_OUTPUT.PUT_LINE
  - Displays messages and variable values from PL/SQL code.

- DBMS_STATS.GATHER_TABLE_STATS
  - Collects optimizer statistics for a table and its indexes.

- DBMS_SCHEDULER.CREATE_JOB
  - Creates and schedules a database job.

- DBMS_LOCK.SLEEP
  - Pauses the current PL/SQL session for a specified duration.

- UTL_FILE.PUT_LINE
  - Writes a line of text to an operating-system file through an Oracle directory object.


**Keys:**
```
- Use built-in functions in SELECT statements.
- Built-in functions are categoried into what they retune: numbers, strings, or converted values,...
- built-in PL/SQL procedures are generally delivered in a package.
- Procedure performs an operation and does not need to return a value. Function must return a value.

```

---

## 3-Creating Functions

```sql
CREATE or REPLACE FUNCTION func1 RETURN INT
IS
BEGIN
    RETURN 1;
END;
/

SELECT func1 FROM dual;

-- output: 1
```

Example below showed that function could be compiled without erorr, but result from Function is error because the logic in the function.
```sql
CREATE OR REPLACE FUNCTION func1 RETURN INT
IS
BEGIN
    null;
END;
/

SELECT func1 FROM dual;

-- output: error becuase function not return a value of proper type.
```

### Privilege to run fuctions;
- CREATE PROCEDURE privilege for the own schema
- CREATE ANY PROCEDURE privilege to create a function any where
- EXECUTE object privilege to run an existing function
  
### Key section of a function:
- **Function header**: Function name, parameters name and data types, and data type of return value.
- **Declaration section**: Where to declare variables needed for the function's processing.
- **Function body**: Contains the executable code between BEGIN and END, where the logic runs and ends with a RETURN statement to send back a value.

### Sample functions return number value:
```sql
CREATE OR REPLACE FUNCTION new_salary_for_dept (current_salary NUMBER, dept_id number)
RETURN NUMBER IS
    update_salary NUMBER;
BEGIN
    update_salary := current_salary * 2;
    IF dept_id = 50 THEN
        update_salary := current_salary * 3;
    END IF;
    RETURN update_salary;
END;
/

SELECT first_name, salary, department_id, new_salary_for_dept(salary, department_id)
FROM hr.employees
ORDER BY department_id;
```



CREATE or replace FUNCTION show_dept_name (dept_id NUMBER) 
RETURN varchar IS
    dept_name varchar(100);
BEGIN 
    SELECT department_name FROM hr.departments
    WHERE department_id = dept_id;
    return dept_name;
END;

