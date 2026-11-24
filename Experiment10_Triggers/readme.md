# Experiment 10: PL/SQL – Triggers

## AIM
To write and execute PL/SQL trigger programs for automating actions in response to specific table events like INSERT, UPDATE, or DELETE.

---

## THEORY

A **trigger** is a stored PL/SQL block that is automatically executed or fired when a specified event occurs on a table or view. Triggers can be used for enforcing business rules, auditing changes, or automatic updates.

### Types of Triggers:
- **Before Trigger**: Executes before the operation (INSERT, UPDATE, DELETE).
- **After Trigger**: Executes after the operation.
- **Row-level Trigger**: Executes for each affected row.
- **Statement-level Trigger**: Executes once for the triggering statement.

**Basic Syntax:**
```sql
CREATE OR REPLACE TRIGGER trigger_name
BEFORE|AFTER INSERT|UPDATE|DELETE ON table_name
[FOR EACH ROW]
BEGIN
   -- trigger logic
END;
```

## 1. Write a trigger to log every insertion into a table.
**Steps:**
- Create two tables: `employees` (for storing data) and `employee_log` (for logging the inserts).
- Write an **AFTER INSERT** trigger on the `employees` table to log the new data into the `employee_log` table.

**Expected Output:**
- A new entry is added to the `employee_log` table each time a new record is inserted into the `employees` table.

**Table Setup:**
```
CREATE TABLE employees10 (
    emp_id       NUMBER(5) PRIMARY KEY,
    emp_name     VARCHAR2(50),
    designation  VARCHAR2(50),
    salary       NUMBER(10,2)
);

CREATE TABLE employee_log10 (
    emp_id    NUMBER(5),
    emp_name  VARCHAR2(50),
    action    VARCHAR2(20),
    log_time  TIMESTAMP DEFAULT SYSTIMESTAMP
);
```
**Code:**
```
CREATE OR REPLACE TRIGGER trg_employee_insert_log
AFTER INSERT ON employees10
FOR EACH ROW
BEGIN
    INSERT INTO employee_log10 (emp_id, emp_name, action)
    VALUES (:NEW.emp_id, :NEW.emp_name, 'INSERTED');
END;
```
**Testing the trigger:**
```
INSERT INTO employees10 VALUES (101, 'Arun Kumar', 'Manager', 65000);
INSERT INTO employees10 VALUES (102, 'Priya Sharma', 'Developer', 48000);
COMMIT;

SELECT * FROM employee_log10;
```
**Output:**

<img width="753" height="179" alt="image" src="https://github.com/user-attachments/assets/f8a305ea-8257-4a33-85c6-9f727600087d" />


---

## 2. Write a trigger to prevent deletion of records from a sensitive table.
**Steps:**
- Write a **BEFORE DELETE** trigger on the `sensitive_data` table.
- Use `RAISE_APPLICATION_ERROR` to prevent deletion and issue a custom error message.

**Expected Output:**
- If an attempt is made to delete a record from `sensitive_data`, an error message is raised, e.g., `ERROR: Deletion not allowed on this table.`

**Table Setup:**
```
CREATE TABLE sensitive_data10 (
    data_id     NUMBER(5) PRIMARY KEY,
    data_value  VARCHAR2(50)
);

INSERT INTO sensitive_data10 VALUES (1, 'Account Number 1001');
INSERT INTO sensitive_data10 VALUES (2, 'Account Number 1002');
COMMIT;
```
**Code:**
```
CREATE OR REPLACE TRIGGER trg_prevent_delete
BEFORE DELETE ON sensitive_data10
BEGIN
    RAISE_APPLICATION_ERROR(-20001,
        'ERROR: Deletion not allowed on this table.');
END;
```
**Testing the trigger:**
```
DELETE FROM sensitive_data10 WHERE data_id = 1;

SELECT * FROM sensitive_data10;
```
**Output:**

<img width="752" height="201" alt="image" src="https://github.com/user-attachments/assets/f68d4787-c788-4dd4-b6c1-8ac477a91755" />

---

## 3. Write a trigger to automatically update a `last_modified` timestamp.
**Steps:**
- Add a `last_modified` column to the `products` table.
- Write a **BEFORE UPDATE** trigger on the `products` table to set the `last_modified` column to the current timestamp whenever an update occurs.

**Expected Output:**
- The `last_modified` column in the `products` table is updated automatically to the current date and time when any record is updated.

