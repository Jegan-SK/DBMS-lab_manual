# Experiment 7: PL/SQL – Variables, Control Structures and Loops

## AIM
To write and execute simple PL/SQL programs using variables, loops, and conditional statements.


## THEORY

PL/SQL, which stands for Procedural Language extensions to the Structured Query Language (SQL). It is a combination of SQL along with the procedural features of programming languages.

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

# PL/SQL Programs – Steps and Expected Output

## 1. Write a PL/SQL program to find the Greatest of Two Numbers

### Steps:
- Declare two numeric variables and initialize them.
- Use an `IF` statement to compare the values.
- Display the greater number using `DBMS_OUTPUT.PUT_LINE`.

**Expected Output:**  
Greater number is: 80


```
DECLARE
a NUMBER :=50;
b NUMBER :=80;
g NUMBER;


BEGIN
    IF a>b THEN g:=a;
    ELSE g:=b;
    END IF;
    DBMS_OUTPUT.PUT_LINE('Greatest Number is :'||g);
END;

```


<img width="716" height="215" alt="Screenshot 2026-11-17 140522" src="https://github.com/user-attachments/assets/78a46fb7-2fe1-4ff9-bdf0-992d3c1a4d85" />


---

## 2. Write a PL/SQL program to Calculate Sum of First N Natural Numbers

### Steps:
- Declare a variable `n` and assign a value (e.g., 10).
- Initialize a `sum` variable to 0.
- Use a `WHILE` loop to iterate from 1 to `n`, adding each number to the sum.
- Display the result using `DBMS_OUTPUT.PUT_LINE`.

**Expected Output:**  
Sum of first 10 natural numbers is: 55

```
DECLARE
n NUMBER :=10;
a NUMBER :=0;
s NUMBER :=0;


BEGIN
    WHILE a<=n LOOP
    s := s + a;
    a := a+1;
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Sum of first '||n|| ' natural numbers is: '||s);
END;

```


<img width="720" height="226" alt="Screenshot 2026-11-17 141404" src="https://github.com/user-attachments/assets/dd5dddc2-4e9e-4289-bad8-e5e4a03e7bd4" />

---

## 3. Write a PL/SQL program to generate Fibonacci series

### Steps:
- Declare the variable `n` to indicate how many terms to generate.
- Initialize the first two Fibonacci numbers (0 and 1).
- Use a loop to generate the next terms using the formula `c = a + b`.
- Print each term in the series.

**Expected Output:**  
n = 7  
Fibonacci sequence: 0, 1, 1, 2, 3, 5, 8

```
SET SERVEROUTPUT ON;


DECLARE
    n NUMBER := 7; -- Number of terms to generate
    a NUMBER := 0; -- First Fibonacci number
    b NUMBER := 1; -- Second Fibonacci number
    c NUMBER;      -- Variable to hold the next term
BEGIN
    DBMS_OUTPUT.PUT_LINE('n = ' || n);
    DBMS_OUTPUT.PUT('Fibonacci sequence: ');


    FOR i IN 1..n LOOP
        IF i = 1 THEN
            DBMS_OUTPUT.PUT(a);
        ELSE
            DBMS_OUTPUT.PUT(', ' || a);
        END IF;


        c := a + b;
        a := b;
        b := c;
    END LOOP;


    DBMS_OUTPUT.NEW_LINE;
END;

```

<img width="717" height="232" alt="Screenshot 2026-11-17 141727" src="https://github.com/user-attachments/assets/680c1d14-4c29-4b79-8a3d-ae459851731b" />

---

## 4. Write a PL/SQL Program to display the number in Reverse Order

### Steps:
- Declare a variable `n` and assign a value (e.g., 1535).
- Use a loop to extract each digit using modulo and reverse the number.
- Display the reversed number.

**Expected Output:**  
n = 1535  
Reversed number is 5351

```
DECLARE
    n NUMBER := 1535;
    rev NUMBER := 0;

    rem NUMBER;
BEGIN
    WHILE n > 0 LOOP
        rem := MOD(n, 10);
        rev := rev * 10 + rem;
        n := TRUNC(n / 10);
    END LOOP;


    DBMS_OUTPUT.PUT_LINE('Reversed number is ' || rev);
END;

```

<img width="713" height="202" alt="Screenshot 2026-11-17 143157" src="https://github.com/user-attachments/assets/3fd94877-dc2d-4abf-a21f-ace598c43694" />


---

## 5. Write a PL/SQL program to find the largest of three numbers

### Steps:
- Declare three numeric variables `a`, `b`, and `c`.
- Use nested `IF-ELSIF-ELSE` conditions to find the largest among the three.
- Display the largest number.

**Expected Output:**  
a = 10, b = 9, c = 15  
Largest of three number is 15

```
DECLARE
    a NUMBER := 10;
    b NUMBER := 9;
    c NUMBER := 15;
    largest NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('a = ' || a || ', b = ' || b || ', c = ' || c);


    IF a >= b THEN
        IF a >= c THEN
            largest := a;
        ELSE
            largest := c;
        END IF;
    ELSIF b >= c THEN
        largest := b;
    ELSE
        largest := c;
    END IF;


    DBMS_OUTPUT.PUT_LINE('Largest of three number is ' || largest);
END;

```

<img width="715" height="225" alt="Screenshot 2026-11-17 143633" src="https://github.com/user-attachments/assets/f76f5442-c12f-498c-9bf4-970a3ce35877" />


## RESULT
Thus, the PL/SQL programs using variables, conditionals, and loops were executed successfully.
