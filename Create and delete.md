In PL/SQL, **CREATE** and **DELETE** operations are essential Data Manipulation Language (DML) commands used to manage data in database tables. Below, I will provide detailed examples of both commands, starting from basic usage to more advanced scenarios, along with explanations.

### Basic Example of CREATE

The **CREATE** statement is used to insert new records into a database table. The syntax for the `INSERT` command in SQL is as follows:

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

#### Example 1: Simple Insert

Suppose we have a table named `employees` with the following columns: `employee_id`, `first_name`, `last_name`, and `salary`.

```sql
CREATE TABLE employees (
    employee_id NUMBER PRIMARY KEY,
    first_name VARCHAR2(50),
    last_name VARCHAR2(50),
    salary NUMBER
);

-- Insert a new employee
INSERT INTO employees (employee_id, first_name, last_name, salary)
VALUES (1, 'John', 'Doe', 50000);
```

**Explanation:**
- **Table Creation**: The `CREATE TABLE` statement defines the structure of the `employees` table.
- **Insert Statement**: The `INSERT INTO` command adds a new employee record with `employee_id` of `1`, first name `John`, last name `Doe`, and salary of `50000`.

#### Executing the Insert

To execute the above insert statement, you can run it in an anonymous PL/SQL block:

```sql
BEGIN
    INSERT INTO employees (employee_id, first_name, last_name, salary)
    VALUES (1, 'John', 'Doe', 50000);
    COMMIT;  -- Commit the changes
END;
```

### Intermediate Example of CREATE with Multiple Records

You can also insert multiple records in a single `INSERT` statement.

#### Example 2: Insert Multiple Records

```sql
BEGIN
    INSERT ALL
        INTO employees (employee_id, first_name, last_name, salary) VALUES (2, 'Jane', 'Smith', 60000)
        INTO employees (employee_id, first_name, last_name, salary) VALUES (3, 'Robert', 'Johnson', 70000)
    SELECT * FROM dual;
    COMMIT;
END;
```

**Explanation:**
- **INSERT ALL**: This allows you to insert multiple records into the `employees` table in a single statement.
- **SELECT * FROM dual**: This is a dummy table used to execute the statement without needing a specific table reference.

### Advanced Example of CREATE with Error Handling

Adding error handling can improve robustness in your insert operations.

#### Example 3: Insert with Error Handling

```sql
DECLARE
    v_employee_id NUMBER := 4;
BEGIN
    INSERT INTO employees (employee_id, first_name, last_name, salary)
    VALUES (v_employee_id, 'Alice', 'Brown', 80000);
    COMMIT;
EXCEPTION
    WHEN DUP_VAL_ON_INDEX THEN
        DBMS_OUTPUT.PUT_LINE('Error: Employee ID ' || v_employee_id || ' already exists.');
        ROLLBACK;  -- Rollback if there's an error
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
        ROLLBACK;  -- Rollback for any other errors
END;
```

**Explanation:**
- **Error Handling**: The block uses an exception handler to catch the `DUP_VAL_ON_INDEX` error, which occurs when you try to insert a duplicate primary key.
- **ROLLBACK**: If an error occurs, it undoes the insert operation.

### Basic Example of DELETE

The **DELETE** statement is used to remove records from a database table. The syntax is as follows:

```sql
DELETE FROM table_name WHERE condition;
```

#### Example 4: Simple Delete

To delete an employee from the `employees` table:

```sql
BEGIN
    DELETE FROM employees 
    WHERE employee_id = 1;  -- Delete the employee with ID 1
    COMMIT;  -- Commit the changes
END;
```

**Explanation:**
- **DELETE Statement**: This command deletes the record where `employee_id` is `1`.
- **COMMIT**: This finalizes the changes made by the delete operation.

### Intermediate Example of DELETE with Conditional Logic

You can delete records based on conditions using subqueries.

#### Example 5: Delete with Subquery

```sql
BEGIN
    DELETE FROM employees 
    WHERE salary < (SELECT AVG(salary) FROM employees);  -- Delete employees with below-average salary
    COMMIT;
END;
```

**Explanation:**
- **Subquery**: The `DELETE` statement removes all employees whose salary is below the average salary calculated from the `employees` table.

### Advanced Example of DELETE with Error Handling

Just like with inserts, error handling can also be useful in delete operations.

#### Example 6: Delete with Error Handling

```sql
DECLARE
    v_employee_id NUMBER := 999;  -- Non-existing employee ID
BEGIN
    DELETE FROM employees 
    WHERE employee_id = v_employee_id;
    IF SQL%ROWCOUNT = 0 THEN
        DBMS_OUTPUT.PUT_LINE('No employee found with ID ' || v_employee_id);
    ELSE
        DBMS_OUTPUT.PUT_LINE('Employee with ID ' || v_employee_id || ' deleted successfully.');
    END IF;
    COMMIT;
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
        ROLLBACK;  -- Rollback if there's an error
END;
```

**Explanation:**
- **SQL%ROWCOUNT**: This attribute gives the number of rows affected by the last DML statement. If no rows were deleted, it prints a message indicating that no employee was found.
- **Error Handling**: Catches any unexpected errors and performs a rollback if necessary.

### Summary

- The **CREATE** operation (via `INSERT`) is used to add records to a table, while the **DELETE** operation removes records.
- Basic examples show single inserts and deletes, while intermediate examples demonstrate multiple inserts and conditional deletes.
- Advanced examples illustrate the importance of error handling in both operations to manage exceptions effectively.

These examples cover various aspects of the **CREATE** and **DELETE** commands in PL/SQL, providing a progression from basic to advanced concepts, and including detailed explanations for clarity.