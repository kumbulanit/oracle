The `SELECT` statement in PL/SQL is used to query data from a database. It can be employed in various ways, including simple queries, queries with joins, aggregate functions, and more. In PL/SQL, `SELECT` statements can be used in several contexts, such as into clauses for retrieving data into variables or records, and within cursor definitions.

### Basic SELECT Statement

A basic `SELECT` statement retrieves data from a single table.

#### Example 1: Simple SELECT

```sql
SELECT first_name, last_name 
FROM employees 
WHERE department_id = 10;
```

**Explanation:**
- This query selects the `first_name` and `last_name` columns from the `employees` table where the `department_id` is 10.

### Using SELECT INTO

In PL/SQL, you can use `SELECT INTO` to retrieve values into variables.

#### Example 2: SELECT INTO

```sql
DECLARE
    v_first_name employees.first_name%TYPE;
    v_last_name employees.last_name%TYPE;
BEGIN
    SELECT first_name, last_name 
    INTO v_first_name, v_last_name 
    FROM employees 
    WHERE employee_id = 1;

    DBMS_OUTPUT.PUT_LINE('Employee Name: ' || v_first_name || ' ' || v_last_name);
END;
```

**Explanation:**
- In this example, `v_first_name` and `v_last_name` are declared as variables to store the retrieved values.
- The `SELECT INTO` statement retrieves the first and last names of the employee with `employee_id` 1 and stores them in the variables.
- The `DBMS_OUTPUT.PUT_LINE` procedure prints the employee's name.

### Advanced SELECT Examples

#### Example 3: SELECT with Multiple Records

When selecting multiple records, you can use collections or cursors to handle the data.

```sql
DECLARE
    TYPE emp_record IS RECORD (
        employee_id employees.employee_id%TYPE,
        first_name employees.first_name%TYPE,
        last_name employees.last_name%TYPE
    );

    TYPE emp_table IS TABLE OF emp_record;
    employees_data emp_table;
BEGIN
    SELECT employee_id, first_name, last_name
    BULK COLLECT INTO employees_data
    FROM employees
    WHERE department_id = 10;

    FOR i IN 1 .. employees_data.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || employees_data(i).employee_id || 
                             ', Name: ' || employees_data(i).first_name || 
                             ' ' || employees_data(i).last_name);
    END LOOP;
END;
```

**Explanation:**
- Here, a record type `emp_record` is defined to hold employee data.
- A table type `emp_table` is defined to hold multiple records.
- The `SELECT ... BULK COLLECT INTO` statement retrieves all employees in department 10 and stores them in the `employees_data` collection.
- A loop is used to print each employee's details.

#### Example 4: SELECT with Joins

You can join multiple tables to retrieve related data.

```sql
DECLARE
    v_employee_name employees.first_name%TYPE;
    v_department_name departments.department_name%TYPE;
BEGIN
    SELECT e.first_name, d.department_name
    INTO v_employee_name, v_department_name
    FROM employees e
    JOIN departments d ON e.department_id = d.department_id
    WHERE e.employee_id = 1;

    DBMS_OUTPUT.PUT_LINE('Employee: ' || v_employee_name || ', Department: ' || v_department_name);
END;
```

**Explanation:**
- This example retrieves the employee's name and their department name by joining the `employees` and `departments` tables.
- The values are stored in variables and printed out.

### Aggregate Functions and GROUP BY

You can use aggregate functions like `COUNT`, `SUM`, `AVG`, etc., with a `GROUP BY` clause.

#### Example 5: Aggregate Function

```sql
DECLARE
    v_total_salary NUMBER;
BEGIN
    SELECT SUM(salary)
    INTO v_total_salary
    FROM employees
    WHERE department_id = 10;

    DBMS_OUTPUT.PUT_LINE('Total Salary for Department 10: ' || v_total_salary);
END;
```

**Explanation:**
- This example calculates the total salary of all employees in department 10 using the `SUM` function and stores the result in the variable `v_total_salary`.

#### Example 6: GROUP BY with HAVING

```sql
DECLARE
    v_department_name departments.department_name%TYPE;
    v_average_salary NUMBER;
BEGIN
    SELECT d.department_name, AVG(e.salary)
    INTO v_department_name, v_average_salary
    FROM employees e
    JOIN departments d ON e.department_id = d.department_id
    GROUP BY d.department_name
    HAVING AVG(e.salary) > 60000;

    DBMS_OUTPUT.PUT_LINE('Department: ' || v_department_name || ', Average Salary: ' || v_average_salary);
END;
```

**Explanation:**
- This example retrieves the department names and their average salaries, filtering to show only departments with an average salary greater than 60,000 using the `HAVING` clause.

### Summary

The `SELECT` statement in PL/SQL is powerful and versatile, allowing for:

- Retrieving single or multiple records.
- Using joins to fetch data from multiple tables.
- Performing aggregate calculations with grouping and filtering.
- Utilizing collections for bulk data handling.

These examples demonstrate a range of `SELECT` operations from basic queries to more complex uses involving collections and aggregate functions. Each example illustrates how to interact with PL/SQL's capabilities effectively.