In PL/SQL, a **cursor FOR UPDATE** is used when you want to update rows that have been retrieved by a cursor. This type of cursor locks the selected rows for update, preventing other sessions from modifying them until the transaction is completed. Below are examples ranging from basic to advanced for using **cursor FOR UPDATE** in PL/SQL.

### 1. Basic Cursor FOR UPDATE Example

#### Example 1: Simple Cursor FOR UPDATE

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, salary
        FROM employees
        WHERE department_id = 10
        FOR UPDATE;  -- Locking the selected rows for update

    v_employee_id   employees.employee_id%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;

        -- Update the salary of the selected employee
        UPDATE employees
        SET salary = salary * 1.10  -- Increase salary by 10%
        WHERE CURRENT OF emp_cursor;  -- Reference the current row of the cursor

        -- Display the updated values
        DBMS_OUTPUT.PUT_LINE('Updated Employee ID: ' || v_employee_id || 
                             ', New Salary: ' || (v_salary * 1.10));
    END LOOP;

    CLOSE emp_cursor;  -- Close the cursor
END;
```

**Explanation**:
- The cursor `emp_cursor` retrieves employee IDs and salaries from the `employees` table for a specific department.
- The `FOR UPDATE` clause locks the selected rows for modification.
- The `UPDATE` statement uses `WHERE CURRENT OF emp_cursor` to update the salary of the current row being processed by the cursor.

### 2. Cursor FOR UPDATE with Error Handling

#### Example 2: Cursor FOR UPDATE with Exception Handling

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, salary
        FROM employees
        WHERE department_id = 20
        FOR UPDATE;  -- Locking the selected rows for update

    v_employee_id   employees.employee_id%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;

        -- Update the salary of the selected employee
        UPDATE employees
        SET salary = salary * 1.05  -- Increase salary by 5%
        WHERE CURRENT OF emp_cursor;  -- Reference the current row of the cursor

        DBMS_OUTPUT.PUT_LINE('Updated Employee ID: ' || v_employee_id || 
                             ', New Salary: ' || (v_salary * 1.05));
    END LOOP;

    CLOSE emp_cursor;  -- Close the cursor
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An error occurred: ' || SQLERRM);
        CLOSE emp_cursor;  -- Ensure the cursor is closed on error
END;
```

**Explanation**:
- This example adds exception handling to manage potential errors during execution.
- If an error occurs, it outputs the error message and ensures that the cursor is closed to avoid memory leaks.

### 3. Using Cursor FOR UPDATE with Multiple Cursors

#### Example 3: Multiple Cursors with FOR UPDATE

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, salary
        FROM employees
        WHERE department_id = 30
        FOR UPDATE;  -- Locking the selected rows for update

    CURSOR dept_cursor IS
        SELECT department_id, budget
        FROM departments
        WHERE department_id = 30
        FOR UPDATE;  -- Locking the selected rows for update

    v_employee_id   employees.employee_id%TYPE;
    v_salary        employees.salary%TYPE;
    v_budget        departments.budget%TYPE;
BEGIN
    OPEN emp_cursor;  -- Open employee cursor
    OPEN dept_cursor;  -- Open department cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;

        -- Update the salary of the selected employee
        UPDATE employees
        SET salary = salary * 1.07  -- Increase salary by 7%
        WHERE CURRENT OF emp_cursor;  -- Reference the current row of the cursor

        DBMS_OUTPUT.PUT_LINE('Updated Employee ID: ' || v_employee_id || 
                             ', New Salary: ' || (v_salary * 1.07));
    END LOOP;

    -- Update the department budget based on the total salaries updated
    FETCH dept_cursor INTO v_budget;
    UPDATE departments
    SET budget = budget + 10000  -- Increase budget by 10,000
    WHERE CURRENT OF dept_cursor;  -- Reference the current row of the department cursor

    CLOSE emp_cursor;  -- Close employee cursor
    CLOSE dept_cursor;  -- Close department cursor
END;
```

**Explanation**:
- This example shows how to use multiple cursors with `FOR UPDATE`.
- It updates employee salaries while also updating the budget of the corresponding department based on the changes made.
- The `WHERE CURRENT OF` clause is used to refer to the current rows in both cursors.

### 4. Advanced Example: Using Cursor FOR UPDATE with a Subquery

#### Example 4: Cursor FOR UPDATE with Subquery

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, salary
        FROM employees
        WHERE salary < (SELECT AVG(salary) FROM employees)  -- Subquery to get average salary
        FOR UPDATE;  -- Locking the selected rows for update

    v_employee_id   employees.employee_id%TYPE;
    v_salary        employees.salary%TYPE;
BEGIN
    OPEN emp_cursor;  -- Open the cursor

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_salary;
        EXIT WHEN emp_cursor%NOTFOUND;

        -- Update the salary of the selected employee
        UPDATE employees
        SET salary = salary + 5000  -- Increase salary by 5,000
        WHERE CURRENT OF emp_cursor;  -- Reference the current row of the cursor

        DBMS_OUTPUT.PUT_LINE('Updated Employee ID: ' || v_employee_id || 
                             ', New Salary: ' || (v_salary + 5000));
    END LOOP;

    CLOSE emp_cursor;  -- Close the cursor
END;
```

**Explanation**:
- This advanced example shows how to use a subquery within the cursor definition to select employees with a salary below the average salary.
- The selected rows are locked for update, and their salaries are increased by 5,000.
- This demonstrates more complex filtering using a subquery while still utilizing the cursor FOR UPDATE functionality.

### Conclusion

The **cursor FOR UPDATE** feature in PL/SQL allows developers to lock and update rows retrieved by a cursor, ensuring data integrity during transactions. The examples provided cover a range of scenarios from basic usage to more advanced techniques, including:

1. **Basic Cursor FOR UPDATE**: Simple retrieval and update of employee salaries.
2. **Error Handling**: Managing exceptions during cursor operations.
3. **Multiple Cursors**: Working with more than one cursor to update related tables.
4. **Subquery Usage**: Incorporating subqueries in cursor definitions for more complex filtering.

These examples provide a comprehensive foundation for using **cursor FOR UPDATE** in PL/SQL effectively.