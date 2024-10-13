In PL/SQL, **parameterized cursors** allow you to pass parameters to the cursor at runtime, enabling dynamic querying based on the provided parameters. This feature enhances flexibility by letting you reuse the same cursor definition with different values. Below are examples ranging from basic to advanced for using parameterized cursors in PL/SQL.

### 1. Basic Parameterized Cursor

#### Example 1: Simple Parameterized Cursor

```sql
DECLARE
    -- Define a parameterized cursor
    CURSOR emp_cursor (min_salary NUMBER) IS
        SELECT employee_id, employee_name, salary
        FROM employees
        WHERE salary > min_salary;  -- Use the parameter in the WHERE clause

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor(50000);  -- Pass parameter value 50000

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
- The cursor `emp_cursor` is defined with a parameter `min_salary` which is used in the `WHERE` clause to filter employees based on salary.
- When opening the cursor, a specific value (`50000`) is passed, and it fetches and displays all employees with a salary greater than this value.

### 2. Using Multiple Parameters

#### Example 2: Cursor with Multiple Parameters

```sql
DECLARE
    -- Define a parameterized cursor with two parameters
    CURSOR emp_cursor (min_salary NUMBER, max_salary NUMBER) IS
        SELECT employee_id, employee_name, salary
        FROM employees
        WHERE salary BETWEEN min_salary AND max_salary;  -- Use both parameters

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor(30000, 70000);  -- Pass min and max salary values

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
- This example defines a cursor with two parameters: `min_salary` and `max_salary`.
- It fetches employees whose salaries fall within the specified range and displays the results.

### 3. Parameterized Cursor in a Procedure

#### Example 3: Parameterized Cursor in a Procedure

```sql
CREATE OR REPLACE PROCEDURE display_salary_range_employees(p_min_salary NUMBER, p_max_salary NUMBER) IS
    -- Define the parameterized cursor
    CURSOR emp_cursor (min_salary NUMBER, max_salary NUMBER) IS
        SELECT employee_id, employee_name, salary
        FROM employees
        WHERE salary BETWEEN min_salary AND max_salary;

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor(p_min_salary, p_max_salary);  -- Pass parameters to the cursor

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

-- Execute the procedure with specific salary range
BEGIN
    display_salary_range_employees(20000, 60000);
END;
```

**Explanation**:
- A procedure `display_salary_range_employees` is created to encapsulate the logic for displaying employees within a specified salary range.
- It uses a parameterized cursor that accepts minimum and maximum salary values as arguments.
- The procedure is executed with specific values (`20000` and `60000`).

### 4. Advanced Example: Using Cursor Attributes with Parameters

#### Example 4: Advanced Cursor with Attributes

```sql
DECLARE
    CURSOR emp_cursor (min_salary NUMBER) IS
        SELECT employee_id, employee_name, salary
        FROM employees
        WHERE salary > min_salary;

    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
    v_total_rows    INTEGER := 0;  -- Variable to count total rows
BEGIN
    OPEN emp_cursor(50000);  -- Pass parameter value

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_employee_name, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;

        v_total_rows := v_total_rows + 1;  -- Increment total row count

        -- Display the values
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee_id || 
                             ', Name: ' || v_employee_name || 
                             ', Salary: ' || v_salary);
    END LOOP;

    -- Display total number of rows fetched
    DBMS_OUTPUT.PUT_LINE('Total Employees Fetched: ' || v_total_rows);

    CLOSE emp_cursor;  -- Close the cursor
END;
```

**Explanation**:
- This advanced example incorporates a counter to keep track of the total number of rows fetched by the parameterized cursor.
- The logic remains similar, but now we are also displaying the total number of employees that meet the criteria.

### 5. Dynamic Query with Parameterized Cursors

#### Example 5: Dynamic SQL with Parameterized Cursors

```sql
DECLARE
    v_min_salary NUMBER := 40000;
    v_max_salary NUMBER := 80000;
    v_sql        VARCHAR2(1000);
    emp_cursor   SYS_REFCURSOR;  -- Using REF CURSOR for dynamic SQL
    v_employee_id   employees.employee_id%TYPE;
    v_employee_name employees.employee_name%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    -- Construct dynamic SQL query with parameters
    v_sql := 'SELECT employee_id, employee_name, salary FROM employees ' ||
             'WHERE salary BETWEEN :1 AND :2';

    OPEN emp_cursor FOR v_sql USING v_min_salary, v_max_salary;  -- Open cursor with parameters

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
- This example demonstrates how to create a dynamic SQL statement that uses parameterized cursors with `SYS_REFCURSOR`.
- The SQL query string is built dynamically and parameters are bound using the `USING` clause when opening the cursor.

### Conclusion

Parameterized cursors in PL/SQL are a powerful feature that allows for flexible and dynamic data retrieval. The examples provided cover a range of scenarios, from basic usage to more advanced techniques, including:

1. **Basic Parameterized Cursor**: Simple usage with one parameter.
2. **Multiple Parameters**: Using two parameters to filter results.
3. **Procedure Encapsulation**: Wrapping the cursor in a procedure for reusability.
4. **Cursor Attributes**: Counting rows fetched while using a parameterized cursor.
5. **Dynamic SQL with Parameterized Cursors**: Utilizing dynamic SQL for more complex queries.

These examples provide a comprehensive foundation for using parameterized cursors in PL/SQL effectively.