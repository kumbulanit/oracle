In PL/SQL, a **static cursor** is a type of cursor that is defined once and remains constant throughout the program's execution. This cursor retrieves data from the database when it is opened and does not change until it is closed and reopened. Static cursors are beneficial when the data being queried does not change often and when you want to optimize performance.

### 1. Basic Static Cursor Example

#### Example 1: Simple Static Cursor

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, employee_name, salary
        FROM employees;  -- Assuming there is an 'employees' table

    v_employee_id   employees.employee_id%TYPE;  -- Variable to hold employee_id
    v_employee_name employees.employee_name%TYPE;  -- Variable to hold employee_name
    v_salary        employees.salary%TYPE;  -- Variable to hold salary
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    FETCH emp_cursor INTO v_employee_id, v_employee_name, v_salary;  -- Fetch first row

    -- Display the values
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id);
    DBMS_OUTPUT.PUT_LINE('Employee Name: ' || v_employee_name);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_salary);

    CLOSE emp_cursor;  -- Close the cursor
END;
```

**Explanation**:
- A static cursor `emp_cursor` is declared to select employee details from the `employees` table.
- The cursor is opened, and the first row is fetched into individual variables.
- The values are displayed, and then the cursor is closed.

### 2. Fetching Multiple Rows with a Static Cursor

#### Example 2: Looping Through Rows

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, employee_name, salary
        FROM employees;

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_employee_name, v_salary;  -- Fetch each row
        EXIT WHEN emp_cursor%NOTFOUND;  -- Exit when no more rows

        -- Display the values
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || 
                             ', Name: ' || v_employee_name || 
                             ', Salary: ' || v_salary);
    END LOOP;

    CLOSE emp_cursor;  -- Close the cursor
END;
```

**Explanation**:
- The cursor `emp_cursor` selects employee details.
- A loop is used to fetch each row until all rows have been processed (indicated by `emp_cursor%NOTFOUND`).
- Each employee's details are printed inside the loop.

### 3. Using a Static Cursor with WHERE Clause

#### Example 3: Conditional Fetching

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, employee_name, salary
        FROM employees
        WHERE salary > 50000;  -- Fetch only employees with salary greater than 50,000

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_employee_name, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;

        -- Display the values
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || 
                             ', Name: ' || v_employee_name || 
                             ', Salary: ' || v_salary);
    END LOOP;

    CLOSE emp_cursor;  -- Close the cursor
END;
```

**Explanation**:
- This example introduces a `WHERE` clause in the cursor definition to filter results based on a condition (salary greater than 50,000).
- The remaining logic remains the same as the previous examples, looping through and displaying results.

### 4. Using Attributes of Static Cursors

#### Example 4: Cursor Attributes

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, employee_name, salary
        FROM employees;

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
    v_total_rows    INTEGER := 0;  -- Variable to count total rows fetched
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_employee_name, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;

        -- Increment total rows
        v_total_rows := v_total_rows + 1;

        -- Display the values
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || 
                             ', Name: ' || v_employee_name || 
                             ', Salary: ' || v_salary);
    END LOOP;

    CLOSE emp_cursor;  -- Close the cursor

    -- Display total number of rows fetched
    DBMS_OUTPUT.PUT_LINE('Total Employees Fetched: ' || v_total_rows);
END;
```

**Explanation**:
- In this example, an additional variable `v_total_rows` is introduced to count the number of rows fetched.
- The total number of rows is displayed after the cursor has been processed.

### 5. Using Static Cursors in Procedures

#### Example 5: Static Cursor in a Procedure

```sql
CREATE OR REPLACE PROCEDURE display_high_salary_employees IS
    CURSOR emp_cursor IS
        SELECT employee_id, employee_name, salary
        FROM employees
        WHERE salary > 60000;  -- Fetch employees with salary greater than 60,000

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_employee_name, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;

        -- Display the values
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || 
                             ', Name: ' || v_employee_name || 
                             ', Salary: ' || v_salary);
    END LOOP;

    CLOSE emp_cursor;  -- Close the cursor
END;
/

-- Execute the procedure
BEGIN
    display_high_salary_employees;
END;
```

**Explanation**:
- A procedure `display_high_salary_employees` is created to encapsulate the logic of displaying employees with high salaries.
- The cursor is declared within the procedure, and it follows the same logic as previous examples.
- After defining the procedure, it is executed in an anonymous block.

### Conclusion

Static cursors in PL/SQL provide a structured way to handle query results. They are beneficial for fetching data in a predictable manner and allow for the use of attributes to manage the cursor's state. In summary, the examples covered:
- **Basic Static Cursor**: Declaring and fetching from a simple cursor.
- **Fetching Multiple Rows**: Looping through the cursor to process all rows.
- **Conditional Fetching**: Using a `WHERE` clause to filter results.
- **Cursor Attributes**: Utilizing cursor attributes for counting and managing rows.
- **Static Cursors in Procedures**: Encapsulating cursor logic in a reusable procedure.

These examples provide a comprehensive foundation for working with static cursors in PL/SQL.