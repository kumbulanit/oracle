### PL/SQL Package Structure

A **PL/SQL package** is a group of related PL/SQL objects such as procedures, functions, variables, cursors, etc. It consists of two parts:
1. **Package Specification**: This is the interface to the package, containing the declarations of the public objects (procedures, functions, variables, constants, cursors, etc.) that can be accessed outside the package.
2. **Package Body**: This contains the implementation of the procedures, functions, and other objects declared in the specification. It may also contain private objects (which are not accessible from outside the package).

### Structure of a PL/SQL Package

1. **Package Specification**: The specification is the public interface for the package. It declares what is visible and accessible to users of the package (like procedures, functions, constants, cursors, etc.).

2. **Package Body**: The body implements the code behind the procedures and functions declared in the package specification. It can also contain private declarations that are not visible outside the package.

### Syntax for PL/SQL Package

#### 1. **Package Specification Syntax**

```plsql
CREATE [OR REPLACE] PACKAGE package_name AS
    -- Public declarations: variables, constants, cursors, procedures, functions, etc.
    PROCEDURE procedure_name(param1 datatype, param2 datatype);
    FUNCTION function_name(param1 datatype) RETURN return_datatype;
    -- More declarations here
END package_name;
/
```

#### 2. **Package Body Syntax**

```plsql
CREATE [OR REPLACE] PACKAGE BODY package_name AS
    -- Implementation of procedures and functions
    PROCEDURE procedure_name(param1 datatype, param2 datatype) IS
    BEGIN
        -- Code logic here
    END procedure_name;

    FUNCTION function_name(param1 datatype) RETURN return_datatype IS
    BEGIN
        -- Code logic here
        RETURN some_value;
    END function_name;

    -- Private procedures or functions (not accessible outside the package)
    PROCEDURE private_procedure IS
    BEGIN
        -- Private code logic here
    END private_procedure;

    -- More implementations here
END package_name;
/
```

---

### Basic Example of a PL/SQL Package

In this basic example, we will create a package that contains a procedure and a function. This package interacts with the employee database.

#### Example 1: Basic Package with a Procedure and a Function

1. **Package Specification**

```plsql
CREATE OR REPLACE PACKAGE Employee_Package AS
    -- Public procedure to display employee details
    PROCEDURE Show_Employee_Details(emp_id IN NUMBER);
    
    -- Public function to get the employee salary
    FUNCTION Get_Employee_Salary(emp_id IN NUMBER) RETURN NUMBER;
END Employee_Package;
/
```

2. **Package Body**

```plsql
CREATE OR REPLACE PACKAGE BODY Employee_Package AS
    -- Procedure to display employee details
    PROCEDURE Show_Employee_Details(emp_id IN NUMBER) IS
        emp_name VARCHAR2(100);
        emp_dept VARCHAR2(100);
    BEGIN
        SELECT ename, dname INTO emp_name, emp_dept
        FROM emp JOIN dept ON emp.deptno = dept.deptno
        WHERE empno = emp_id;
        
        DBMS_OUTPUT.PUT_LINE('Employee Name: ' || emp_name);
        DBMS_OUTPUT.PUT_LINE('Department: ' || emp_dept);
    END Show_Employee_Details;
    
    -- Function to get employee salary
    FUNCTION Get_Employee_Salary(emp_id IN NUMBER) RETURN NUMBER IS
        emp_salary NUMBER;
    BEGIN
        SELECT sal INTO emp_salary FROM emp WHERE empno = emp_id;
        RETURN emp_salary;
    END Get_Employee_Salary;
END Employee_Package;
/
```

3. **Using the Package**

```plsql
BEGIN
    -- Display employee details
    Employee_Package.Show_Employee_Details(7369);
    
    -- Get and display employee salary
    DBMS_OUTPUT.PUT_LINE('Employee Salary: ' || Employee_Package.Get_Employee_Salary(7369));
END;
/
```

---

### Advanced Example of a PL/SQL Package

In this advanced example, we will create a package that handles multiple functionalities for employees, including adding new employees, updating employee salaries, and deleting employees. We will also have private functions for internal package logic that will not be accessible from outside the package.

#### Example 2: Advanced Package with Public and Private Components

1. **Package Specification**

```plsql
CREATE OR REPLACE PACKAGE Advanced_Employee_Package AS
    -- Public procedures to manage employee data
    PROCEDURE Add_Employee(emp_id IN NUMBER, emp_name IN VARCHAR2, emp_salary IN NUMBER);
    PROCEDURE Update_Employee_Salary(emp_id IN NUMBER, new_salary IN NUMBER);
    PROCEDURE Delete_Employee(emp_id IN NUMBER);
    
    -- Public function to get employee details
    FUNCTION Get_Employee_Info(emp_id IN NUMBER) RETURN VARCHAR2;
END Advanced_Employee_Package;
/
```

