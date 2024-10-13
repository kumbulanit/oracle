Cursors in PL/SQL are used to retrieve and manipulate data from the database in a controlled manner. They allow developers to process rows returned by a SQL query one at a time. There are two types of cursors: **implicit cursors** and **explicit cursors**. Below, we will explore both types with detailed examples.

### 1. Implicit Cursors

Implicit cursors are automatically created by PL/SQL for single SQL statements. They are used when a SELECT statement returns a single row, and you do not need to manage the cursor explicitly.

#### Example 1: Implicit Cursor

```sql
DECLARE
    v_employee_name VARCHAR2(100);
BEGIN
    SELECT employee_name
    INTO v_employee_name
    FROM employees
    WHERE employee_id = 101;  -- Assuming employee_id 101 exists

    DBMS_OUTPUT.PUT_LINE('Employee Name: ' || v_employee_name);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employee found with the given ID.');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
```

**Explanation**:
- An implicit cursor is created when the `SELECT` statement is executed.
- The `INTO` clause is used to store the result in the variable `v_employee_name`.
- If no employee with the specified ID is found, a `NO_DATA_FOUND` exception is raised and handled.

### 2. Explicit Cursors

Explicit cursors provide more control over the processing of SQL query results. You can declare a cursor, open it, fetch rows, and close it. 

#### Steps to Use an Explicit Cursor:

1. **Declare the Cursor**: Define the SQL query that the cursor will use.
2. **Open the Cursor**: Allocate memory for the cursor and execute the SQL query.
3. **Fetch Rows**: Retrieve each row from the cursor one at a time.
4. **Close the Cursor**: Release the cursor resources.

#### Example 2: Explicit Cursor

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, employee_name, salary
        FROM employees
        WHERE department_id = 10;  -- Assuming department_id 10 exists

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor;  -- Step 2: Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_employee_name, v_salary;  -- Step 3: Fetch rows
        EXIT WHEN emp_cursor%NOTFOUND;  -- Exit the loop when no more rows

        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || 
                             ', Name: ' || v_employee_name || 
                             ', Salary: ' || v_salary);
    END LOOP;

    CLOSE emp_cursor;  -- Step 4: Close the cursor
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
```

**Explanation**:
- A cursor `emp_cursor` is declared to retrieve employees from a specific department.
- The cursor is opened using the `OPEN` statement.
- A `LOOP` is used to fetch rows until no more rows are found (checked using `%NOTFOUND`).
- Each fetched row is printed using `DBMS_OUTPUT.PUT_LINE`.
- Finally, the cursor is closed to release resources.

### 3. Cursors with Parameters

You can also create parameterized cursors to make them more dynamic.

#### Example 3: Parameterized Cursor

```sql
DECLARE
    CURSOR emp_cursor (p_department_id IN NUMBER) IS
        SELECT employee_id, employee_name, salary
        FROM employees
        WHERE department_id = p_department_id;

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
    v_dept_id       NUMBER := 20;  -- Example department ID
BEGIN
    OPEN emp_cursor(v_dept_id);  -- Step 2: Open the cursor with a parameter

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_employee_name, v_salary;  -- Step 3: Fetch rows
        EXIT WHEN emp_cursor%NOTFOUND;  -- Exit the loop when no more rows

        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || 
                             ', Name: ' || v_employee_name || 
                             ', Salary: ' || v_salary);
    END LOOP;

    CLOSE emp_cursor;  -- Step 4: Close the cursor
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
```

**Explanation**:
- This example shows how to create a parameterized cursor that takes `p_department_id` as an input.
- The cursor is opened with a specific department ID.
- The process of fetching and displaying the results remains the same.

### 4. Cursors FOR UPDATE

You can use the `FOR UPDATE` clause with cursors to lock the rows retrieved by the cursor for update operations.

#### Example 4: Cursor FOR UPDATE

```sql
DECLARE
    CURSOR emp_cursor FOR
        SELECT employee_id, salary
        FROM employees
        WHERE department_id = 30
        FOR UPDATE;  -- Lock rows for update

    v_employee_id   employees.employee_id%TYPE;
    v_new_salary    NUMBER;
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id;  -- Fetch employee ID
        EXIT WHEN emp_cursor%NOTFOUND;  -- Exit when no more rows

        -- Calculate new salary (10% increase)
        v_new_salary := v_salary * 1.10;  

        -- Update the salary
        UPDATE employees
        SET salary = v_new_salary
        WHERE CURRENT OF emp_cursor;  -- Update the current row

        DBMS_OUTPUT.PUT_LINE('Updated Employee ID: ' || v_employee_id || 
                             ' New Salary: ' || v_new_salary);
    END LOOP;

    CLOSE emp_cursor;  -- Close the cursor
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
```

**Explanation**:
- The `FOR UPDATE` clause is added to the cursor declaration to lock the rows.
- The `UPDATE` statement uses `WHERE CURRENT OF emp_cursor` to update the row currently being fetched.
- Each updated employee's ID and new salary are displayed.

### 5. Advanced Cursor Example with Multiple Fetches

#### Example 5: Using Multiple Cursors

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, employee_name
        FROM employees;

    CURSOR dept_cursor IS
        SELECT department_id, department_name
        FROM departments;

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_dept_id       departments.department_id%TYPE;
    v_dept_name     departments.department_name%TYPE;
BEGIN
    OPEN emp_cursor;
    OPEN dept_cursor;

    -- Fetch employee details
    LOOP
        FETCH emp_cursor INTO v_employee_id, v_employee_name;
        EXIT WHEN emp_cursor%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || 
                             ', Name: ' || v_employee_name);
    END LOOP;

    -- Fetch department details
    LOOP
        FETCH dept_cursor INTO v_dept_id, v_dept_name;
        EXIT WHEN dept_cursor%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE('Department ID: ' || v_dept_id || 
                             ', Name: ' || v_dept_name);
    END LOOP;

    CLOSE emp_cursor;
    CLOSE dept_cursor;
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
```

**Explanation**:
- This example uses two separate cursors: one for employees and another for departments.
- Each cursor is opened, and a loop is used to fetch and display details from both cursors.
- The cursors are closed at the end.

### Conclusion

- **Implicit Cursors**: Automatically managed by PL/SQL for single SQL statements.
- **Explicit Cursors**: Allow developers to manage the cursor lifecycle explicitly, providing more control.
- **Parameterized Cursors**: Allow passing parameters to make cursors dynamic.
- **FOR UPDATE Cursors**: Lock rows for updates, ensuring data integrity during modification.
- **Multiple Cursors**: Demonstrates the ability to work with several cursors in a single block.

Using cursors effectively allows developers to process query results efficiently while maintaining control over data manipulation and application logic in PL/SQL.