**Table Setup:**
```
CREATE TABLE products10 (
    product_id    NUMBER(5) PRIMARY KEY,
    product_name  VARCHAR2(50),
    price         NUMBER(10,2)
);

INSERT INTO products10 VALUES (1, 'Laptop', 50000);
INSERT INTO products10 VALUES (2, 'Mouse', 500);
COMMIT;

ALTER TABLE products10 ADD last_modified TIMESTAMP;
```
**Code:**
```
CREATE OR REPLACE TRIGGER trg_update_timestamp
BEFORE UPDATE ON products10
FOR EACH ROW
BEGIN
    :NEW.last_modified := SYSTIMESTAMP;
END;
```
**Testing the trigger:**
```
UPDATE products10 SET price = 55000 WHERE product_id = 1;
COMMIT;

SELECT * FROM products10;
```
**Output:**

<img width="752" height="223" alt="image" src="https://github.com/user-attachments/assets/c85e8dfc-cfde-45ee-b428-0f8b188a44f2" />


---

## 4. Write a trigger to keep track of the number of updates made to a table.
**Steps:**
- Create an `audit_log` table with a counter column.
- Write an **AFTER UPDATE** trigger on the `customer_orders` table to increment the counter in the `audit_log` table every time a record is updated.

**Expected Output:**
- The `audit_log` table will maintain a count of how many updates have been made to the `customer_orders` table.

**Table Setup:**
```
CREATE TABLE customer_orders10 (
    order_id       NUMBER(5) PRIMARY KEY,
    customer_name  VARCHAR2(50),
    amount         NUMBER(10,2)
);

INSERT INTO customer_orders10 VALUES (1, 'Arun Kumar', 2000);
INSERT INTO customer_orders10 VALUES (2, 'Priya Sharma', 1500);
INSERT INTO customer_orders10 VALUES (3, 'Rahul Verma', 3200);

CREATE TABLE audit_log10 (
    table_name    VARCHAR2(30),
    update_count  NUMBER
);

INSERT INTO audit_log10 VALUES ('CUSTOMER_ORDERS10', 0);
COMMIT;
```
**Code:**
```
CREATE OR REPLACE TRIGGER trg_count_updates
AFTER UPDATE ON customer_orders10
FOR EACH ROW
BEGIN
    UPDATE audit_log10
    SET update_count = update_count + 1
    WHERE table_name = 'CUSTOMER_ORDERS10';
END;
```
**Testing the trigger:**
```
UPDATE customer_orders10 SET amount = 2500 WHERE order_id = 1;
UPDATE customer_orders10 SET amount = 1800 WHERE order_id = 2;
COMMIT;

SELECT * FROM audit_log10;
```
**Output:**

<img width="752" height="164" alt="image" src="https://github.com/user-attachments/assets/d413d80e-5003-4ea1-8460-4f675ba93265" />


---

## 5. Write a trigger that checks a condition before allowing insertion into a table.
**Steps:**
- Write a **BEFORE INSERT** trigger on the `employees` table to check if the inserted salary meets a specific condition (e.g., salary must be greater than 3000).
- If the condition is not met, raise an error to prevent the insert.

**Expected Output:**
- If the inserted salary in the `employees` table is below the condition (e.g., salary < 3000), the insert operation is blocked, and an error message is raised, such as: `ERROR: Salary below minimum threshold.`

**Code:**
```
CREATE OR REPLACE TRIGGER trg_check_salary
BEFORE INSERT ON employees10
FOR EACH ROW
BEGIN
    -- a salary below 3000 is not allowed
    IF :NEW.salary IS NULL OR :NEW.salary < 3000 THEN
        RAISE_APPLICATION_ERROR(-20002,
            'ERROR: Salary below minimum threshold.');
    END IF;
END;
```
**Testing the trigger:**
```
-- valid salary: the insert succeeds
INSERT INTO employees10 VALUES (103, 'Rahul Verma', 'Analyst', 40000);

-- salary below 3000: the insert is blocked
INSERT INTO employees10 VALUES (104, 'Sneha Reddy', 'Tester', 2500);

SELECT * FROM employees10;
```
**Output:**

<img width="752" height="249" alt="image" src="https://github.com/user-attachments/assets/1c8ac376-0656-416e-8e88-26dc2eb56bae" />


## RESULT
Thus, the PL/SQL trigger programs were written and executed successfully.