2. **Package Body**

```plsql
CREATE OR REPLACE PACKAGE BODY Advanced_Employee_Package AS
    -- Private procedure to log actions (not accessible outside the package)
    PROCEDURE Log_Action(action_desc IN VARCHAR2) IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('Action: ' || action_desc);
    END Log_Action;

    -- Public procedure to add a new employee
    PROCEDURE Add_Employee(emp_id IN NUMBER, emp_name IN VARCHAR2, emp_salary IN NUMBER) IS
    BEGIN
        INSERT INTO emp (empno, ename, sal) VALUES (emp_id, emp_name, emp_salary);
        Log_Action('Added employee ' || emp_name);
    END Add_Employee;

    -- Public procedure to update employee salary
    PROCEDURE Update_Employee_Salary(emp_id IN NUMBER, new_salary IN NUMBER) IS
    BEGIN
        UPDATE emp SET sal = new_salary WHERE empno = emp_id;
        Log_Action('Updated salary for employee ID: ' || emp_id);
    END Update_Employee_Salary;

    -- Public procedure to delete an employee
    PROCEDURE Delete_Employee(emp_id IN NUMBER) IS
        emp_name VARCHAR2(100);
    BEGIN
        SELECT ename INTO emp_name FROM emp WHERE empno = emp_id;
        DELETE FROM emp WHERE empno = emp_id;
        Log_Action('Deleted employee ' || emp_name);
    END Delete_Employee;

    -- Public function to get employee info
    FUNCTION Get_Employee_Info(emp_id IN NUMBER) RETURN VARCHAR2 IS
        emp_info VARCHAR2(100);
    BEGIN
        SELECT ename || ', Salary: ' || sal INTO emp_info FROM emp WHERE empno = emp_id;
        RETURN emp_info;
    END Get_Employee_Info;
END Advanced_Employee_Package;
/
```

3. **Using the Advanced Package**

```plsql
BEGIN
    -- Add a new employee
    Advanced_Employee_Package.Add_Employee(1001, 'John Doe', 5000);
    
    -- Update employee salary
    Advanced_Employee_Package.Update_Employee_Salary(1001, 6000);
    
    -- Get employee info
    DBMS_OUTPUT.PUT_LINE(Advanced_Employee_Package.Get_Employee_Info(1001));
    
    -- Delete the employee
    Advanced_Employee_Package.Delete_Employee(1001);
END;
/
```

#### Output:

```
Action: Added employee John Doe
Action: Updated salary for employee ID: 1001
John Doe, Salary: 6000
Action: Deleted employee John Doe
```

---

### Key Features of PL/SQL Packages

- **Private vs Public Declarations**: 
  - **Public**: Anything declared in the package specification is public and can be accessed from outside the package.
  - **Private**: Declarations in the package body but not in the specification are private and can only be used within the package.
  
- **Overloading Procedures/Functions**: You can define multiple procedures or functions with the same name but with different parameter types (this is called overloading).
  
  Example:
  ```plsql
  PROCEDURE Update_Employee(emp_id IN NUMBER, new_salary IN NUMBER);
  PROCEDURE Update_Employee(emp_name IN VARCHAR2, new_salary IN NUMBER);
  ```

- **Package Initialization**: The package body can include an initialization block. This block runs only once when the package is first invoked in a session.
  
  Example:
  ```plsql
  CREATE OR REPLACE PACKAGE BODY Example_Package AS
      BEGIN
          -- Initialization section
          DBMS_OUTPUT.PUT_LINE('Package Initialized');
      END Example_Package;
  ```

- **Global Variables**: Variables declared in the package specification can be shared across multiple procedures and functions within the package, making them global to the package.

  Example:
  ```plsql
  CREATE OR REPLACE PACKAGE Global_Package AS
      -- Global variable
      g_counter NUMBER := 0;
      
      PROCEDURE Increment_Counter;
      FUNCTION Get_Counter RETURN NUMBER;
  END Global_Package;
  /
  
  CREATE OR REPLACE PACKAGE BODY Global_Package AS
      PROCEDURE Increment_Counter IS
      BEGIN
          g_counter := g_counter + 1;
      END Increment_Counter;
      
      FUNCTION Get_Counter RETURN NUMBER IS
      BEGIN
          RETURN g_counter;
      END Get_Counter;
  END Global_Package;
  /
  ```

---

### Conclusion

PL/SQL packages offer a modular and powerful way to group related procedures, functions, and other elements into a single logical unit. They enhance the maintainability, reusability, and security of PL/SQL code, allowing public-private encapsulation of logic and improving performance by loading the whole package into memory at once.
