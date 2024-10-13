PL/SQL blocks are the building units of PL/SQL programs. They consist of sections that can handle executable statements, variables, exception handling, and more. Here's a breakdown of different types of PL/SQL blocks from basic to advanced with examples.

### **Basic Structure of a PL/SQL Block**
A PL/SQL block has three sections:

1. **Declarative Section** (optional): Where variables, constants, and other structures are declared.
2. **Executable Section** (mandatory): Contains the actual code to be executed.
3. **Exception Handling Section** (optional): Handles errors and exceptions.

### **Basic PL/SQL Block**
```plsql
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello, PL/SQL World!');
END;
```
- This is the simplest form of a PL/SQL block. It starts with `BEGIN` and ends with `END`.
- The `DBMS_OUTPUT.PUT_LINE` procedure prints text to the output console.

### **PL/SQL Block with Variables (Anonymous Block)**
In this block, we declare and use variables.

```plsql
DECLARE
    v_name VARCHAR2(50);
    v_age NUMBER(3);
BEGIN
    v_name := 'Alice';
    v_age := 25;
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_name);
    DBMS_OUTPUT.PUT_LINE('Age: ' || v_age);
END;
```
- The `DECLARE` section defines variables `v_name` and `v_age`.
- The values are assigned in the `BEGIN` section, and then they are output using `DBMS_OUTPUT.PUT_LINE`.

### **PL/SQL Block with Control Statements**
This block includes an `IF-THEN-ELSE` control structure to demonstrate decision-making.

```plsql
DECLARE
    v_score NUMBER := 85;
BEGIN
    IF v_score >= 90 THEN
        DBMS_OUTPUT.PUT_LINE('Grade: A');
    ELSIF v_score >= 80 THEN
        DBMS_OUTPUT.PUT_LINE('Grade: B');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Grade: C');
    END IF;
END;
```
- The block checks the value of `v_score` and prints a different grade based on its value.

### **PL/SQL Block with Loops**
This example includes a `FOR` loop to iterate over a range of values.

```plsql
BEGIN
    FOR i IN 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE('Iteration: ' || i);
    END LOOP;
END;
```
- A loop iterates over values from 1 to 5 and prints each iteration.

### **PL/SQL Block with Exception Handling**
Here, we introduce exception handling to manage errors that may occur during execution.

```plsql
DECLARE
    v_num1 NUMBER := 10;
    v_num2 NUMBER := 0;
    v_result NUMBER;
BEGIN
    v_result := v_num1 / v_num2;  -- This will cause a division by zero error
    DBMS_OUTPUT.PUT_LINE('Result: ' || v_result);
EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE('Error: Division by zero is not allowed.');
END;
```
- The `EXCEPTION` block handles the division by zero error and prints an appropriate message.

### **Advanced PL/SQL Block with Nested Blocks**
Nested blocks allow PL/SQL code to be modular and better organized.

```plsql
DECLARE
    v_outer_var NUMBER := 100;
BEGIN
    DBMS_OUTPUT.PUT_LINE('Outer Block Variable: ' || v_outer_var);

    DECLARE
        v_inner_var NUMBER := 50;
    BEGIN
        DBMS_OUTPUT.PUT_LINE('Inner Block Variable: ' || v_inner_var);
    END;

    DBMS_OUTPUT.PUT_LINE('Back to Outer Block: ' || v_outer_var);
END;
```
- This example demonstrates nesting blocks within a PL/SQL program. The inner block has access to its own variables but not those of the outer block unless explicitly passed.

### **Advanced PL/SQL Block with Cursors**
In this example, a cursor is used to fetch multiple rows from a table.

```plsql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, first_name FROM employees WHERE department_id = 10;
    v_emp_id employees.employee_id%TYPE;
    v_first_name employees.first_name%TYPE;
BEGIN
    OPEN emp_cursor;
    LOOP
        FETCH emp_cursor INTO v_emp_id, v_first_name;
        EXIT WHEN emp_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_emp_id || ', Name: ' || v_first_name);
    END LOOP;
    CLOSE emp_cursor;
END;
```
- The `emp_cursor` retrieves employee details from the `employees` table. The `LOOP` is used to fetch and print each employee's information until all rows are processed.

### **Advanced PL/SQL Block with Dynamic SQL**
Dynamic SQL allows you to construct and execute SQL statements at runtime.

```plsql
DECLARE
    v_sql VARCHAR2(200);
    v_table_name VARCHAR2(50) := 'employees';
    v_count NUMBER;
BEGIN
    v_sql := 'SELECT COUNT(*) FROM ' || v_table_name;
    EXECUTE IMMEDIATE v_sql INTO v_count;
    DBMS_OUTPUT.PUT_LINE('Total rows in ' || v_table_name || ': ' || v_count);
END;
```
- The `EXECUTE IMMEDIATE` statement executes a dynamically constructed SQL statement to count the number of rows in the `employees` table.

### **Advanced PL/SQL Block with Bulk Operations**
Bulk processing allows for more efficient handling of large volumes of data.

```plsql
DECLARE
    TYPE emp_table IS TABLE OF employees%ROWTYPE;
    v_emp_data emp_table;
BEGIN
    SELECT * BULK COLLECT INTO v_emp_data FROM employees WHERE department_id = 10;
    
    FOR i IN 1..v_emp_data.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_emp_data(i).employee_id || 
                             ', Name: ' || v_emp_data(i).first_name);
    END LOOP;
END;
```
- This example demonstrates the use of the `BULK COLLECT` to fetch multiple rows at once, improving performance when handling large datasets.

---

### Summary of PL/SQL Block Types:

1. **Anonymous Blocks**: Non-named blocks of PL/SQL code, like the ones above.
2. **Named Blocks**: Stored procedures, functions, triggers, etc. (more advanced topics).
3. **Control Blocks**: Blocks with decision-making and loops (`IF`, `FOR`, `WHILE`).
4. **Exception Handling Blocks**: Blocks designed to catch and handle errors.
5. **Dynamic SQL Blocks**: Blocks that allow for SQL execution built at runtime.
6. **Cursors and Bulk Operations**: Blocks that deal with data retrieval and large data manipulation.

With these examples, you have seen how PL/SQL blocks can be constructed from the simplest anonymous blocks to more complex ones involving cursors and dynamic SQL.