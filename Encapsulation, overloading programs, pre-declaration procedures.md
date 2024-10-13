In PL/SQL, packages allow you to encapsulate related functionalities, making your code more organized and reusable. They also support overloading, which enables you to define multiple procedures or functions with the same name but different parameter lists. Pre-declaration of procedures allows you to declare a procedure in the package specification before defining it in the package body.

### 1. Encapsulation

**Encapsulation** is the concept of bundling data and methods that operate on that data within one unit, typically a class or package. In PL/SQL, packages encapsulate procedures, functions, variables, and types, controlling the visibility of these elements.

#### Example of Encapsulation

```sql
-- Create a package to encapsulate employee-related functionalities
CREATE OR REPLACE PACKAGE emp_pkg AS
    -- Public procedure to add an employee
    PROCEDURE add_employee(p_name IN VARCHAR2, p_salary IN NUMBER);
    
    -- Public function to retrieve an employee's salary
    FUNCTION get_employee_salary(p_name IN VARCHAR2) RETURN NUMBER;
    
    -- Private variable to hold employee details
    emp_details SYS.ODCIVARCHAR2LIST;  -- Collection type to store employee names
    emp_salaries SYS.ODCINUMBERLIST;    -- Collection type to store employee salaries
END emp_pkg;
/

-- Package body definition
CREATE OR REPLACE PACKAGE BODY emp_pkg AS
    -- Implementation of add_employee
    PROCEDURE add_employee(p_name IN VARCHAR2, p_salary IN NUMBER) IS
    BEGIN
        emp_details.EXTEND;  -- Extend the collection for the new entry
        emp_details(emp_details.LAST) := p_name;  -- Add employee name
        emp_salaries.EXTEND;  -- Extend the salaries collection
        emp_salaries(emp_salaries.LAST) := p_salary;  -- Add employee salary
        DBMS_OUTPUT.PUT_LINE('Added employee: ' || p_name || ' with salary: ' || p_salary);
    END add_employee;

    -- Implementation of get_employee_salary
    FUNCTION get_employee_salary(p_name IN VARCHAR2) RETURN NUMBER IS
        v_salary NUMBER;
    BEGIN
        FOR i IN 1 .. emp_details.COUNT LOOP
            IF emp_details(i) = p_name THEN
                v_salary := emp_salaries(i);
                RETURN v_salary;  -- Return the salary of the requested employee
            END IF;
        END LOOP;
        RETURN NULL;  -- If employee not found, return NULL
    END get_employee_salary;
END emp_pkg;
/
```

**Usage**:

```sql
BEGIN
    emp_pkg.add_employee('John Doe', 50000);
    emp_pkg.add_employee('Alice Smith', 60000);
    
    DBMS_OUTPUT.PUT_LINE('Salary of John Doe: ' || emp_pkg.get_employee_salary('John Doe'));
END;
/
```

### 2. Overloading Programs

**Overloading** allows you to create multiple procedures or functions with the same name but different parameter lists. This is useful for providing different ways to interact with the same operation.

#### Example of Overloading

```sql
CREATE OR REPLACE PACKAGE math_pkg AS
    -- Overloaded procedures to add two numbers
    PROCEDURE add_numbers(p_num1 IN NUMBER, p_num2 IN NUMBER);
    PROCEDURE add_numbers(p_num1 IN VARCHAR2, p_num2 IN VARCHAR2);
END math_pkg;
/

CREATE OR REPLACE PACKAGE BODY math_pkg AS
    PROCEDURE add_numbers(p_num1 IN NUMBER, p_num2 IN NUMBER) IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('Sum (Number): ' || (p_num1 + p_num2));
    END add_numbers;

    PROCEDURE add_numbers(p_num1 IN VARCHAR2, p_num2 IN VARCHAR2) IS
        v_num1 NUMBER := TO_NUMBER(p_num1);
        v_num2 NUMBER := TO_NUMBER(p_num2);
    BEGIN
        DBMS_OUTPUT.PUT_LINE('Sum (String): ' || (v_num1 + v_num2));
    END add_numbers;
END math_pkg;
/
```

**Usage**:

```sql
BEGIN
    math_pkg.add_numbers(10, 20);              -- Calls the first procedure
    math_pkg.add_numbers('10', '20');          -- Calls the second procedure
END;
/
```

### 3. Pre-declaration Procedures

Pre-declaration allows you to declare a procedure in the package specification before providing its implementation in the package body. This is particularly useful for recursive procedures or when you want to provide a clearer interface.

#### Example of Pre-declaration

```sql
CREATE OR REPLACE PACKAGE calc_pkg AS
    -- Pre-declaration of a procedure for calculating factorial
    FUNCTION factorial(n IN NUMBER) RETURN NUMBER;
END calc_pkg;
/

CREATE OR REPLACE PACKAGE BODY calc_pkg AS
    FUNCTION factorial(n IN NUMBER) RETURN NUMBER IS
    BEGIN
        IF n = 0 THEN
            RETURN 1;  -- Base case
        ELSE
            RETURN n * factorial(n - 1);  -- Recursive case
        END IF;
    END factorial;
END calc_pkg;
/
```

**Usage**:

```sql
DECLARE
    v_result NUMBER;
BEGIN
    v_result := calc_pkg.factorial(5);  -- Calculate factorial of 5
    DBMS_OUTPUT.PUT_LINE('Factorial of 5: ' || v_result);
END;
/
```

### Summary

- **Encapsulation**: Packages allow you to bundle related procedures, functions, and variables together, controlling their visibility and access. 
- **Overloading**: You can define multiple procedures or functions with the same name but different parameter lists to provide flexibility in how you call them.
- **Pre-declaration**: This feature allows you to declare a procedure in the package specification before defining it in the package body, facilitating better organization and clarity.

By using these concepts, you can create more modular, reusable, and maintainable PL/SQL code. The provided examples demonstrate how to implement these features effectively.