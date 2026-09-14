# Conditional Statements
- [Conditional Statements](#conditional-statements)
	- [**1-FOR LOOP**](#1-for-loop)
		- [**What is For Loop?**](#what-is-for-loop)
		- [**BASIC For Loop**](#basic-for-loop)
		- [**REVERSE with For Loop**](#reverse-with-for-loop)
		- [**CONTINUE with For Loop**](#continue-with-for-loop)
		- [**EXIT with For Loop**](#exit-with-for-loop)
		- [**GOTO with For Loop**](#goto-with-for-loop)
		- [**RETURN with For Loop**](#return-with-for-loop)
		- [**CURSOR with For Loop**](#cursor-with-for-loop)


## **1-FOR LOOP**

### **What is For Loop?**
For loops in PL/SQL iterate through a sequence of integers or through rows of a query result set, making them essential for procedural programming. 

- The loop index can iterate in ascending or descending order using the REVERSE keyword.
- Control flow statements like CONTINUE, EXIT, GOTO, and RETURN allow to manage loop execution.
- Using for loop with CURSOR to process each row from the query result in cursor.

**Syntax:**
```sql
FOR index IN [REVERSE] lower_bound..upper_bound LOOP
   -- statements to execute
END LOOP;
```
### **BASIC For Loop**
```sql
DECLARE 
	start_val NUMBER := 1;
	end_val  NUMBER := 5;
	cal_sqrt DECIMAL(3,2);

BEGIN
	FOR i IN start_val..end_val LOOP
		cal_sqrt := sqrt(i);
		dbms_output.put_line('Square root of ' || i || ' is: ' || cal_sqrt);
		
	end LOOP;
	
END;

-- ouput
Square root of 1 is: 1
Square root of 2 is: 1.41
Square root of 3 is: 1.73
Square root of 4 is: 2
Square root of 5 is: 2.24

```

### **REVERSE with For Loop** 
Using reverse to start index loop from upper bond value.

```sql
DECLARE 
	start_val NUMBER := 1;
	end_val  NUMBER := 5;
	cal_sqrt DECIMAL(3,2);

BEGIN
	FOR i IN REVERSE start_val..end_val LOOP
		cal_sqrt := sqrt(i);
		dbms_output.put_line('Square root of ' || i || ' is: ' || cal_sqrt);
		
	end LOOP;
	
END;

-- output
Square root of 5 is: 2.24
Square root of 4 is: 2
Square root of 3 is: 1.73
Square root of 2 is: 1.41
Square root of 1 is: 1
```

### **CONTINUE with For Loop**
Using continue with for loop to skip the rest of loop and process the next index value.

```sql
DECLARE 
	start_val NUMBER := 1;
	end_val  NUMBER := 5;
	cal_sqrt DECIMAL(3,2);

BEGIN
	FOR i IN start_val..end_val LOOP
		cal_sqrt := sqrt(i);
		IF cal_sqrt = 2 THEN
			CONTINUE;
		END IF;

		dbms_output.put_line('Square root of ' || i || ' is: ' || cal_sqrt);
		
	end LOOP;
		
	dbms_output.put_line('-----------');
END;

-- output
Square root of 1 is: 1
Square root of 2 is: 1.41
Square root of 3 is: 1.73
Square root of 5 is: 2.24
-----------
```

### **EXIT with For Loop**

Using exit in for loop to exit loop before interating through all index values.

```sql
DECLARE 
	start_val NUMBER := 1;
	end_val  NUMBER := 5;
	cal_sqrt DECIMAL(3,2);

BEGIN
	FOR i IN start_val..end_val LOOP
		cal_sqrt := sqrt(i);
		IF cal_sqrt = 2 THEN
			EXIT;
		END IF;

		dbms_output.put_line('Square root of ' || i || ' is: ' || cal_sqrt);
		
	end LOOP;
		
	dbms_output.put_line('-----------');
END;

-- ouput
Square root of 1 is: 1
Square root of 2 is: 1.41
Square root of 3 is: 1.73
-----------
```

### **GOTO with For Loop**

Using Goto in for loop to skip to a label outside of the loop but in the same block.

```sql
DECLARE 
	start_val NUMBER := 1;
	end_val  NUMBER := 5;
	cal_sqrt DECIMAL(3,2);

BEGIN
	FOR i IN start_val..end_val LOOP
		cal_sqrt := sqrt(i);
		IF cal_sqrt = 2 THEN
			GOTO skip_end;
		END IF;

		dbms_output.put_line('Square root of ' || i || ' is: ' || cal_sqrt);
		
	end LOOP;
	<<skip_end>> null;
	dbms_output.put_line('Done looping');
END;

--output
Square root of 1 is: 1
Square root of 2 is: 1.41
Square root of 3 is: 1.73
Done looping
-----------
```

### **RETURN with For Loop**

Using Return in for loop to exit loop and enclosing block.

```sql
DECLARE 
	start_val NUMBER := 1;
	end_val  NUMBER := 5;
	cal_sqrt DECIMAL(3,2);

BEGIN
	FOR i IN start_val..end_val LOOP
		cal_sqrt := sqrt(i);
		IF cal_sqrt = 2 THEN
			RETURN;
		END IF;

		dbms_output.put_line('Square root of ' || i || ' is: ' || cal_sqrt);
		
	end LOOP;
	dbms_output.put_line('Done looping');
END;


-- output: REUTRN exit immediately the block, and don't run code outside the loop, as in example, didn't output the line "Done looping"
Square root of 1 is: 1
Square root of 2 is: 1.41
Square root of 3 is: 1.73

```

### **CURSOR with For Loop**

For loop process through resulf set of a query in cursor.

```sql
DECLARE 
	emp_firstname employees.first_name%TYPE;
	emp_dept employees.department_id%TYPE; 

BEGIN
	FOR name IN (SELECT first_name, department_id FROM employees) LOOP	
		
		emp_dept := name.department_id;
		emp_firstname := name.first_name;
		
		IF emp_firstname = 'Adam' THEN
		
			dbms_output.put_line('Exmployees firstname is : ' || emp_firstname || ' , Dept id: ' || emp_dept);
		
		END IF;
		
	END LOOP;
	
END;

-- output
Exmployees firstname is : Adam , Dept id: 50
```

---

