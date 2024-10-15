DML (Data Manipulation Language) triggers in Oracle PL/SQL are special kinds of triggers that execute automatically in response to DML events such as **INSERT**, **UPDATE**, or **DELETE** operations on a table or view. Triggers are typically used to enforce business rules, maintain data integrity, audit changes, or trigger additional events.

Here are examples of DML triggers ranging from basic to advanced use cases:

### 1. **Basic `BEFORE INSERT` Trigger**

This trigger runs before a new record is inserted into the `employees` table. It ensures that a default value for the `hire_date` column is set if none is provided during the insert.

```plsql
CREATE OR REPLACE TRIGGER trg_before_insert_employees
BEFORE INSERT ON employees
FOR EACH ROW
BEGIN
    IF :NEW.hire_date IS NULL THEN
        :NEW.hire_date := SYSDATE;
    END IF;
END;
/
```

**Explanation**:
- `BEFORE INSERT`: The trigger runs before the `INSERT` statement.
- `:NEW`: Refers to the new data being inserted into the table.
- `hire_date`: If no `hire_date` is provided, the trigger automatically sets the current date (`SYSDATE`).

### 2. **Basic `AFTER UPDATE` Trigger**

This trigger records changes to the salary column in an `employee_changes` table whenever an employee's salary is updated.

```plsql
CREATE OR REPLACE TRIGGER trg_after_update_salary
AFTER UPDATE OF salary ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employee_changes (employee_id, old_salary, new_salary, change_date)
    VALUES (:OLD.employee_id, :OLD.salary, :NEW.salary, SYSDATE);
END;
/
```

**Explanation**:
- `AFTER UPDATE OF salary`: The trigger runs after an update to the `salary` column.
- `:OLD`: Refers to the old value of the row before the update.
- `:NEW`: Refers to the new value of the row after the update.
- The trigger logs the `employee_id`, old salary, new salary, and the change date into a separate table for auditing.

### 3. **Intermediate `BEFORE DELETE` Trigger**

This trigger prevents deletion of an employee record if that employee is a manager, by checking if the employee has subordinates in the `employees` table.

```plsql
CREATE OR REPLACE TRIGGER trg_before_delete_employees
BEFORE DELETE ON employees
FOR EACH ROW
DECLARE
    subordinate_count NUMBER;
BEGIN
    SELECT COUNT(*)
    INTO subordinate_count
    FROM employees
    WHERE manager_id = :OLD.employee_id;

    IF subordinate_count > 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Cannot delete employee with subordinates.');
    END IF;
END;
/
```

**Explanation**:
- The trigger checks if the employee being deleted has subordinates.
- `RAISE_APPLICATION_ERROR`: Raises a custom error to prevent deletion.
- If the employee has subordinates (manager), the trigger raises an error and prevents the deletion.

### 4. **Advanced `BEFORE INSERT OR UPDATE` Trigger for Data Validation**

This trigger validates the `salary` before inserting or updating the `employees` table. If the salary is below a certain threshold, the trigger raises an error.

```plsql
CREATE OR REPLACE TRIGGER trg_salary_check
BEFORE INSERT OR UPDATE OF salary ON employees
FOR EACH ROW
BEGIN
    IF :NEW.salary < 30000 THEN
        RAISE_APPLICATION_ERROR(-20002, 'Salary must be at least 30,000.');
    END IF;
END;
/
```

**Explanation**:
- `BEFORE INSERT OR UPDATE`: The trigger runs before both insert and update operations on the `salary` column.
- If the new salary is less than `30,000`, the trigger raises an error to prevent the operation.

### 5. **Advanced `AFTER INSERT OR UPDATE` Trigger for Auditing**

This trigger tracks changes made to the `employees` table, recording the user who made the change, the old and new values, and the type of operation (insert or update).

```plsql
CREATE OR REPLACE TRIGGER trg_audit_employee_changes
AFTER INSERT OR UPDATE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO audit_employee (employee_id, operation, old_salary, new_salary, changed_by, change_date)
    VALUES (:NEW.employee_id,
            CASE 
                WHEN INSERTING THEN 'INSERT'
                WHEN UPDATING THEN 'UPDATE'
            END,
            :OLD.salary,
            :NEW.salary,
            USER,
            SYSDATE);
END;
/
```

**Explanation**:
- `INSERTING` and `UPDATING`: These are conditional checks inside the trigger to identify whether the operation is an `INSERT` or `UPDATE`.
- The trigger stores details of the change in the `audit_employee` table, including the old and new salary, the user who made the change (`USER`), and the date (`SYSDATE`).

### 6. **Advanced Compound DML Trigger for Complex Logic**

This compound trigger is used when multiple events are triggered by an `INSERT`, `UPDATE`, or `DELETE`. It handles logic across different DML events like `BEFORE`, `AFTER`, `INSTEAD OF` in a single trigger structure.

```plsql
CREATE OR REPLACE TRIGGER trg_employee_compound
FOR INSERT OR UPDATE OR DELETE ON employees
COMPOUND TRIGGER

    -- Variables to use across trigger events
    TYPE employee_tab IS TABLE OF employees%ROWTYPE;
    employee_changes employee_tab := employee_tab();

    BEFORE EACH ROW IS
    BEGIN
        -- Logic before each DML event
        IF INSERTING THEN
            DBMS_OUTPUT.PUT_LINE('Before Insert');
        ELSIF UPDATING THEN
            DBMS_OUTPUT.PUT_LINE('Before Update');
        ELSIF DELETING THEN
            DBMS_OUTPUT.PUT_LINE('Before Delete');
        END IF;
    END BEFORE EACH ROW;

    AFTER EACH ROW IS
    BEGIN
        -- Logic after each DML event
        IF INSERTING OR UPDATING THEN
            employee_changes.EXTEND;
            employee_changes(employee_changes.COUNT) := :NEW;
        END IF;
    END AFTER EACH ROW;

    AFTER STATEMENT IS
    BEGIN
        -- Logic to be executed after the statement
        DBMS_OUTPUT.PUT_LINE('After Statement: ' || employee_changes.COUNT || ' changes made.');
    END AFTER STATEMENT;

END trg_employee_compound;
/
```

**Explanation**:
- The **compound trigger** allows handling multiple trigger timings (`BEFORE EACH ROW`, `AFTER EACH ROW`, `AFTER STATEMENT`) in one block.
- It stores and manipulates data across different stages of the trigger.

---

### Conclusion:
DML triggers provide powerful mechanisms for enforcing rules, maintaining data integrity, auditing changes, and implementing complex business logic in Oracle PL/SQL. Starting with basic examples like `BEFORE INSERT` and `AFTER UPDATE`, you can build advanced scenarios using compound triggers and error handling. Triggers should be used judiciously to avoid performance bottlenecks, especially in high-transaction environments.