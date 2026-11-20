# Experiment 8: PL/SQL Cursor Programs

## AIM
To write and execute PL/SQL programs using cursors and exception handling to manage runtime errors effectively and display appropriate messages.

## THEORY

In PL/SQL, cursors are used to handle query result sets row-by-row. 

There are two types of cursors:

- Implicit Cursors: Automatically created by PL/SQL for single-row queries.
- Explicit Cursors: Declared and controlled by the programmer for multi-row queries.

Types of Explicit Cursors:

1. Simple Cursor: Basic cursor to iterate over multiple rows.

2. Parameterized Cursor: Accepts parameters to filter the result dynamically.

3. Cursor FOR Loop: Simplifies cursor operations (open, fetch, close).

4. %ROWTYPE Cursor: Fetches entire row into a record using %ROWTYPE.

5. Cursor with FOR UPDATE: Used for row-level locking and updating the rows while looping.

**Syntax:**
```sql
DECLARE 
   <declarations section> 
BEGIN 
   <executable command(s)>
EXCEPTION 
   <exception handling> 
END;
```

### Basic Components of PL/SQL Block:

- DECLARE: Section to declare variables and constants.
- BEGIN: The execution section that contains PL/SQL statements.
- EXCEPTION: Handles errors or exceptions that occur in the program.
- END: Marks the end of the PL/SQL block.

**Exception Handling**

PL/SQL provides a robust mechanism to handle runtime errors using exception handling blocks. When an error occurs during execution, control is passed to the EXCEPTION section, where specific or general errors can be handled gracefully.

### Components of Exception Handling:
- Predefined Exceptions: Automatically raised by PL/SQL for common errors (e.g., NO_DATA_FOUND, TOO_MANY_ROWS, ZERO_DIVIDE).
- User-defined Exceptions: Declared explicitly in the declaration section using the EXCEPTION keyword.
- WHEN OTHERS: A generic handler for all exceptions not handled explicitly.

```sql
BEGIN
   -- Statements
EXCEPTION
   WHEN exception_name THEN
      -- Handling code
   WHEN OTHERS THEN
      -- Handling for unknown errors
END;
```

### **Question 1: Simple Cursor with Exception Handling**

**Write a PL/SQL program using a simple cursor to fetch employee names and designations from the `employees` table. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: When no rows are fetched.
2. **OTHERS**: Any other unexpected errors during execution.

**Steps:**

- Create an `employees` table with fields `emp_id`, `emp_name`, and `designation`.
- Insert some sample data into the table.
- Use a simple cursor to fetch and display employee names and designations.
- Implement exception handling to catch the relevant exceptions and display appropriate messages.

**Code:**
```
SET SERVEROUTPUT ON;

DECLARE
    CURSOR emp_cur IS
        SELECT emp_name, designation
        FROM employees8
        ORDER BY emp_id;
    v_name   employees8.emp_name%TYPE;
    v_desig  employees8.designation%TYPE;
BEGIN
    OPEN emp_cur;
    FETCH emp_cur INTO v_name, v_desig;

    -- No row in the first fetch means the table is empty
    IF emp_cur%NOTFOUND THEN
        RAISE NO_DATA_FOUND;
    END IF;

    DBMS_OUTPUT.PUT_LINE(RPAD('EMP_NAME', 16) || 'DESIGNATION');
    DBMS_OUTPUT.PUT_LINE('---------------------------');
    WHILE emp_cur%FOUND LOOP
        DBMS_OUTPUT.PUT_LINE(RPAD(v_name, 16) || v_desig);
        FETCH emp_cur INTO v_name, v_desig;
    END LOOP;
    CLOSE emp_cur;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employee records found.');
        IF emp_cur%ISOPEN THEN
            CLOSE emp_cur;
        END IF;
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
        IF emp_cur%ISOPEN THEN
            CLOSE emp_cur;
        END IF;
END;
```

**Output:**  

<img width="707" height="318" alt="image" src="https://github.com/user-attachments/assets/f67b967a-4bab-4f07-aa7e-f7077c6176ff" />

---

### **Question 2: Parameterized Cursor with Exception Handling**

**Write a PL/SQL program using a parameterized cursor to retrieve and display employees with a salary in a given range. Implement exception handling for the following errors:**

