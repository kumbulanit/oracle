In PL/SQL, **procedures** and **functions** are subprograms that allow you to encapsulate and reuse code. Both are defined using the `CREATE PROCEDURE` and `CREATE FUNCTION` statements, respectively. The key difference is that functions return a single value, while procedures do not return a value but can return multiple values through parameters.

### Basic Concepts

- **Procedure**: A named PL/SQL block that can perform actions (like inserting, updating, or deleting records) but does not return a value.
- **Function**: A named PL/SQL block that performs a calculation or action and returns a single value.

### Basic Example of a Procedure

Here's a simple example that demonstrates creating and using a procedure.

#### Example 1: Simple Procedure

```sql
CREATE OR REPLACE PROCEDURE greet_employee(p_employee_id IN NUMBER) IS
    v_employee_name employees.first_name%TYPE;
BEGIN
    SELECT first_name INTO v_employee_name 
    FROM employees 
    WHERE employee_id = p_employee_id;

    DBMS_OUTPUT.PUT_LINE('Hello, ' || v_employee_name || '!');
END;
```

**Explanation:**
- **Procedure Name**: `greet_employee`
- **Parameter**: `p_employee_id` is an input parameter of type `NUMBER`.
- **Variable Declaration**: `v_employee_name` stores the employee's first name.
- **SELECT INTO**: Retrieves the employee's first name based on the provided `employee_id`.
- **Output**: Uses `DBMS_OUTPUT.PUT_LINE` to print a greeting.

#### Executing the Procedure

To call the procedure, you can use an anonymous PL/SQL block:

```sql
BEGIN
    greet_employee(1);  -- Assuming employee_id 1 exists
END;
```

### Basic Example of a Function

Now let's create a simple function that calculates and returns an employee's salary after a bonus.

#### Example 2: Simple Function

```sql
CREATE OR REPLACE FUNCTION calculate_bonus(p_employee_id IN NUMBER) RETURN NUMBER IS
    v_salary employees.salary%TYPE;
    v_bonus NUMBER;
BEGIN
    SELECT salary INTO v_salary 
    FROM employees 
    WHERE employee_id = p_employee_id;

    v_bonus := v_salary * 0.10;  -- Calculate 10% bonus

    RETURN v_bonus;  -- Return the calculated bonus
END;
```

**Explanation:**
- **Function Name**: `calculate_bonus`
- **Parameter**: `p_employee_id` is an input parameter.
- **Return Type**: `RETURN NUMBER` indicates that the function returns a number.
- **Variable Declaration**: `v_salary` and `v_bonus` store the salary and calculated bonus, respectively.
- **SELECT INTO**: Retrieves the salary for the given employee.
- **Calculation**: Computes the bonus as 10% of the salary.
- **Return**: Uses the `RETURN` statement to send back the bonus value.

#### Using the Function

To call the function, you can include it in a query or another PL/SQL block:

```sql
DECLARE
    v_bonus_amount NUMBER;
BEGIN
    v_bonus_amount := calculate_bonus(1);  -- Assuming employee_id 1 exists
    DBMS_OUTPUT.PUT_LINE('Bonus Amount: ' || v_bonus_amount);
END;
```

### Intermediate Example of a Procedure with Multiple Parameters

Now, let's create a procedure that updates an employee's salary and returns a confirmation message.

#### Example 3: Procedure with Output Parameter

```sql
CREATE OR REPLACE PROCEDURE update_salary(
    p_employee_id IN NUMBER,
    p_new_salary IN NUMBER,
    p_confirmation OUT VARCHAR2
) IS
BEGIN
    UPDATE employees
    SET salary = p_new_salary
    WHERE employee_id = p_employee_id;

    p_confirmation := 'Salary updated for Employee ID: ' || p_employee_id; 
    COMMIT;  -- Commit the changes
END;
```

**Explanation:**
- **Procedure Name**: `update_salary`
- **Parameters**: 
  - `p_employee_id` (IN): The ID of the employee whose salary is to be updated.
  - `p_new_salary` (IN): The new salary amount.
  - `p_confirmation` (OUT): An output parameter to return a confirmation message.
- **UPDATE Statement**: Changes the salary for the specified employee.
- **Confirmation Message**: Sets the output parameter to a message confirming the update.
- **COMMIT**: Saves the changes to the database.

#### Calling the Procedure with an Output Parameter

```sql
DECLARE
    v_message VARCHAR2(100);
BEGIN
    update_salary(1, 75000, v_message);  -- Update employee ID 1's salary
    DBMS_OUTPUT.PUT_LINE(v_message);  -- Output confirmation message
END;
```

### Advanced Example of a Function with Exception Handling

Let’s create a more advanced function that includes error handling.

#### Example 4: Function with Error Handling

```sql
CREATE OR REPLACE FUNCTION get_employee_salary(p_employee_id IN NUMBER) RETURN NUMBER IS
    v_salary employees.salary%TYPE;
BEGIN
    SELECT salary INTO v_salary 
    FROM employees 
    WHERE employee_id = p_employee_id;

    RETURN v_salary;  -- Return the salary
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Employee not found!');
        RETURN NULL;  -- Return NULL if employee is not found
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('An error occurred: ' || SQLERRM);
        RETURN NULL;  -- Handle any other exceptions
END;
```

**Explanation:**
- **Function Name**: `get_employee_salary`
- **Error Handling**:
  - `NO_DATA_FOUND`: Catches the exception if no employee is found and returns `NULL`.
  - `OTHERS`: Catches any other exceptions and outputs an error message.

#### Using the Advanced Function

```sql
DECLARE
    v_salary NUMBER;
BEGIN
    v_salary := get_employee_salary(999);  -- Assuming employee ID 999 does not exist
    IF v_salary IS NULL THEN
        DBMS_OUTPUT.PUT_LINE('Salary not available.');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Salary: ' || v_salary);
    END IF;
END;
```

### Summary

- **Procedures** and **functions** allow you to encapsulate and reuse code.
- **Procedures** can perform actions and return multiple values via output parameters.
- **Functions** perform calculations or actions and return a single value.
- Error handling can be implemented in functions to manage exceptions gracefully.

These examples provide a comprehensive understanding of procedures and functions in PL/SQL, progressing from basic to advanced concepts with detailed explanations. You can adapt these patterns to suit your specific business logic and requirements.