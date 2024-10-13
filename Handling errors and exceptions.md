Handling errors and exceptions in PL/SQL is crucial for developing robust and reliable applications. PL/SQL provides mechanisms to catch and manage exceptions that occur during the execution of code, allowing developers to gracefully handle unexpected situations rather than having the program crash. Below, we will explore the concepts of error handling and exceptions in detail, along with examples ranging from basic to advanced.

### 1. Types of Exceptions in PL/SQL

#### 1.1 Predefined Exceptions

These are standard exceptions defined by PL/SQL, such as:

- `NO_DATA_FOUND`: Raised when a SELECT INTO statement returns no rows.
- `TOO_MANY_ROWS`: Raised when a SELECT INTO statement returns more than one row.
- `ZERO_DIVIDE`: Raised when there is an attempt to divide by zero.
- `INVALID_NUMBER`: Raised when there is an attempt to convert a string to a number and it fails.

#### 1.2 User-Defined Exceptions

You can define your own exceptions to handle specific situations in your application.

### 2. Basic Exception Handling

PL/SQL uses the `BEGIN ... EXCEPTION ... END;` block to handle exceptions. Here’s a simple example:

#### Example 1: Basic Exception Handling

```sql
DECLARE
    v_number NUMBER := 10;
    v_result NUMBER;
BEGIN
    v_result := v_number / 0;  -- This will cause a divide by zero error
    DBMS_OUTPUT.PUT_LINE('Result: ' || v_result);
EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE('Error: Division by zero is not allowed.');
END;
```

**Output**:
```
Error: Division by zero is not allowed.
```

**Explanation**: In this example, the `ZERO_DIVIDE` exception is caught in the `EXCEPTION` block, and a friendly message is displayed.

### 3. Handling Multiple Exceptions

You can handle multiple exceptions in a single `EXCEPTION` block.

#### Example 2: Handling Multiple Exceptions

```sql
DECLARE
    v_number1 NUMBER := 10;
    v_number2 NUMBER := 0;  -- This will cause a divide by zero error
    v_result  NUMBER;
BEGIN
    v_result := v_number1 / v_number2;  -- Attempt to divide by zero
    DBMS_OUTPUT.PUT_LINE('Result: ' || v_result);
EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE('Error: Division by zero.');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
```

**Output**:
```
Error: Division by zero.
```

**Explanation**: The `ZERO_DIVIDE` exception is explicitly caught, and an `OTHERS` clause is included to catch any other unexpected exceptions. The `SQLERRM` function retrieves the error message associated with the exception.

### 4. Raising Exceptions

You can raise exceptions explicitly using the `RAISE` statement.

#### Example 3: Raising User-Defined Exceptions

```sql
DECLARE
    my_exception EXCEPTION;  -- Define a user-defined exception
    v_age NUMBER := 15;
BEGIN
    IF v_age < 18 THEN
        RAISE my_exception;  -- Raise the user-defined exception
    END IF;
EXCEPTION
    WHEN my_exception THEN
        DBMS_OUTPUT.PUT_LINE('Error: Age must be at least 18.');
END;
```

**Output**:
```
Error: Age must be at least 18.
```

**Explanation**: In this example, a user-defined exception `my_exception` is raised when the age is less than 18.

### 5. Using the RAISE_APPLICATION_ERROR Procedure

You can use the `RAISE_APPLICATION_ERROR` procedure to generate user-defined error messages.

#### Example 4: Using RAISE_APPLICATION_ERROR

```sql
DECLARE
    v_salary NUMBER := -1000;  -- Invalid salary
BEGIN
    IF v_salary < 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Error: Salary cannot be negative.');  -- Raise custom error
    END IF;
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE(SQLERRM);  -- Display custom error message
END;
```

**Output**:
```
Error: Salary cannot be negative.
```

**Explanation**: Here, if the salary is negative, a custom error message is raised using `RAISE_APPLICATION_ERROR`, and the error is caught in the `EXCEPTION` block.

### 6. Advanced Exception Handling with Stored Procedures

#### Example 5: Exception Handling in Stored Procedures

```sql
CREATE OR REPLACE PROCEDURE calculate_bonus(p_employee_id IN NUMBER) IS
    v_bonus NUMBER;
BEGIN
    SELECT salary * 0.10 INTO v_bonus FROM employees WHERE employee_id = p_employee_id;

    -- Simulate an error if no rows are found
    IF v_bonus IS NULL THEN
        RAISE NO_DATA_FOUND; 
    END IF;

    DBMS_OUTPUT.PUT_LINE('Bonus for employee ' || p_employee_id || ': ' || v_bonus);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Error: No employee found with ID ' || p_employee_id);
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An unexpected error occurred: ' || SQLERRM);
END;
```

#### Executing the Procedure

```sql
BEGIN
    calculate_bonus(9999);  -- Non-existent employee ID
END;
```

**Output**:
```
Error: No employee found with ID 9999
```

**Explanation**: In this stored procedure, if no employee is found with the given ID, a `NO_DATA_FOUND` exception is raised. The procedure handles this gracefully by providing a relevant message.

### Conclusion

- **Predefined Exceptions**: PL/SQL has built-in exceptions for common error scenarios.
- **User-Defined Exceptions**: You can define your own exceptions for specific application needs.
- **RAISE Statement**: Use the `RAISE` statement to trigger exceptions explicitly.
- **RAISE_APPLICATION_ERROR**: This allows for the generation of custom error messages.
- **Stored Procedures**: Exception handling can be implemented within stored procedures, ensuring better error management in complex applications.

By effectively managing errors and exceptions, you can create resilient PL/SQL programs that provide meaningful feedback to users and maintain data integrity.