1. **NO_DATA_FOUND**: When no employees meet the salary criteria.
2. **OTHERS**: For any unexpected errors during the execution.

**Steps:**

- Modify the `employees` table by adding a `salary` column.
- Insert sample salary values for the employees.
- Use a parameterized cursor to accept a salary range as input and fetch employees within that range.
- Implement exception handling to catch and display relevant error messages.

**Code:**
```
SET SERVEROUTPUT ON;

DECLARE
    CURSOR sal_cur (p_min NUMBER, p_max NUMBER) IS
        SELECT emp_name, designation, salary
        FROM employees8
        WHERE salary BETWEEN p_min AND p_max
        ORDER BY emp_id;
    v_name   employees8.emp_name%TYPE;
    v_desig  employees8.designation%TYPE;
    v_sal    employees8.salary%TYPE;
    v_min    NUMBER := 40000;   -- lower limit of the salary range
    v_max    NUMBER := 50000;   -- upper limit of the salary range
BEGIN
    DBMS_OUTPUT.PUT_LINE('Salary range: ' || v_min || ' to ' || v_max);
    OPEN sal_cur(v_min, v_max);
    FETCH sal_cur INTO v_name, v_desig, v_sal;

    IF sal_cur%NOTFOUND THEN
        RAISE NO_DATA_FOUND;
    END IF;

    DBMS_OUTPUT.PUT_LINE(RPAD('EMP_NAME', 16) || RPAD('DESIGNATION', 14)
                         || 'SALARY');
    DBMS_OUTPUT.PUT_LINE('------------------------------------');
    WHILE sal_cur%FOUND LOOP
        DBMS_OUTPUT.PUT_LINE(RPAD(v_name, 16) || RPAD(v_desig, 14)
                             || v_sal);
        FETCH sal_cur INTO v_name, v_desig, v_sal;
    END LOOP;
    CLOSE sal_cur;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employees found in this salary range.');
        IF sal_cur%ISOPEN THEN
            CLOSE sal_cur;
        END IF;
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
        IF sal_cur%ISOPEN THEN
            CLOSE sal_cur;
        END IF;
END;
```

**Output:**  

<img width="707" height="305" alt="image" src="https://github.com/user-attachments/assets/27e21795-5ab6-4848-9268-7dea3d7ad5a1" />

---

### **Question 3: Cursor FOR Loop with Exception Handling**

**Write a PL/SQL program using a cursor FOR loop to retrieve and display all employee names and their department numbers from the `employees` table. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: If no employees are found in the database.
2. **OTHERS**: For any other unexpected errors.

**Steps:**

- Modify the `employees` table by adding a `dept_no` column.
- Insert sample department numbers for employees.
- Use a cursor FOR loop to fetch and display employee names along with their department numbers.
- Implement exception handling to catch the relevant exceptions.

**Code:**
```
SET SERVEROUTPUT ON;

DECLARE
    CURSOR emp_cur IS
        SELECT emp_name, dept_no
        FROM employees8
        ORDER BY emp_id;
    v_count  NUMBER := 0;
BEGIN
    DBMS_OUTPUT.PUT_LINE(RPAD('EMP_NAME', 16) || 'DEPT_NO');
    DBMS_OUTPUT.PUT_LINE('------------------------');

    -- The cursor FOR loop opens, fetches and closes the cursor itself
    FOR rec IN emp_cur LOOP
        DBMS_OUTPUT.PUT_LINE(RPAD(rec.emp_name, 16) || rec.dept_no);
        v_count := v_count + 1;
    END LOOP;

    -- The loop body never runs when the table is empty
    IF v_count = 0 THEN
        RAISE NO_DATA_FOUND;
    END IF;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employees found in the database.');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
END;
```

**Output:**  

<img width="712" height="331" alt="image" src="https://github.com/user-attachments/assets/7bae5c2c-fad9-424d-ac75-30d8f400550f" />

---

### **Question 4: Cursor with `%ROWTYPE` and Exception Handling**

**Write a PL/SQL program that uses a cursor with `%ROWTYPE` to fetch and display complete employee records (emp_id, emp_name, designation, salary). Implement exception handling for the following errors:**

