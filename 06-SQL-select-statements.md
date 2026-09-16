# **Using SQL Select Statements**

- [**Using SQL Select Statements**](#using-sql-select-statements)
  - [1-Using SELECT INTO and SQL%](#1-using-select-into-and-sql)
    - [Key features of PL/SQL with Oracle SQL](#key-features-of-plsql-with-oracle-sql)
    - [PL/SQL Architecture with SQL](#plsql-architecture-with-sql)
    - [DML in PL/SQL](#dml-in-plsql)
    - [SELECT INTO](#select-into)
    - [DML Built-in Variables](#dml-built-in-variables)


## 1-Using SELECT INTO and SQL%

### Key features of PL/SQL with Oracle SQL

- PL/SQL intergrated with Oracle SQL feature such as `cursor`, `for loop`, `if-then`, and so on.
- Using `select statements` in PL/SQL.
- Using `DML(Data Manipulation Language)`: insert, update, delete, merge, and so on.
- Can `run DML` statements `without changing syntax` inside PL/SQL
- Process `select results` in useful ways like put into PL/SQL variable using `INTO` for single row or `BULK COLLECT INTO` for multiple rows.
- Use `DML built-in variable` such as: SQL%ROWCOUNT, SQL%NOTFOUND, SQL%FOUND, SQL%ROWCOUNT, % vairable scope, and so on.  

---

### PL/SQL Architecture with SQL

![plsql-architecture-with-sql](slide/plsql_architecture_sql.png)

### DML in PL/SQL
- Intergration DML statements in PL/SQL:

![dml-intergrated-plsql-example1](slide/DML_in_plsql_1.png)

- Intergration DML statements in PL/SQL which is not very useful, because there is no something to do with select results:

![dml-intergrated-plsql-example2](slide/DML_in_plsql_2.png)

---

### SELECT INTO

![select-into-syntax](slide/select_into_syntax.png)

- Using `select into` for returning a signle rows.

Example:
```sql
DECLARE
    -- Variable datatype matches the LAST_NAME column
    v_last_name employees.last_name%TYPE;
BEGIN
    -- SELECT INTO retrieves exactly one row
    SELECT last_name
    INTO v_last_name          -- Store the selected value in the variable
    FROM employees
    WHERE employee_id = 100;  -- Use a unique condition to return one row

    DBMS_OUTPUT.PUT_LINE(
        'Employee: ' || v_last_name
    );
END;
/

-- output:
Employee: King

```
- Using `bulk collect into` for returning multples rows.

Example:
```sql
DECLARE
    -- Step 1: Define a collection type
    TYPE name_list IS
        TABLE OF employees.last_name%TYPE;

    -- Step 2: Declare a collection variable
    v_last_names name_list;
BEGIN
    -- Step 3: Store multiple query results in the collection
    SELECT last_name
    BULK COLLECT INTO v_last_names
    FROM employees
    WHERE department_id = 50;

    -- Step 4: Access the collection elements
    FOR i IN 1 .. v_last_names.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE(v_last_names(i));
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Total employees last name found: ' || v_last_names.COUNT);
END;
/
-- output:
Employees 1 last_name : Grant
Employees 2 last_name : Weiss
Employees 3 last_name : Fripp
.
.
.
Employees 44 last_name : Feeney
Employees 45 last_name : OConnell
Total employees last name found: 45
```

---

### DML Built-in Variables

- `SQL%ROWCOUNT`: show number of affected rows by the DML.
- `SQL%NOTFOUND`: show bolean value(TRUE/FALSE) of rows that met the where clause were not found.
- `SQL%FOUND`: show bolean value(TRUE/FALSE) of rows were found that met the where clause.
- `% variable scope`: always reflect the status of the last SQL statement run, but if want to keep their values for later, need to store them in declared variables.

Example:
```sql
BEGIN
    -- Update the salary of employees in department 10000
    UPDATE employees
    SET salary = 20000
    WHERE department_id = 10000;

    -- SQL%FOUND returns TRUE when at least one row was updated
    IF SQL%FOUND THEN

        -- SQL%ROWCOUNT returns the total number of rows updated
        DBMS_OUTPUT.PUT_LINE(
            'Total employees updated: ' || SQL%ROWCOUNT
        );

    -- SQL%NOTFOUND returns TRUE when no rows were updated
    ELSIF SQL%NOTFOUND THEN
        DBMS_OUTPUT.PUT_LINE(
            'No employees were updated.'
        );
    END IF;

    -- Undo the update after testing to preserve the original data
    ROLLBACK;
END;
/

-- output: 
No employees were updated.
```

---

