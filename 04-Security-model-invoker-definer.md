# Security Model in PL/SQL

- [Security Model in PL/SQL](#security-model-in-plsql)
  - [1-Invoker Rights, Definer Rights](#1-invoker-rights-definer-rights)
    - [Using AUTHID to define invoker rights or definer right in procedure](#using-authid-to-define-invoker-rights-or-definer-right-in-procedure)
    - [Role in PL/SQL procedure](#role-in-plsql-procedure)
  - [2-PL/SQL Block and Scope](#2-plsql-block-and-scope)
    - [PL/SQL Block](#plsql-block)
    - [Nested Block](#nested-block)
    - [Labeled Block](#labeled-block)
    - [Definition of Scope](#definition-of-scope)

---

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

---
## 2-PL/SQL Block and Scope

- A PL/SQL block is a self-contained unit of code, typically between the keywords BEGIN and END, and can be anonymous or named (like in procedures and functions).
- Labels can be added to blocks to improve code clarity, help identify block boundaries, and qualify variables when the same name exists in nested or parent blocks.
- Nested blocks allow you to isolate program logic and handle errors locally without terminating the entire procedure.
- Exception handling within blocks lets you manage errors gracefully, such as trapping errors in nested blocks and continuing execution.
- Variable scope is important; variables in nested blocks can have the same name as those in outer blocks, and labels help specify which variable you mean. 
  
### PL/SQL Block

A PL/SQL block groups declarations and executable statements into one logical unit.
- DECLARE is optional.
- BEGIN ... END is required.
- EXCEPTION is optional.
- A block can contain other blocks.
- A block can optionally have a label.

```sql
[DECLARE
    -- Variables, constants, cursors, exceptions
]
BEGIN
    -- Executable statements
[EXCEPTION
    -- Exception handlers
]
END;
/
```

### Nested Block

A nested block is a PL/SQL block placed inside another PL/SQL block.

Scope Rules
- The inner block can access variables declared in the outer block.
- The outer block cannot access variables declared only in the inner block.
- An inner variable can hide an outer variable with the same name.
- An inner variable exists only while the inner block is executing.

```sql
SET SERVEROUTPUT ON;

DECLARE
    v_outer_message VARCHAR2(50) := 'Variable from outer block';
BEGIN
    DBMS_OUTPUT.PUT_LINE(v_outer_message);

    -- Nested block
    DECLARE
        v_inner_message VARCHAR2(50) := 'Variable from inner block';
    BEGIN
        -- The inner block can access both variables
        DBMS_OUTPUT.PUT_LINE(v_outer_message);
        DBMS_OUTPUT.PUT_LINE(v_inner_message);
    END;

    -- Valid because it belongs to the outer block
    DBMS_OUTPUT.PUT_LINE(v_outer_message);

    -- Invalid because the inner variable is out of scope
    -- DBMS_OUTPUT.PUT_LINE(v_inner_message);
END;
/
-- output
Variable from outer block
Variable from outer block
Variable from inner block
Variable from outer block

```

### Labeled Block

A labeled block is a PL/SQL block that has been given a name.
- A label uses double angle brackets: <<label_name>>.
- The label must appear before DECLARE or BEGIN.
- The block name after END is optional but improves readability.
- Labels help distinguish variables with the same name in nested blocks.
```sql
-- Label Syntax
<<block_name>>
DECLARE
BEGIN
    NULL;
END block_name;
/
```
Example:
```sql
SET SERVEROUTPUT ON;

<<outer_block>>
DECLARE
    v_count NUMBER := 10;
BEGIN
    DBMS_OUTPUT.PUT_LINE(
        'Outer before inner block: ' || v_count
    );

    <<inner_block>>
    DECLARE
        v_count NUMBER := 5;
    BEGIN
        -- Uses the nearest variable declaration
        DBMS_OUTPUT.PUT_LINE(
            'Inner count: ' || v_count
        );

        -- Accesses the outer block variable
        DBMS_OUTPUT.PUT_LINE(
            'Outer count: ' || outer_block.v_count
        );

        -- Accesses the inner block variable by its label
        DBMS_OUTPUT.PUT_LINE(
            'Qualified inner count: ' || inner_block.v_count
        );
    END inner_block;

    DBMS_OUTPUT.PUT_LINE(
        'Outer after inner block: ' || v_count
    );
END outer_block;
/

-- output
Outer before inner block: 10
Inner count: 5
Outer count: 10
Qualified inner count: 5
Outer after inner block: 10

```

### Definition of Scope

In PL/SQL, scope is the part of the program where a declared item—such as a variable, constant, cursor, or exception—can be referenced and used.

- An item declared in an outer block is accessible within that block and its nested blocks.
- An item declared in an inner block is accessible only inside that inner block.
- If both blocks declare the same variable name, the inner declaration hides the outer one within the inner block.
- A block label can be used to access the hidden outer variable.

Example:

```sql
<<outer_block>>
DECLARE
    v_count NUMBER := 10; -- Scope: outer and inner blocks
BEGIN
    DECLARE
        v_total NUMBER := 5; -- Scope: inner block only
    BEGIN
        DBMS_OUTPUT.PUT_LINE(v_count); -- Valid
        DBMS_OUTPUT.PUT_LINE(v_total); -- Valid
    END;

    DBMS_OUTPUT.PUT_LINE(v_count); -- Valid
    -- DBMS_OUTPUT.PUT_LINE(v_total); -- Invalid: out of scope
END outer_block;
/
```

**Example: Outer block handles the exception**

If an error occurs inside an inner PL/SQL block and that block does not handle the error in its own EXCEPTION section, Oracle passes the exception to the surrounding outer block.

```sql
SET SERVEROUTPUT ON;

BEGIN
    DBMS_OUTPUT.PUT_LINE('Outer block started');

    BEGIN
        DBMS_OUTPUT.PUT_LINE('Inner block started');

        -- Causes ZERO_DIVIDE
        DBMS_OUTPUT.PUT_LINE(10 / 0);

        -- This line is skipped
        DBMS_OUTPUT.PUT_LINE('Inner block completed');
    END;

    -- This line is also skipped
    DBMS_OUTPUT.PUT_LINE('Outer block completed');

EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE(
            'Outer block handled the inner block error'
        );
END;
/
```
`The process is:`

- ZERO_DIVIDE occurs in the inner block.
- The inner block has no EXCEPTION handler.
- Oracle terminates the inner block.
- The exception propagates to the outer block.
- The outer block’s ZERO_DIVIDE handler processes it.

**Example: Inner block handles the exception**

Inner block handles its own exception, the error does not propagate to the outer block. After the inner block finishes, the outer block continues normally.

```sql
SET SERVEROUTPUT ON;

BEGIN
    DBMS_OUTPUT.PUT_LINE('1. Outer block started');

    BEGIN
        DBMS_OUTPUT.PUT_LINE('2. Inner block started');

        -- Raises ZERO_DIVIDE
        DBMS_OUTPUT.PUT_LINE(10 / 0);

        -- Skipped because an exception occurred
        DBMS_OUTPUT.PUT_LINE('3. Inner calculation completed');

    EXCEPTION
        WHEN ZERO_DIVIDE THEN
            DBMS_OUTPUT.PUT_LINE(
                '3. Inner block handled ZERO_DIVIDE'
            );
    END;

    -- Continues because the exception was handled
    DBMS_OUTPUT.PUT_LINE('4. Outer block continues normally');

EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE(
            'Outer block handled: ' || SQLERRM
        );
END;
/
-- output
1. Outer block started
2. Inner block started
3. Inner block handled ZERO_DIVIDE
4. Outer block continues normally

```

`Benefits of Exception Handling in an Inner Block:`
Using an EXCEPTION section inside an inner block allows you to handle a local error without stopping the entire outer block.