In PL/SQL, variables are used to store data temporarily during the execution of a program. They must be declared in the **Declarative Section** before being used in the executable section. Here's a detailed explanation and examples of declaring and using variables from basic to advanced levels.

### **Basic Variable Declaration and Use**

In a basic PL/SQL block, variables are declared in the `DECLARE` section and used in the `BEGIN` section.

#### Example 1: Simple Declaration and Assignment
```plsql
DECLARE
    v_name VARCHAR2(50);  -- Declaring a variable of type VARCHAR2
    v_age NUMBER(3);      -- Declaring a variable of type NUMBER
BEGIN
    v_name := 'Alice';    -- Assigning a value to the variable
    v_age := 25;          -- Assigning a numeric value
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_name);  -- Displaying the value
    DBMS_OUTPUT.PUT_LINE('Age: ' || v_age);
END;
```
- **v_name** is a string variable with a maximum length of 50.
- **v_age** is a numeric variable that can store up to 3 digits.

---

### **Intermediate Examples of Using Variables**

#### Example 2: Declaring and Using Constants
Constants are variables whose value cannot be changed after assignment.

```plsql
DECLARE
    v_pi CONSTANT NUMBER := 3.14159;  -- Declaring a constant
    v_radius NUMBER := 10;
    v_area NUMBER;
BEGIN
    v_area := v_pi * v_radius * v_radius;  -- Using the constant in a formula
    DBMS_OUTPUT.PUT_LINE('Area of the circle: ' || v_area);
END;
```
- The `CONSTANT` keyword makes the variable immutable after assignment.

#### Example 3: Declaring Variables Based on Database Columns
Using `%TYPE` allows you to declare a variable with the same data type as a database column.

```plsql
DECLARE
    v_employee_id employees.employee_id%TYPE;  -- Declaring a variable with the same data type as employee_id column
    v_salary employees.salary%TYPE;            -- Declaring a variable with the same data type as salary column
BEGIN
    v_employee_id := 1001;
    v_salary := 5000;
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_salary);
END;
```
- **`%TYPE`** helps avoid hardcoding data types, ensuring that variables match the column data type.

#### Example 4: Declaring and Using Composite Types (Records)
A record is a composite variable that can hold multiple fields of different types.

```plsql
DECLARE
    TYPE emp_record IS RECORD (
        employee_id employees.employee_id%TYPE,
        first_name employees.first_name%TYPE,
        salary employees.salary%TYPE
    );
    v_employee emp_record;  -- Declaring a record variable of type emp_record
BEGIN
    v_employee.employee_id := 1001;
    v_employee.first_name := 'Alice';
    v_employee.salary := 6000;

    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee.employee_id);
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_employee.first_name);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_employee.salary);
END;
```
- Here, we define a `RECORD` type to store multiple fields related to employees and use that record to assign and print values.

---

### **Advanced Examples of Using Variables**

#### Example 5: Using `%ROWTYPE` to Declare Variables for Entire Rows
`%ROWTYPE` allows you to declare a variable that represents an entire row of a table.

```plsql
DECLARE
    v_employee employees%ROWTYPE;  -- Declaring a variable to hold an entire row from the employees table
BEGIN
    SELECT * INTO v_employee FROM employees WHERE employee_id = 1001;
    
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee.employee_id);
    DBMS_OUTPUT.PUT_LINE('Name: ' || v_employee.first_name);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_employee.salary);
END;
```
- Using `%ROWTYPE`, we can work with entire rows of a table, making it useful when retrieving full records.

#### Example 6: Using Cursors with Variables
In this example, variables are used with a cursor to fetch multiple rows from a table.

```plsql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, first_name, salary FROM employees WHERE department_id = 10;
    v_employee_id employees.employee_id%TYPE;
    v_first_name employees.first_name%TYPE;
    v_salary employees.salary%TYPE;
BEGIN
    OPEN emp_cursor;
    LOOP
        FETCH emp_cursor INTO v_employee_id, v_first_name, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || ', Name: ' || v_first_name || ', Salary: ' || v_salary);
    END LOOP;
    CLOSE emp_cursor;
END;
```
- Variables `v_employee_id`, `v_first_name`, and `v_salary` are used to hold values fetched from the cursor.

#### Example 7: Using Bind Variables in Dynamic SQL
Bind variables are used to improve performance and security in dynamic SQL by avoiding hardcoding values directly into SQL statements.

```plsql
DECLARE
    v_table_name VARCHAR2(50) := 'employees';
    v_sql VARCHAR2(200);
    v_count NUMBER;
BEGIN
    v_sql := 'SELECT COUNT(*) FROM ' || v_table_name;
    EXECUTE IMMEDIATE v_sql INTO v_count;
    DBMS_OUTPUT.PUT_LINE('Total employees: ' || v_count);
END;
```
- The SQL statement is dynamically created, and the `v_count` variable holds the result.

---

### **Advanced Use of Composite Data Types: Collections**

#### Example 8: Using Nested Tables (PL/SQL Table Type)
A nested table is a collection that can store multiple rows of data.

```plsql
DECLARE
    TYPE emp_table IS TABLE OF employees.employee_id%TYPE;
    v_employee_ids emp_table;
BEGIN
    SELECT employee_id BULK COLLECT INTO v_employee_ids FROM employees WHERE department_id = 10;
    
    FOR i IN 1..v_employee_ids.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_ids(i));
    END LOOP;
END;
```
- In this example, a nested table type `emp_table` is used to store multiple employee IDs, and the `BULK COLLECT` operation efficiently retrieves multiple rows at once.

#### Example 9: Using Associative Arrays (Index-by Tables)
Associative arrays allow you to index a collection by integers or strings.

```plsql
DECLARE
    TYPE emp_salary_table IS TABLE OF employees.salary%TYPE INDEX BY employees.employee_id%TYPE;
    v_salaries emp_salary_table;
    v_employee_id employees.employee_id%TYPE;
BEGIN
    SELECT employee_id, salary BULK COLLECT INTO v_employee_id, v_salaries FROM employees WHERE department_id = 10;
    
    FOR i IN v_salaries.FIRST..v_salaries.LAST LOOP
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || i || ', Salary: ' || v_salaries(i));
    END LOOP;
END;
```
- Here, an associative array stores salaries for employees, indexed by their employee IDs.

---

### **Summary**

- **Basic**: Declare and assign variables (e.g., `VARCHAR2`, `NUMBER`).
- **Intermediate**: Use constants, `RECORDS`, and `%TYPE` to reference database columns dynamically.
- **Advanced**: Use `%ROWTYPE` for entire rows, cursors with variables for fetching multiple rows, dynamic SQL with bind variables, and collections like nested tables and associative arrays for more complex data handling.

These examples demonstrate how variables in PL/SQL can be used in increasingly sophisticated ways to store, manipulate, and retrieve data, making PL/SQL a powerful language for working with Oracle databases.