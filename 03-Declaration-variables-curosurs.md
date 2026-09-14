# Declaration Variables and Curosurs

- [Declaration Variables and Curosurs](#declaration-variables-and-curosurs)
  - [1-Declaring Variables](#1-declaring-variables)
    - [Declaration section](#declaration-section)
    - [Assigning Default Values](#assigning-default-values)
    - [Declaring Constants](#declaring-constants)
    - [Declaratio with %TYPE](#declaratio-with-type)
    - [Declaratio with %ROWTYPE](#declaratio-with-rowtype)
  - [2-Declaring Cursors](#2-declaring-cursors)
    - [**What is a Cursor?**](#what-is-a-cursor)
    - [**Explicit cursor**](#explicit-cursor)
      - [Explicit Cursor with FOR LOOP](#explicit-cursor-with-for-loop)
      - [Explicit Cursor with FETCH](#explicit-cursor-with-fetch)
    - [**Implicit cursor**](#implicit-cursor)

## 1-Declaring Variables
### Declaration section
Declaration variables section can begin in some ways:
1. Start with DELARE keyword use in Anonymouse block
   
```sql
    declare 
        var number := 1;
    begin 
        var := var + 1;
    end;
```
1. After keyword IS of creatung functions

```sql
    create function new_salary(...)
    return number is
        working_salary number;
    begin
    ...
    end;
```
3. After keyword IS of creatung procedures

```sql
    create procedure cnt_employees(...)
    is
        total_employees number;
    begin
    ...
    end;
```

### Assigning Default Values

Assigning default values will start at the begin of running time, and can change new values later.

```sql
DECLARE
    -- assign default value
    var NUMBER := 15;
BEGIN

    dbms_output.put_line('This variable var has default value is: ' || var);
    -- assign new value
    var := 30;
    dbms_output.put_line('This variable var has new value is: ' || var);

END;
```

### Declaring Constants

Declaring constant values will start at the begin of running time, and cannot be changed later.

```sql
DECLARE
    var NUMBER := 15;
    pi constant float := 3.14;
BEGIN

    dbms_output.put_line('This variable var has default value is: ' || var);
    dbms_output.put_line('This variable pi has constant value is: ' || pi);
    pi := 3.15; -- error becasue pi is a constant variable, and cannot be changed.
END;a
```

### Declaratio with %TYPE

Creating %TYPE declaration when we don't know the exact data type/length of a column in a table or columns that always change the data type.

```sql
DECLARE
  -- Declare a variable with the same data type as the last_name column in the employees table
    v_last_name employees.last_name%TYPE;
    CURSOR v_employees IS
    SELECT
        *
    FROM
        employees;

BEGIN
    FOR lastname IN v_employees LOOP
        v_last_name := lastname.last_name;
        dbms_output.put_line('employee last name: ' || v_last_name);
    END LOOP;
END;
```


### Declaratio with %ROWTYPE

Creating %ROWTYPE declaration to reference to data type/length all columns in a specific table.

```sql
DECLARE
    v_emp employees%rowtype;
BEGIN
    SELECT
        *
    INTO v_emp
    FROM
        employees
    WHERE
        employee_id = 100;

    dbms_output.put_line(v_emp.employee_id
                         || ' '
                         || v_emp.first_name
                         || ' ' 
                         || v_emp.last_name);

END;
/
```

---

## 2-Declaring Cursors

Format of a PL/SQL procedure and where the cursors are declared:

```sql
[declare 
-- declaration #1
-- declaration #2
-- cursor declaration #1
]
begin
    -- statement #1
    -- statement #2 with cursor
    -- statement #n
    return [return_value];
    [exception
        -- statements to run
        -- when errors occur
    ]
end;
```

### **What is a Cursor?**

**Cursor**: is a pointer to a private SQL area that stores information about processing a specific SELECT or DML statement.
We can declare cursors either in the declaration section or directly in the code section within BEGIN...END.
There are 2 types of cursurs: `explicit cursor, and Implicit cursor`.
 
Example of declaring cursor:
```sql
-- Declaring cursor in declaraction section
DECLARE
    v_last_name varchar2(1000);
    CURSOR emp_cursor IS -- cursor declaration
    SELECT
        *
    FROM
        employees;

BEGIN
    FOR lastname IN emp_cursor LOOP
        v_last_name := lastname.last_name;
        dbms_output.put_line('Employee last name: ' || v_last_name);
    END LOOP;
END;

-- Declaring cursor in code section between BEGIN...END
DECLARE
    v_last_name varchar2(1000);
BEGIN
    FOR lastname IN (SELECT * FROM employees) -- surcor declaration
    LOOP
        v_last_name := lastname.last_name;
        dbms_output.put_line('Employee last name: ' || v_last_name);
    END LOOP;
END;
```

### **Explicit cursor** 
Explicit cursor is a session cursor that you construct and manage. You must declare and define an explicit cursor, giving it a name and associating it with a query (typically, the query returns multiple rows). Then you can process the query result set in either of these ways:
  - Open the explicit cursor (with the OPEN statement), fetch rows from the result set (with the FETCH statement), and close the explicit cursor (with the CLOSE statement).
  - Use the explicit cursor in a cursor FOR LOOP statement
  
#### Explicit Cursor with FOR LOOP
With a cursor FOR LOOP, Oracle automatically performs OPEN, FETCH, and CLOSE. Therefore, we do not write FETCH manually.
```sql
DECLARE
    -- Explicit cursor
    CURSOR emp_cursor IS
        SELECT employee_id, last_name
        FROM employees
        WHERE department_id = 50
        ORDER BY employee_id;
BEGIN
    -- Oracle automatically opens and fetches from the cursor
    FOR emp_record IN emp_cursor LOOP
        DBMS_OUTPUT.PUT_LINE(
            emp_record.employee_id || ' - ' ||
            emp_record.last_name
        );
    END LOOP;

    -- Oracle automatically closes the cursor
END;
/

```

#### Explicit Cursor with FETCH
Use `FETCH` when you manually control an explicit cursor or cursor variable and want to `retrieve its result one row at a time`.
```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, last_name
        FROM employees WHERE employee_id < 110 ;

    v_emp emp_cursor%ROWTYPE;
BEGIN
    OPEN emp_cursor;

    LOOP
        FETCH emp_cursor INTO v_emp;
        EXIT WHEN emp_cursor%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE(
            v_emp.employee_id || ' - ' || v_emp.last_name
        );
    END LOOP;

    CLOSE emp_cursor;
END;
-- output
100 - King
101 - Yang
102 - Garcia
103 - James
104 - Miller
105 - Williams
106 - Jackson
107 - Nguyen
108 - Gruenberg
109 - Faviet
```

### **Implicit cursor** 
Implicit cursor is a session cursor that is constructed and managed by PL/SQL. PL/SQL opens an implicit cursor every time you run a SELECT or DML statement. You cannot control an implicit cursor, but you can get information from its attributes by using built-in SQL% variables.

Using SQL% Built-in variables:
```sql
BEGIN
    UPDATE employees
    SET
        salary = 1000
    WHERE
        department_id = 50;

    row_update_cnt := SQL%rowcount;
    dbms_output.put_line('number of row updated is: ' || row_update_cnt);
END;
```

**Learn more:** https://docs.oracle.com/en/database/oracle/oracle-database/26/lnpls/cursors-overview.html#GUID-89E0242F-42AC-4B21-9DF1-ACD6F4FC03B9