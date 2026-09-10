# Security Model in PL/SQL

The secuirty model for running PL/SQL procedure can be broken down into 2 categories: `invoker rights and definer rights`.

## 1-Invoker Rights, Definer Rights

- **Definer rights:** runs under privilege of procedure owner (`default`).
- **Invoker rights:** runs under privilege of the user running the procedure(not the owner of procedure). Requiring that user to have `EXECUTE privileges`. 

`Using AUTHID to define invoker rights or definer right in procedure.`

Example1: Procedure create and run under privilege of definer rights, table in code also under privilege of definer:

```sql
-- Create by user HR (owner of table employees)
CREATE OR REPLACE PROCEDURE pro_cnt_emp
    AUTHID definer
IS
    cnt_emp NUMBER;
BEGIN
    SELECT
        COUNT(*)
    INTO cnt_emp
    FROM
        employees;

    dbms_output.put_line('total emplyoees is: ' || cnt_emp);
END;

GRANT EXECUTE ON pro_cnt_emp TO oe;

-- Execute by user OE
EXECUTE pro_cnt_emp;
-- Output: Total emplyoees is: 107
```