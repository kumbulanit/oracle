Parameterization in PL/SQL refers to the practice of using parameters in procedures, functions, and cursors to make them more flexible and reusable. By defining parameters, you can pass different values to these objects at runtime, allowing for more dynamic and versatile code.

### Basic Concepts of Parameterization

1. **Parameters**: These are variables that allow you to pass values into procedures, functions, and cursors. Parameters can be categorized into:
   - **IN**: This type of parameter is used to pass values into the subprogram. It is read-only.
   - **OUT**: This type allows the subprogram to return a value back to the caller. It is write-only.
   - **IN OUT**: This allows both reading and writing. The initial value is passed in, and it can be modified.

2. **Syntax**: When defining a procedure or function, you specify parameters within parentheses in the header. The syntax is as follows:

   ```sql
   CREATE OR REPLACE PROCEDURE procedure_name(parameter_name data_type) IS
   BEGIN
       -- Procedure logic
   END;
   ```

### Basic Example of Parameterization

#### Example 1: Simple Procedure with IN Parameter

```sql
CREATE OR REPLACE PROCEDURE greet_employee(p_employee_id IN NUMBER) IS
    v_first_name VARCHAR2(50);
BEGIN
    -- Select the first name of the employee based on the employee_id
    SELECT first_name INTO v_first_name
    FROM employees
    WHERE employee_id = p_employee_id;

    DBMS_OUTPUT.PUT_LINE('Hello, ' || v_first_name || '!');
END;
```

**Explanation**:
- **Procedure**: The procedure `greet_employee` accepts an employee ID as an input parameter.
- **SELECT INTO**: It fetches the first name of the employee and prints a greeting message.

#### Executing the Procedure

To call this procedure:

```sql
BEGIN
    greet_employee(1);  -- Replace 1 with the actual employee ID
END;
```

### Intermediate Example of Parameterization

#### Example 2: Procedure with OUT Parameter

```sql
CREATE OR REPLACE PROCEDURE get_employee_salary(
    p_employee_id IN NUMBER,
    p_salary OUT NUMBER
) IS
BEGIN
    SELECT salary INTO p_salary
    FROM employees
    WHERE employee_id = p_employee_id;
END;
```

**Explanation**:
- **OUT Parameter**: The `get_employee_salary` procedure takes an employee ID as an input and returns the salary through the `p_salary` output parameter.

#### Executing the Procedure with OUT Parameter

To call this procedure and retrieve the salary:

```sql
DECLARE
    v_salary NUMBER;
BEGIN
    get_employee_salary(1, v_salary);  -- Replace 1 with the actual employee ID
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_salary);
END;
```

### Advanced Example of Parameterization

#### Example 3: Procedure with IN OUT Parameter

```sql
CREATE OR REPLACE PROCEDURE update_employee_salary(
    p_employee_id IN NUMBER,
    p_new_salary IN OUT NUMBER
) IS
BEGIN
    UPDATE employees
    SET salary = p_new_salary
    WHERE employee_id = p_employee_id;

    -- Increase the salary by 10%
    p_new_salary := p_new_salary * 1.10;
END;
```

**Explanation**:
- **IN OUT Parameter**: The `update_employee_salary` procedure takes an employee ID as input and allows modifying the salary value directly.
- After updating the salary, it increases the new salary by 10%.

#### Executing the Procedure with IN OUT Parameter

To call this procedure and observe the changes:

```sql
DECLARE
    v_new_salary NUMBER := 50000;  -- Initial salary
BEGIN
    update_employee_salary(1, v_new_salary);  -- Replace 1 with the actual employee ID
    DBMS_OUTPUT.PUT_LINE('New Salary: ' || v_new_salary);
END;
```

### Advanced Example with Cursors and Parameters

#### Example 4: Parameterized Cursor

You can also create parameterized cursors to fetch data dynamically based on input parameters.

```sql
DECLARE
    CURSOR emp_cursor (p_department_id NUMBER) IS
        SELECT employee_id, first_name, last_name
        FROM employees
        WHERE department_id = p_department_id;

    v_employee_id NUMBER;
    v_first_name VARCHAR2(50);
    v_last_name VARCHAR2(50);
BEGIN
    OPEN emp_cursor(10);  -- Replace 10 with the desired department ID

    LOOP
        FETCH emp_cursor INTO v_employee_id, v_first_name, v_last_name;
        EXIT WHEN emp_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_employee_id || ': ' || v_first_name || ' ' || v_last_name);
    END LOOP;

    CLOSE emp_cursor;
END;
```

**Explanation**:
- **Parameterized Cursor**: The cursor `emp_cursor` accepts a department ID and fetches employees from that department.
- **Loop**: It iterates through the results and prints each employee's details.

### Conclusion

- **Parameterization** in PL/SQL allows for more flexible and reusable code by passing parameters to procedures, functions, and cursors.
- **Basic examples** demonstrated simple input, output, and in-out parameters.
- **Intermediate examples** showcased how to retrieve data using output parameters.
- **Advanced examples** included using in-out parameters and parameterized cursors for dynamic queries.

Parameterization is a crucial feature that enhances code readability, maintainability, and reusability, making it a fundamental concept in PL/SQL programming.