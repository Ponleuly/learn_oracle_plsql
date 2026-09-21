# **EXCEPTION - Error Handling**
- [**EXCEPTION - Error Handling**](#exception---error-handling)
  - [1-Predefined System Exceptions](#1-predefined-system-exceptions)
  - [2-Non-Predefined System Exceptions](#2-non-predefined-system-exceptions)
  - [3-User-Defined Exceptions](#3-user-defined-exceptions)
  - [3-Main built-in error-reporting functions](#3-main-built-in-error-reporting-functions)
    - [Using SQLCODE, SQLERRM](#using-sqlcode-sqlerrm)
    - [Using RAISE\_APPLICATION\_ERROR VS RAISE](#using-raise_application_error-vs-raise)



`Error handling` allows a PL/SQL program to detect an error and respond to it instead of ending unexpectedly.

The exception section is written after the executable section:

```sql
BEGIN
    -- Executable statements

EXCEPTION
    WHEN exception_name THEN
        -- Error-handling statements
    WHEN others THEN
        -- Error-handling statements
END;
/
```

## 1-Predefined System Exceptions 

Oracle provides predefined names for common error conditions. When these occur, PL/SQL raises them automatically.

- `NO_DATA_FOUND` – A SELECT INTO statement returns no rows.
- `TOO_MANY_ROWS` – A SELECT INTO statement returns more than one row.
- `ZERO_DIVIDE` – An attempt is made to divide a number by zero.
- `DUP_VAL_ON_INDEX` – An attempt is made to store duplicate values in a column with a UNIQUE index.
- `VALUE_ERROR` – An arithmetic, conversion, truncation, or size-constraint error occurs.

`Important`: After handling the exception, Oracle does not return to the statement that failed.

```sql
DECLARE
    v_emp_first_name employees.first_name%TYPE;
BEGIN
    -- This query may return no row
    SELECT
        first_name
    INTO v_emp_first_name
    FROM
        employees
    WHERE
        employee_id = 9999;

    dbms_output.put_line('employee frist name is: ' || v_emp_first_name);
EXCEPTION
    -- Handles the error when the query returns no row
    WHEN no_data_found THEN
        dbms_output.put_line('Employee no found!');
    -- Handles any other unexpected error
    WHEN OTHERS THEN
        dbms_output.put_line('error: ' || sqlerrm);
END;
/
-- output
Employee no found!
```

## 2-Non-Predefined System Exceptions

For standard Oracle errors that do not have a predefined name, you can map an `Oracle error number (ORA-XXXXX)` to a custom name using `PRAGMA EXCEPTION_INIT`.

```sql
DECLARE
   e_fk_violation EXCEPTION;
   PRAGMA EXCEPTION_INIT(e_fk_violation, -2292); -- ORA-02292: integrity constraint violated - child record found
BEGIN
   DELETE FROM dept WHERE department_id = 50;
EXCEPTION
   WHEN e_fk_violation THEN
      DBMS_OUTPUT.PUT_LINE('Cannot delete department because dependent employee records exist.');
END;
/
-- output
Cannot delete department because dependent employee records exist.

```

## 3-User-Defined Exceptions

A user-defined exception is an exception created by programme to handle business logic failures and raise them explicitly using the `RAISE` statement or `RAISE_APPLICATION_ERROR`.

```sql
DECLARE
    v_salary NUMBER := -500;

    -- Declare a custom exception
    e_invalid_salary EXCEPTION;
BEGIN
    -- Check the business condition
    IF v_salary < 0 THEN
        -- Manually raise the custom exception
        RAISE e_invalid_salary;
    END IF;

    DBMS_OUTPUT.PUT_LINE(
        'Salary: ' || v_salary
    );

EXCEPTION
    -- Handle the custom exception
    WHEN e_invalid_salary THEN
        DBMS_OUTPUT.PUT_LINE(
            'Error: Salary cannot be negative.'
        );
END;
/
-- output:
Error: Salary cannot be negative.
```

## 3-Main built-in error-reporting functions

- `SQLCODE`: Returns the numeric code of the most recent error.
- `SQLERRM`: Returns the error message string associated with the error code.
- `RAISE_APPLICATION_ERROR(error_number, message)`: Raises a user-defined error code (between -20000 and -20999) and custom message back to the calling application or client environment.

### Using SQLCODE, SQLERRM

```sql
DECLARE
    v_result NUMBER;
BEGIN
    -- Causes a division-by-zero error
    v_result := 10 / 0;

EXCEPTION
    WHEN OTHERS THEN
        -- Display the numeric Oracle error code
        DBMS_OUTPUT.PUT_LINE(
            'Error code: ' || SQLCODE
        );

        -- Display the Oracle error message
        DBMS_OUTPUT.PUT_LINE(
            'Error message: ' || SQLERRM
        );
END;
/
-- output:
Error code: -1476
Error message: ORA-01476: divisor is equal to zero

```

### Using RAISE_APPLICATION_ERROR VS RAISE

- Use `RAISE_APPLICATION_ERROR` to raise an error with own number and message directly to the caller.
 
```sql
DECLARE
    v_salary NUMBER := -500;
BEGIN
    -- Check the business rule
    IF v_salary < 0 THEN
        -- Raise a custom Oracle error and stop the block
        RAISE_APPLICATION_ERROR(
            -20001,
            'Salary cannot be negative.'
        );
    END IF;

    -- This statement is skipped when the error is raised
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_salary);
END;
/
-- output:
ORA-20001: Salary cannot be negative.
ORA-06512: at line 7

```

- Use `RAISE` to explicitly raise an exception declared in PL/SQL. Because the exception is handled inside the block, the client sees only the DBMS_OUTPUT message. If the user-defined exception is not handled, the client normally receives:

```sql
DECLARE
    e_invalid_salary EXCEPTION;
    v_salary NUMBER := -500;
BEGIN
    IF v_salary < 0 THEN
        -- Raise the exception by its name
        RAISE e_invalid_salary;
    END IF;
-- If the user-defined exception is handled
EXCEPTION
    WHEN e_invalid_salary THEN
        DBMS_OUTPUT.PUT_LINE(
            'Salary cannot be negative.'
        );
END;
/
-- output:
Salary cannot be negative.


DECLARE
    e_invalid_salary EXCEPTION;
    v_salary NUMBER := -500;
BEGIN
    IF v_salary < 0 THEN
        -- Raise the exception by its name
        RAISE e_invalid_salary;
    END IF;
-- If the user-defined exception is not handled
/*
EXCEPTION
    WHEN e_invalid_salary THEN
        DBMS_OUTPUT.PUT_LINE(
            'Salary cannot be negative.'
        );
*/
END;
/
-- output:
ORA-06510: PL/SQL: unhandled user-defined exception
ORA-06512: at line 7
```