1. **NO_DATA_FOUND**: When no employees are found in the database.
2. **OTHERS**: For any other errors that occur.

**Steps:**

- Modify the `employees` table by adding `emp_id`, `emp_name`, `designation`, and `salary` fields.
- Insert sample data into the `employees` table.
- Declare a cursor using `%ROWTYPE` to fetch complete rows from the `employees` table.
- Implement exception handling to catch the relevant exceptions and display appropriate messages.

**Code:**
```
SET SERVEROUTPUT ON;

DECLARE
    CURSOR emp_cur IS
        SELECT * FROM employees8 ORDER BY emp_id;
    v_emp  emp_cur%ROWTYPE;
BEGIN
    OPEN emp_cur;
    FETCH emp_cur INTO v_emp;

    IF emp_cur%NOTFOUND THEN
        RAISE NO_DATA_FOUND;
    END IF;

    DBMS_OUTPUT.PUT_LINE(RPAD('EMP_ID', 8) || RPAD('EMP_NAME', 16) ||
                         RPAD('DESIGNATION', 14) || 'SALARY');
    DBMS_OUTPUT.PUT_LINE('--------------------------------------------');
    WHILE emp_cur%FOUND LOOP
        DBMS_OUTPUT.PUT_LINE(RPAD(v_emp.emp_id, 8) ||
                             RPAD(v_emp.emp_name, 16) ||
                             RPAD(v_emp.designation, 14) ||
                             v_emp.salary);
        FETCH emp_cur INTO v_emp;
    END LOOP;
    CLOSE emp_cur;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employee records found.');
        IF emp_cur%ISOPEN THEN
            CLOSE emp_cur;
        END IF;
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
        IF emp_cur%ISOPEN THEN
            CLOSE emp_cur;
        END IF;
END;
```

**Output:**  
<img width="715" height="361" alt="image" src="https://github.com/user-attachments/assets/97525a95-a7fb-45fc-8987-4eed1b2bda3d" />

---

### **Question 5: Cursor with FOR UPDATE Clause and Exception Handling**

**Write a PL/SQL program using a cursor with the `FOR UPDATE` clause to update the salary of employees in a specific department. Implement exception handling for the following cases:**

1. **NO_DATA_FOUND**: If no rows are affected by the update.
2. **OTHERS**: For any unexpected errors during execution.

**Steps:**

- Modify the `employees` table to include a `dept_no` and `salary` field.
- Insert sample data into the `employees` table with different department numbers.
- Use a cursor with the `FOR UPDATE` clause to lock the rows of employees in a specific department and update their salary.
- Implement exception handling to handle `NO_DATA_FOUND` or other errors that may occur.

**Code:**
```
SET SERVEROUTPUT ON;

DECLARE
    CURSOR emp_cur (p_dept NUMBER) IS
        SELECT emp_id, emp_name, salary
        FROM employees8
        WHERE dept_no = p_dept
        ORDER BY emp_id
        FOR UPDATE;
    v_dept    NUMBER := 20;     -- department whose salary is increased
    v_new_sal NUMBER;
    v_count   NUMBER := 0;
BEGIN
    FOR rec IN emp_cur(v_dept) LOOP
        v_new_sal := rec.salary + (rec.salary * 0.10);   -- 10% increment

        UPDATE employees8
        SET salary = v_new_sal
        WHERE CURRENT OF emp_cur;

        v_count := v_count + 1;
        DBMS_OUTPUT.PUT_LINE(rec.emp_name || ': ' || rec.salary ||
                             ' -> ' || v_new_sal);
    END LOOP;

    IF v_count = 0 THEN
        RAISE NO_DATA_FOUND;
    END IF;

    COMMIT;
    DBMS_OUTPUT.PUT_LINE(v_count || ' employee(s) updated in department '
                         || v_dept);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employees found in department ' || v_dept);
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('Error occurred: ' || SQLERRM);
END;
```

**Output:**  
<img width="706" height="267" alt="image" src="https://github.com/user-attachments/assets/0b7a853e-38bb-4f81-99e9-b80ce96c7083" />

---

## RESULT
Thus, the program successfully executed and displayed employee details using a cursor. 

