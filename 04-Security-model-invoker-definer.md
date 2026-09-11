# Security Model in PL/SQL

The secuirty model for running PL/SQL procedure can be broken down into 2 categories: `invoker rights and definer rights`.

## 1-Invoker Rights, Definer Rights

- **Definer rights:** runs under privilege of procedure owner (`default`).
- **Invoker rights:** runs under privilege of the user running the procedure(not the owner of procedure). Requiring that user to have `EXECUTE privileges`. 

### Using AUTHID to define invoker rights or definer right in procedure

Example1: Procedure create and run under privilege of definer rights, table in code also under privilege of definer:

```sql
-- Create by user HR (owner of table employees)
CREATE OR REPLACE PROCEDURE hr.pro_cnt_emp
    AUTHID definer
IS
    cnt_emp NUMBER;
BEGIN
    SELECT
        COUNT(*)
    INTO cnt_emp
    FROM
        hr.employees;

    dbms_output.put_line('total emplyoees is: ' || cnt_emp);
END;

GRANT EXECUTE ON hr.pro_cnt_emp TO oe;

-- Execute by user OE
EXECUTE hr.pro_cnt_emp;
-- Output: Total emplyoees is: 107
```

Example2: Procedure create and run under privilege of invoker right (current user who execute procedure), table in code is under privilege of definer and haven't granted privilege to invoker.


```sql
-- Create by user HR (owner of table employees)
CREATE OR REPLACE PROCEDURE hr.pro_cnt_emp
    AUTHID current_user
IS
    cnt_emp NUMBER;
BEGIN
    SELECT
        COUNT(*)
    INTO cnt_emp
    FROM
        hr.employees;

    dbms_output.put_line('total emplyoees is: ' || cnt_emp);
END;

GRANT EXECUTE ON hr.pro_cnt_emp TO oe;

-- Execute by user OE
EXECUTE hr.pro_cnt_emp;
-- Output: erorr table or view doesn't exsited (because procedure run under privilige of user OE but user OE doesn't has privilege select on table hr.employees)

```

### Role in PL/SQL procedure

- Rolse are `ENABLED` in sql command line or an anonymouse block when execute with invoker rights
- Roles are `DISABLED` when execute in pl/sql procedure with definer right.
- Even with the invoker right, roles must be enabled by default or in procedure itself. To get roles work completely, need to grant privileges on database objects directly to user who works with objects and procedures. 

Example: 
  - user A has role as DBA can select on table TAB1 in sql command line or anonymose block 
  - user B create a procedure P with definer right with code select on table TAB1, then grant execute on procedure P to user A.
  - user A cann't execute P because insuffic privileges on table TAB1. Then need to grant select on TAB1 directly to user A to be able to execute P(because privileges under role of user A are disabled with definer right in procedure)