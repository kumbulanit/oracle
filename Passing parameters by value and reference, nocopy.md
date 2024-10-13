In PL/SQL, parameters can be passed to procedures and functions in two ways: **by value** and **by reference**. Understanding these two methods is crucial for optimizing performance and managing memory effectively. 

Additionally, PL/SQL provides the `NOCOPY` hint to enhance performance when passing large data structures, especially in `OUT` and `IN OUT` parameters. This guide will cover these concepts in detail, including examples from basic to advanced.

### 1. Passing Parameters by Value

**Definition**: When parameters are passed by value, a copy of the actual parameter's value is made in memory. This means any modifications to the parameter within the subprogram do not affect the original variable.

**Example**: 

#### Basic Example of Passing by Value

```sql
CREATE OR REPLACE PROCEDURE update_salary_by_value(
    p_salary IN NUMBER
) IS
BEGIN
    -- Increase the salary by 10%
    p_salary := p_salary * 1.10;  -- This will not affect the original salary
    DBMS_OUTPUT.PUT_LINE('Updated Salary inside procedure: ' || p_salary);
END;
```

#### Executing the Procedure

```sql
DECLARE
    v_salary NUMBER := 50000;
BEGIN
    update_salary_by_value(v_salary);
    DBMS_OUTPUT.PUT_LINE('Original Salary after procedure call: ' || v_salary);
END;
```

**Output**:
```
Updated Salary inside procedure: 55000
Original Salary after procedure call: 50000
```

**Explanation**: In this example, the `v_salary` variable remains unchanged after the procedure call, demonstrating the behavior of passing parameters by value.

### 2. Passing Parameters by Reference

**Definition**: When parameters are passed by reference, a reference (or pointer) to the actual parameter is passed to the subprogram. This means modifications to the parameter within the subprogram will affect the original variable.

**Example**: 

#### Basic Example of Passing by Reference

```sql
CREATE OR REPLACE PROCEDURE update_salary_by_reference(
    p_salary IN OUT NUMBER
) IS
BEGIN
    -- Increase the salary by 10%
    p_salary := p_salary * 1.10;  -- This will affect the original salary
    DBMS_OUTPUT.PUT_LINE('Updated Salary inside procedure: ' || p_salary);
END;
```

#### Executing the Procedure

```sql
DECLARE
    v_salary NUMBER := 50000;
BEGIN
    update_salary_by_reference(v_salary);
    DBMS_OUTPUT.PUT_LINE('Original Salary after procedure call: ' || v_salary);
END;
```

**Output**:
```
Updated Salary inside procedure: 55000
Original Salary after procedure call: 55000
```

**Explanation**: In this case, the original `v_salary` variable is updated because it was passed by reference.

### 3. NOCOPY Hint

The `NOCOPY` hint is used to improve performance when passing large data structures, especially for `OUT` and `IN OUT` parameters. By default, PL/SQL creates a copy of the parameter data when passing it to a subprogram. The `NOCOPY` hint avoids this copy, potentially enhancing performance.

#### Example with NOCOPY

```sql
CREATE OR REPLACE PROCEDURE update_employee_salary_nocopy(
    p_employee_id IN NUMBER,
    p_salary IN OUT NOCOPY NUMBER
) IS
BEGIN
    -- Update the salary directly
    p_salary := p_salary * 1.10;  -- This will affect the original salary
    DBMS_OUTPUT.PUT_LINE('Updated Salary inside procedure: ' || p_salary);
END;
```

#### Executing the Procedure

```sql
DECLARE
    v_salary NUMBER := 50000;
BEGIN
    update_employee_salary_nocopy(1, v_salary);
    DBMS_OUTPUT.PUT_LINE('Original Salary after procedure call: ' || v_salary);
END;
```

**Output**:
```
Updated Salary inside procedure: 55000
Original Salary after procedure call: 55000
```

**Explanation**: The `NOCOPY` hint allows direct access to the original `v_salary` variable without creating a copy, which can improve performance, especially for large data structures.

### 4. Advanced Example: Using Records with NOCOPY

In more complex scenarios, you may want to use records with `NOCOPY`. Here’s how you can do that:

#### Creating a Record Type

```sql
DECLARE
    TYPE employee_record IS RECORD (
        employee_id NUMBER,
        first_name  VARCHAR2(50),
        salary      NUMBER
    );

    v_employee employee_record;
    
    -- Procedure that uses NOCOPY with a record type
    PROCEDURE update_employee_details(
        p_emp_details IN OUT NOCOPY employee_record
    ) IS
    BEGIN
        p_emp_details.salary := p_emp_details.salary * 1.10;  -- Update salary
        DBMS_OUTPUT.PUT_LINE('Updated Salary: ' || p_emp_details.salary);
    END;
BEGIN
    -- Initialize record
    v_employee.employee_id := 1;
    v_employee.first_name := 'John';
    v_employee.salary := 50000;

    -- Call procedure
    update_employee_details(v_employee);
    DBMS_OUTPUT.PUT_LINE('Original Salary after procedure call: ' || v_employee.salary);
END;
```

**Output**:
```
Updated Salary: 55000
Original Salary after procedure call: 55000
```

**Explanation**: In this example, a record type `employee_record` is defined, and the procedure `update_employee_details` takes this record as a parameter using the `NOCOPY` hint. This allows modifications to the record directly, without the overhead of copying the data.

### Conclusion

- **Passing by Value**: A copy of the parameter is made; changes do not affect the original variable.
- **Passing by Reference**: A reference to the original variable is passed; changes affect the original variable.
- **NOCOPY Hint**: Used to improve performance when passing large structures by avoiding unnecessary copying.
- **Advanced Usage**: You can use `NOCOPY` with complex data types like records to enhance performance further.

Understanding how to pass parameters efficiently in PL/SQL is crucial for developing optimized applications. Using the right method depending on the context can lead to better performance and more maintainable code.