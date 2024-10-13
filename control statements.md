In PL/SQL, control statements allow you to direct the flow of execution based on conditions, loops, and branching. These include conditional statements, iterative loops, and error-handling constructs.

Let’s look at control statements from **basic** to **advanced**, ensuring they are consistent with the previous examples.

---

### **1. Conditional Statements (IF-THEN-ELSE)**
Conditional statements control the flow based on true or false conditions.

#### **Basic Example: Simple IF-THEN**
```plsql
DECLARE
    v_salary NUMBER := 4500;
BEGIN
    IF v_salary > 4000 THEN
        DBMS_OUTPUT.PUT_LINE('Salary is greater than 4000');
    END IF;
END;
```
- If `v_salary` is greater than 4000, the message will be printed.

#### **Intermediate Example: IF-THEN-ELSE**
```plsql
DECLARE
    v_salary NUMBER := 3500;
BEGIN
    IF v_salary > 4000 THEN
        DBMS_OUTPUT.PUT_LINE('Salary is above 4000');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Salary is below or equal to 4000');
    END IF;
END;
```
- The ELSE branch executes when the condition is false.

#### **Advanced Example: IF-THEN-ELSIF-ELSE**
```plsql
DECLARE
    v_salary NUMBER := 5000;
BEGIN
    IF v_salary > 5000 THEN
        DBMS_OUTPUT.PUT_LINE('Salary is above 5000');
    ELSIF v_salary = 5000 THEN
        DBMS_OUTPUT.PUT_LINE('Salary is exactly 5000');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Salary is below 5000');
    END IF;
END;
```
- The `ELSIF` block allows for multiple conditional checks.

---

### **2. CASE Statements**
CASE is a more flexible form of decision-making. It allows selecting among multiple values.

#### **Basic Example: Simple CASE**
```plsql
DECLARE
    v_grade CHAR(1) := 'B';
BEGIN
    CASE v_grade
        WHEN 'A' THEN DBMS_OUTPUT.PUT_LINE('Excellent');
        WHEN 'B' THEN DBMS_OUTPUT.PUT_LINE('Good');
        WHEN 'C' THEN DBMS_OUTPUT.PUT_LINE('Average');
        ELSE DBMS_OUTPUT.PUT_LINE('Poor');
    END CASE;
END;
```
- Each `WHEN` clause matches a specific value.

#### **Advanced Example: Searched CASE**
```plsql
DECLARE
    v_score NUMBER := 85;
BEGIN
    CASE
        WHEN v_score >= 90 THEN DBMS_OUTPUT.PUT_LINE('Grade A');
        WHEN v_score >= 75 THEN DBMS_OUTPUT.PUT_LINE('Grade B');
        WHEN v_score >= 60 THEN DBMS_OUTPUT.PUT_LINE('Grade C');
        ELSE DBMS_OUTPUT.PUT_LINE('Fail');
    END CASE;
END;
```
- The searched `CASE` evaluates conditions instead of specific values.

---

### **3. Loops**
Loops are used to repeat a block of code multiple times.

#### **Basic Example: Simple LOOP**
```plsql
DECLARE
    v_counter NUMBER := 1;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
        v_counter := v_counter + 1;
        EXIT WHEN v_counter > 5;
    END LOOP;
END;
```
- The loop continues until `v_counter` exceeds 5.

#### **Intermediate Example: WHILE LOOP**
```plsql
DECLARE
    v_counter NUMBER := 1;
BEGIN
    WHILE v_counter <= 5 LOOP
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
        v_counter := v_counter + 1;
    END LOOP;
END;
```
- The WHILE loop checks the condition before each iteration.

#### **Advanced Example: FOR LOOP**
```plsql
BEGIN
    FOR v_counter IN 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
    END LOOP;
END;
```
- The FOR loop runs from 1 to 5 automatically without manually incrementing the counter.

---

### **4. Nested Loops**
You can use loops inside other loops to handle more complex logic.

#### **Advanced Example: Nested Loops**
```plsql
BEGIN
    FOR i IN 1..3 LOOP
        DBMS_OUTPUT.PUT_LINE('Outer loop, i = ' || i);
        FOR j IN 1..2 LOOP
            DBMS_OUTPUT.PUT_LINE('Inner loop, j = ' || j);
        END LOOP;
    END LOOP;
END;
```
- The inner loop runs multiple times for each iteration of the outer loop.

---

### **5. EXIT and CONTINUE**
These control loop iterations explicitly.

#### **Basic Example: EXIT in a Loop**
```plsql
DECLARE
    v_counter NUMBER := 0;
BEGIN
    LOOP
        v_counter := v_counter + 1;
        IF v_counter = 3 THEN
            EXIT;  -- Exit the loop when counter is 3
        END IF;
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
    END LOOP;
END;
```
- The loop terminates when `v_counter` reaches 3.

#### **Advanced Example: CONTINUE in a Loop**
```plsql
BEGIN
    FOR i IN 1..5 LOOP
        IF i = 3 THEN
            CONTINUE;  -- Skip the rest of the loop when i equals 3
        END IF;
        DBMS_OUTPUT.PUT_LINE('Value: ' || i);
    END LOOP;
END;
```
- The loop skips the iteration when `i` equals 3 but continues with other values.

---

### **6. GOTO Statement**
`GOTO` allows you to jump to a specific label in your PL/SQL code.

#### **Advanced Example: GOTO Statement**
```plsql
DECLARE
    v_counter NUMBER := 0;
BEGIN
    <<start_loop>>
    v_counter := v_counter + 1;
    
    IF v_counter = 5 THEN
        GOTO end_loop;  -- Jump to the end of the loop
    ELSE
        DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
        GOTO start_loop;  -- Repeat the loop
    END IF;

    <<end_loop>>
    DBMS_OUTPUT.PUT_LINE('Loop ended');
END;
```
- `GOTO` jumps to the `start_loop` label and ends at `end_loop`.

---

### **7. Iterating with Cursors in Loops**
Cursors allow you to process query results row by row in loops.

#### **Intermediate Example: Simple Cursor with LOOP**
```plsql
DECLARE
    CURSOR emp_cursor IS SELECT employee_id, first_name FROM employees;
    v_employee_id employees.employee_id%TYPE;
    v_first_name employees.first_name%TYPE;
BEGIN
    OPEN emp_cursor;
    LOOP
        FETCH emp_cursor INTO v_employee_id, v_first_name;
        EXIT WHEN emp_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || ', Name: ' || v_first_name);
    END LOOP;
    CLOSE emp_cursor;
END;
```
- This loop iterates over the results of the cursor and fetches data row by row.

#### **Advanced Example: Cursor FOR LOOP**
```plsql
DECLARE
    CURSOR emp_cursor IS SELECT employee_id, first_name FROM employees;
BEGIN
    FOR emp_rec IN emp_cursor LOOP
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || emp_rec.employee_id || ', Name: ' || emp_rec.first_name);
    END LOOP;
END;
```
- The FOR loop automatically opens, fetches, and closes the cursor.

---

### **Summary**

- **Conditional statements**: Handle decision-making logic (`IF-THEN`, `CASE`).
- **Loops**: Handle repetitive tasks (`LOOP`, `WHILE`, `FOR`).
- **Nested loops and EXIT/CONTINUE**: Allow more complex control flows.
- **GOTO**: Provides control for jumping across sections of code.
- **Cursors in loops**: Enable row-by-row processing of query results.

These control structures provide essential logic handling in PL/SQL, allowing you to create efficient and powerful database procedures and functions.