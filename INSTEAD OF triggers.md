**INSTEAD OF Triggers** in Oracle PL/SQL are a special type of trigger that allows you to perform operations on **views** that normally would not be possible. These triggers "drive" operations by specifying what to do **instead of** the default action (like `INSERT`, `UPDATE`, or `DELETE`) that cannot be directly applied to a view. This allows you to manipulate the underlying base tables of a view.

Typically, views are read-only, but with **INSTEAD OF** triggers, you can make certain views updatable by redirecting the DML (Data Manipulation Language) operations to the underlying base tables.

### Syntax of INSTEAD OF Triggers

```plsql
CREATE [OR REPLACE] TRIGGER trigger_name
INSTEAD OF INSERT|DELETE|UPDATE [OF column] ON view_name
[FOR EACH ROW]
DECLARE
    -- Optional declarations
BEGIN
    -- Trigger logic here
END;
/
```

### Key Points:
1. **INSTEAD OF** triggers only work on views, not on tables.
2. You can handle `INSERT`, `UPDATE`, and `DELETE` operations for views that are based on multiple tables.
3. **FOR EACH ROW** is typically used when the trigger is to be executed once for each row of the view affected by the DML operation.

---

### Example 1: **Basic INSTEAD OF INSERT Trigger**

Assume you have a view that joins two tables: `employees` and `departments`. Normally, you can't directly `INSERT` into this view, but an **INSTEAD OF** trigger can be used to insert into the underlying base tables.

#### Create the Base Tables:

```plsql
CREATE TABLE employees (
    employee_id NUMBER PRIMARY KEY,
    first_name  VARCHAR2(50),
    last_name   VARCHAR2(50),
    department_id NUMBER
);

CREATE TABLE departments (
    department_id NUMBER PRIMARY KEY,
    department_name VARCHAR2(50)
);
```

#### Create the View:

```plsql
CREATE VIEW emp_dept_view AS
SELECT e.employee_id, e.first_name, e.last_name, d.department_name
FROM employees e
JOIN departments d ON e.department_id = d.department_id;
```

Since this view joins two tables, it is not directly insertable. Now let's create an **INSTEAD OF** trigger for the `INSERT` operation.

#### Create the INSTEAD OF Trigger:

```plsql
CREATE OR REPLACE TRIGGER trg_instead_of_insert_emp_dept
INSTEAD OF INSERT ON emp_dept_view
FOR EACH ROW
BEGIN
    -- Insert data into the employees table
    INSERT INTO employees (employee_id, first_name, last_name, department_id)
    VALUES (:NEW.employee_id, :NEW.first_name, :NEW.last_name, 
        (SELECT department_id FROM departments WHERE department_name = :NEW.department_name));
END;
/
```

#### Explanation:
- The trigger intercepts `INSERT` operations on the view `emp_dept_view`.
- The `:NEW` keyword refers to the new data being inserted.
- The `department_id` is fetched from the `departments` table using the department name from the `INSERT` operation.
- Data is inserted into the `employees` table based on the view's information.

#### Test the Trigger:

```plsql
-- Insert into the view
INSERT INTO emp_dept_view (employee_id, first_name, last_name, department_name)
VALUES (101, 'John', 'Doe', 'HR');

-- Check the base tables
SELECT * FROM employees;
SELECT * FROM departments;
```

---

### Example 2: **INSTEAD OF UPDATE Trigger**

In this example, we create an **INSTEAD OF** trigger to update records in a view that joins two tables (`employees` and `departments`).

#### INSTEAD OF UPDATE Trigger:

```plsql
CREATE OR REPLACE TRIGGER trg_instead_of_update_emp_dept
INSTEAD OF UPDATE ON emp_dept_view
FOR EACH ROW
BEGIN
    -- Update the employees table
    UPDATE employees
    SET first_name = :NEW.first_name,
        last_name = :NEW.last_name,
        department_id = (SELECT department_id FROM departments WHERE department_name = :NEW.department_name)
    WHERE employee_id = :OLD.employee_id;
END;
/
```

#### Explanation:
- The trigger intercepts `UPDATE` operations on the `emp_dept_view` view.
- It updates the `employees` table based on the new values (`:NEW`) from the view.
- The `department_id` is looked up using the department name from the view.

#### Test the Trigger:

```plsql
-- Update the view
UPDATE emp_dept_view
SET first_name = 'Jane', department_name = 'Finance'
WHERE employee_id = 101;

-- Check the base tables
SELECT * FROM employees;
SELECT * FROM departments;
```

---

### Example 3: **INSTEAD OF DELETE Trigger**

This example demonstrates how to use an **INSTEAD OF DELETE** trigger to delete records from a view that is based on multiple base tables.

#### INSTEAD OF DELETE Trigger:

```plsql
CREATE OR REPLACE TRIGGER trg_instead_of_delete_emp_dept
INSTEAD OF DELETE ON emp_dept_view
FOR EACH ROW
BEGIN
    -- Delete from the employees table
    DELETE FROM employees
    WHERE employee_id = :OLD.employee_id;
END;
/
```

#### Explanation:
- The trigger intercepts `DELETE` operations on the view and removes the corresponding record from the `employees` table.

#### Test the Trigger:

```plsql
-- Delete from the view
DELETE FROM emp_dept_view
WHERE employee_id = 101;

-- Check the base tables
SELECT * FROM employees;
SELECT * FROM departments;
```

---

### Example 4: **Advanced INSTEAD OF Trigger with Complex Logic**

In this example, we handle an **INSTEAD OF UPDATE** trigger that performs a more complex validation and business logic before performing the actual update on the base tables.

#### INSTEAD OF UPDATE Trigger with Business Logic:

```plsql
CREATE OR REPLACE TRIGGER trg_instead_of_update_emp_dept_complex
INSTEAD OF UPDATE ON emp_dept_view
FOR EACH ROW
BEGIN
    -- Validate if the new department exists
    IF NOT EXISTS (SELECT 1 FROM departments WHERE department_name = :NEW.department_name) THEN
        RAISE_APPLICATION_ERROR(-20001, 'Department does not exist.');
    END IF;

    -- Update the employees table
    UPDATE employees
    SET first_name = :NEW.first_name,
        last_name = :NEW.last_name,
        department_id = (SELECT department_id FROM departments WHERE department_name = :NEW.department_name)
    WHERE employee_id = :OLD.employee_id;
END;
/
```

#### Explanation:
- The trigger checks if the new department exists before updating the employee.
- If the department does not exist, it raises a custom error using `RAISE_APPLICATION_ERROR`.

#### Test the Trigger:

```plsql
-- Try to update with a non-existent department
UPDATE emp_dept_view
SET department_name = 'NonExistentDept'
WHERE employee_id = 101;

-- Check the base tables
SELECT * FROM employees;
```

---

### Conclusion:

**INSTEAD OF** triggers provide a powerful way to make views updatable by redirecting DML operations to the base tables. They are particularly useful when dealing with complex views that span multiple tables. The examples above range from simple DML operations (`INSERT`, `UPDATE`, and `DELETE`) to more advanced logic like business validations and conditional triggers.

By understanding and using **INSTEAD OF** triggers, you can design more flexible and powerful database applications that allow for DML operations on otherwise read-only views.