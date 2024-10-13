Data Manipulation Language (DML) commands in SQL are used to manage and manipulate data stored in database tables. The main DML commands include `INSERT`, `UPDATE`, `DELETE`, and `MERGE`. Below are examples of each command, ranging from basic to advanced usage.

### 1. **INSERT Command**

The `INSERT` command is used to add new rows to a table.

#### Basic Example

```sql
-- Inserting a single row into the employees table
INSERT INTO employees (employee_id, first_name, last_name, hire_date, salary)
VALUES (101, 'John', 'Doe', '2023-01-15', 60000);
```

#### Advanced Example

```sql
-- Inserting multiple rows into the employees table
INSERT INTO employees (employee_id, first_name, last_name, hire_date, salary)
VALUES 
    (102, 'Jane', 'Smith', '2023-01-20', 65000),
    (103, 'Emily', 'Jones', '2023-01-25', 70000),
    (104, 'Michael', 'Brown', '2023-02-01', 72000);
```

### 2. **UPDATE Command**

The `UPDATE` command is used to modify existing records in a table.

#### Basic Example

```sql
-- Updating the salary of a specific employee
UPDATE employees
SET salary = 75000
WHERE employee_id = 101;
```

#### Advanced Example

```sql
-- Updating multiple records based on a condition
UPDATE employees
SET salary = salary * 1.1 -- Increasing salary by 10%
WHERE hire_date < '2023-01-01';
```

### 3. **DELETE Command**

The `DELETE` command is used to remove rows from a table.

#### Basic Example

```sql
-- Deleting a specific employee from the employees table
DELETE FROM employees
WHERE employee_id = 102;
```

#### Advanced Example

```sql
-- Deleting multiple records based on a condition
DELETE FROM employees
WHERE hire_date < '2023-01-01' AND salary < 60000; -- Deletes employees hired before Jan 1, 2023, with a salary less than 60,000
```

### 4. **MERGE Command**

The `MERGE` command is used to perform `INSERT` and `UPDATE` operations in a single statement, typically for synchronizing tables.

#### Basic Example

```sql
-- Merging data from a source table into a target table
MERGE INTO employees e
USING new_employees ne
ON (e.employee_id = ne.employee_id)
WHEN MATCHED THEN
    UPDATE SET e.salary = ne.salary
WHEN NOT MATCHED THEN
    INSERT (employee_id, first_name, last_name, hire_date, salary)
    VALUES (ne.employee_id, ne.first_name, ne.last_name, ne.hire_date, ne.salary);
```

#### Advanced Example

```sql
-- Merging data with complex conditions
MERGE INTO employees e
USING (SELECT employee_id, first_name, last_name, hire_date, salary FROM new_employees) ne
ON (e.employee_id = ne.employee_id)
WHEN MATCHED THEN
    UPDATE SET 
        e.salary = ne.salary,
        e.hire_date = ne.hire_date
WHERE e.salary < ne.salary -- Only update if the new salary is higher
WHEN NOT MATCHED THEN
    INSERT (employee_id, first_name, last_name, hire_date, salary)
    VALUES (ne.employee_id, ne.first_name, ne.last_name, ne.hire_date, ne.salary);
```

### Conclusion

DML commands are essential for managing and manipulating data within a database. The examples provided range from basic to advanced use cases, demonstrating how to insert, update, delete, and merge records in SQL. By mastering these commands, you can efficiently manage the data in your database applications.