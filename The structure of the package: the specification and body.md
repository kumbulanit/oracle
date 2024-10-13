In PL/SQL, a **package** is a database object that groups related PL/SQL types, variables, procedures, and functions together. It helps in organizing and encapsulating the application logic. A package consists of two main parts:

1. **Specification**: This is the interface of the package, where you declare the public procedures, functions, types, and variables.
2. **Body**: This is the implementation part of the package, where you define the logic for the procedures and functions declared in the specification.

### Structure of a PL/SQL Package

#### 1. Package Specification

The package specification declares the types, variables, constants, exceptions, procedures, and functions that can be accessed from outside the package.

#### 2. Package Body

The package body contains the actual implementation of the procedures and functions declared in the specification. It can also include private procedures and functions that are only accessible within the package.

### Example of a PL/SQL Package

Let's create a package that manages employee records. We will define a package specification and a package body.

#### Package Specification

```sql
CREATE OR REPLACE PACKAGE emp_pkg AS
    -- Public types
    TYPE emp_rec IS RECORD (
        employee_id   NUMBER,
        first_name    VARCHAR2(50),
        last_name     VARCHAR2(50),
        salary        NUMBER
    );

    -- Public procedure to add a new employee
    PROCEDURE add_employee(
        p_first_name IN VARCHAR2,
        p_last_name  IN VARCHAR2,
        p_salary     IN NUMBER
    );

    -- Public function to get an employee's details
    FUNCTION get_employee(p_employee_id IN NUMBER) RETURN emp_rec;

    -- Public procedure to update an employee's salary
    PROCEDURE update_salary(
        p_employee_id IN NUMBER,
        p_new_salary  IN NUMBER
    );
END emp_pkg;
/
```

**Explanation**:
- The `emp_pkg` package specification declares a record type `emp_rec` for storing employee details.
- It declares three public methods:
  - `add_employee`: A procedure to add a new employee.
  - `get_employee`: A function to retrieve an employee's details.
  - `update_salary`: A procedure to update an employee's salary.

#### Package Body

```sql
CREATE OR REPLACE PACKAGE BODY emp_pkg AS
    -- Internal storage for employee data
    emp_table SYS.ODCIVARCHAR2LIST;  -- This will be used to store employee names for demonstration

    PROCEDURE add_employee(
        p_first_name IN VARCHAR2,
        p_last_name  IN VARCHAR2,
        p_salary     IN NUMBER
    ) IS
    BEGIN
        -- Logic to insert a new employee into the employee table (not shown here)
        emp_table.EXTEND;  -- Extending the array to accommodate the new employee
        emp_table(emp_table.LAST) := p_first_name || ' ' || p_last_name;  -- Store employee full name
        DBMS_OUTPUT.PUT_LINE('Employee added: ' || p_first_name || ' ' || p_last_name);
    END add_employee;

    FUNCTION get_employee(p_employee_id IN NUMBER) RETURN emp_rec IS
        emp_data emp_rec;  -- Variable to hold employee data
    BEGIN
        -- Logic to fetch employee data from the database (simulated here)
        emp_data.employee_id := p_employee_id;
        emp_data.first_name := 'John';  -- Dummy data
        emp_data.last_name := 'Doe';     -- Dummy data
        emp_data.salary := 50000;        -- Dummy data
        RETURN emp_data;
    END get_employee;

    PROCEDURE update_salary(
        p_employee_id IN NUMBER,
        p_new_salary  IN NUMBER
    ) IS
    BEGIN
        -- Logic to update the employee's salary in the database (not shown here)
        DBMS_OUTPUT.PUT_LINE('Salary updated for Employee ID: ' || p_employee_id || 
                             ' to ' || p_new_salary);
    END update_salary;

END emp_pkg;
/
```

**Explanation**:
- The package body implements the procedures and functions declared in the specification.
- **`add_employee`**: Simulates adding an employee by extending an internal array and printing a message.
- **`get_employee`**: Simulates fetching employee details by returning a record with hardcoded values.
- **`update_salary`**: Simulates updating an employee's salary and prints a message.

### Using the Package

Now that we have created our package, we can use it in a PL/SQL block as follows:

```sql
DECLARE
    emp_details emp_pkg.emp_rec;  -- Declare a variable of type emp_rec
BEGIN
    -- Add a new employee
    emp_pkg.add_employee('Alice', 'Smith', 60000);

    -- Get employee details
    emp_details := emp_pkg.get_employee(1);
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || emp_details.employee_id ||
                         ', Name: ' || emp_details.first_name || ' ' || emp_details.last_name ||
                         ', Salary: ' || emp_details.salary);

    -- Update employee salary
    emp_pkg.update_salary(1, 65000);
END;
/
```

**Explanation**:
- This PL/SQL block demonstrates how to use the `emp_pkg` package.
- It adds a new employee, retrieves and prints the details of an employee, and updates the employee's salary.

### Conclusion

The structure of a PL/SQL package, consisting of the specification and body, allows for better organization and encapsulation of related functionality. Packages enhance code reusability and maintainability, making it easier to manage complex PL/SQL applications. 

In this example, we've created a simple package to manage employee records, demonstrating both the specification and body components, along with how to utilize the package in a PL/SQL block.