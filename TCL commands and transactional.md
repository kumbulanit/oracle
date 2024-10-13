**Transaction Control Language (TCL)** is a subset of SQL commands used to manage transactions in a database. These commands ensure that all operations in a transaction are completed successfully or rolled back in case of an error. The primary TCL commands are **COMMIT**, **ROLLBACK**, and **SAVEPOINT**.

### Key TCL Commands

1. **COMMIT**: This command saves all changes made during the current transaction. Once committed, the changes are permanent and visible to other users.

2. **ROLLBACK**: This command undoes all changes made during the current transaction. It is used when an error occurs or if you decide not to save the changes.

3. **SAVEPOINT**: This command creates a point within a transaction to which you can later roll back. It allows for partial rollback instead of rolling back the entire transaction.

### Basic Examples

#### 1. COMMIT Example

```sql
BEGIN;

INSERT INTO employees (employee_id, first_name, last_name, hire_date, salary)
VALUES (1, 'John', 'Doe', '2023-10-01', 50000);

COMMIT;  -- Commits the transaction, making the insertion permanent
```

**Explanation:**
- In this example, we start a transaction with `BEGIN`.
- An employee record is inserted into the `employees` table.
- The `COMMIT` command is executed, making the changes permanent.

#### 2. ROLLBACK Example

```sql
BEGIN;

INSERT INTO employees (employee_id, first_name, last_name, hire_date, salary)
VALUES (2, 'Jane', 'Smith', '2023-10-02', 55000);

-- An error occurs, so we decide to roll back
ROLLBACK;  -- All changes made in this transaction are undone
```

**Explanation:**
- A transaction begins, and a new employee record is inserted.
- If an error occurs (not shown in this snippet), the `ROLLBACK` command undoes any changes made during the transaction, so the record is not inserted.

### Advanced Examples

#### 1. SAVEPOINT Example

```sql
BEGIN;

INSERT INTO employees (employee_id, first_name, last_name, hire_date, salary)
VALUES (3, 'Alice', 'Johnson', '2023-10-03', 60000);

SAVEPOINT sp1;  -- Create a savepoint after the first insertion

INSERT INTO employees (employee_id, first_name, last_name, hire_date, salary)
VALUES (4, 'Bob', 'Williams', '2023-10-04', 62000);

-- Oops! We want to rollback to the savepoint after Alice's insertion
ROLLBACK TO sp1;  -- Rollback to the savepoint; Bob's insertion is undone

COMMIT;  -- Commit the transaction; only Alice's record is saved
```

**Explanation:**
- A transaction begins, and an employee record for Alice is inserted.
- A `SAVEPOINT` named `sp1` is created after Alice's insertion.
- Another employee record for Bob is inserted.
- If we decide that Bob's record should not be saved, we can `ROLLBACK TO sp1`, which undoes Bob's insertion but retains Alice's.
- Finally, we `COMMIT` the transaction, making Alice's record permanent.

#### 2. Complex Transaction Example

```sql
BEGIN;

-- Insert employees
INSERT INTO employees (employee_id, first_name, last_name, hire_date, salary)
VALUES (5, 'Charlie', 'Brown', '2023-10-05', 70000);

SAVEPOINT sp2;  -- Create a savepoint after Charlie's insertion

INSERT INTO employees (employee_id, first_name, last_name, hire_date, salary)
VALUES (6, 'Diana', 'Prince', '2023-10-06', 72000);

-- Simulating an error
IF 1 = 1 THEN  -- This condition is just for simulation; replace with actual error check
    ROLLBACK TO sp2;  -- Rollback to Charlie's insertion
ELSE
    COMMIT;  -- If no error, commit the transaction
END IF;

COMMIT;  -- Commit the transaction if it reached this point
```

**Explanation:**
- A transaction begins, and an employee record for Charlie is inserted.
- A savepoint `sp2` is created after Charlie's insertion.
- A new employee record for Diana is inserted.
- An error simulation occurs; if the error condition is met, we `ROLLBACK TO sp2`, keeping only Charlie's record.
- If no error occurs, the transaction can be committed, saving all changes.

### Summary of Transaction Control

- **Atomicity**: Ensures that all operations within a transaction are treated as a single unit. If one part fails, the entire transaction fails, and changes are rolled back.
- **Consistency**: Guarantees that the database transitions from one valid state to another. Any transaction will bring the database from one valid state to another.
- **Isolation**: Ensures that transactions are isolated from each other until they are committed, which prevents data inconsistency.
- **Durability**: Once a transaction is committed, it remains so even in the event of a system failure.

Understanding and effectively using TCL commands helps maintain data integrity and consistency in database applications, especially when multiple operations are involved in a single transaction.