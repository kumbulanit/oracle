In PL/SQL, **records** are composite data types that allow you to group related data together. A record can contain multiple fields (attributes), each of which can have different data types. Records are particularly useful when you want to handle data retrieved from a database table without needing to create a separate variable for each field.

### 1. Basic Record Declaration and Usage

#### Example 1: Simple Record Declaration

Here’s how to declare and use a simple record in PL/SQL.

```sql
DECLARE
    TYPE employee_record IS RECORD (
        employee_id   NUMBER,
        employee_name VARCHAR2(100),
        salary        NUMBER
    );

    v_employee employee_record;  -- Declare a variable of the record type
BEGIN
    -- Assign values to the record fields
    v_employee.employee_id := 101;
    v_employee.employee_name := 'John Doe';
    v_employee.salary := 50000;

    -- Display the values
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee.employee_id);
    DBMS_OUTPUT.PUT_LINE('Employee Name: ' || v_employee.employee_name);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_employee.salary);
END;
```

**Explanation**:
- A record type `employee_record` is defined with three fields.
- A variable `v_employee` of type `employee_record` is declared.
- Values are assigned to the record fields, and the values are displayed using `DBMS_OUTPUT.PUT_LINE`.

### 2. Record Type Based on Table Structure

#### Example 2: Record Based on a Table Structure

You can also define a record type that directly reflects a table’s structure using `%ROWTYPE`.

```sql
DECLARE
    v_employee employees%ROWTYPE;  -- Record based on the employees table
BEGIN
    SELECT * INTO v_employee
    FROM employees
    WHERE employee_id = 101;  -- Assuming employee_id 101 exists

    -- Display the values
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee.employee_id);
    DBMS_OUTPUT.PUT_LINE('Employee Name: ' || v_employee.employee_name);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_employee.salary);
END;
```

**Explanation**:
- The variable `v_employee` is declared using `%ROWTYPE`, which automatically includes all columns of the `employees` table.
- A `SELECT` statement fetches the data into the record variable, allowing you to access each field using dot notation.

### 3. Using Records in a Cursor Loop

#### Example 3: Using Records with Cursors

You can use records to fetch multiple rows from a cursor in a loop.

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT employee_id, employee_name, salary
        FROM employees;

    v_employee emp_cursor%ROWTYPE;  -- Record based on the cursor's row type
BEGIN
    OPEN emp_cursor;

    LOOP
        FETCH emp_cursor INTO v_employee;  -- Fetch into the record
        EXIT WHEN emp_cursor%NOTFOUND;  -- Exit the loop when no more rows

        -- Display the values
        DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee.employee_id || 
                             ', Name: ' || v_employee.employee_name || 
                             ', Salary: ' || v_employee.salary);
    END LOOP;

    CLOSE emp_cursor;
END;
```

**Explanation**:
- A cursor `emp_cursor` is declared to select employees.
- A variable `v_employee` is declared based on the cursor's row type.
- In the loop, rows are fetched into the record variable, allowing easy access to each column.

### 4. Nested Records

#### Example 4: Nested Records

You can also define records within records, which is useful for organizing complex data structures.

```sql
DECLARE
    TYPE address_record IS RECORD (
        street VARCHAR2(100),
        city   VARCHAR2(50),
        zip    VARCHAR2(10)
    );

    TYPE employee_record IS RECORD (
        employee_id   NUMBER,
        employee_name VARCHAR2(100),
        salary        NUMBER,
        address       address_record  -- Nested record
    );

    v_employee employee_record;  -- Declare a variable of the employee record type
BEGIN
    -- Assign values to the record fields
    v_employee.employee_id := 102;
    v_employee.employee_name := 'Jane Smith';
    v_employee.salary := 60000;
    
    -- Assign values to the nested record
    v_employee.address.street := '123 Elm St';
    v_employee.address.city := 'Springfield';
    v_employee.address.zip := '12345';

    -- Display the values
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || v_employee.employee_id);
    DBMS_OUTPUT.PUT_LINE('Employee Name: ' || v_employee.employee_name);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || v_employee.salary);
    DBMS_OUTPUT.PUT_LINE('Address: ' || v_employee.address.street || ', ' ||
                         v_employee.address.city || ' ' || v_employee.address.zip);
END;
```

**Explanation**:
- A nested record type `address_record` is defined within `employee_record`.
- Values are assigned to both the main record and the nested record.
- The values are displayed using `DBMS_OUTPUT.PUT_LINE`.

### 5. Advanced Example: Using Records in Procedures

#### Example 5: Using Records as Procedure Parameters

You can pass records as parameters to procedures for modular programming.

```sql
CREATE OR REPLACE PROCEDURE print_employee_info(p_employee IN employees%ROWTYPE) IS
BEGIN
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || p_employee.employee_id);
    DBMS_OUTPUT.PUT_LINE('Employee Name: ' || p_employee.employee_name);
    DBMS_OUTPUT.PUT_LINE('Salary: ' || p_employee.salary);
END;
/

DECLARE
    v_employee employees%ROWTYPE;  -- Record based on the employees table
BEGIN
    SELECT * INTO v_employee
    FROM employees
    WHERE employee_id = 103;  -- Assuming employee_id 103 exists

    -- Call the procedure
    print_employee_info(v_employee);
END;
```

**Explanation**:
- A procedure `print_employee_info` is created to accept an employee record as a parameter.
- Inside the procedure, the employee details are printed.
- The main block fetches an employee record and calls the procedure, passing the record as an argument.

### Conclusion

Records in PL/SQL provide a powerful way to handle structured data, enabling you to group related fields together, manage data from database tables, and pass complex data structures to procedures. By utilizing records, you can create more organized and maintainable code in your PL/SQL applications. 

- **Basic Records**: Demonstrated simple record creation and access.
- **Table Records**: Showed how to use `%ROWTYPE` for table structures.
- **Cursor Records**: Illustrated using records to fetch multiple rows in loops.
- **Nested Records**: Explained how to create records within records for complex data structures.
- **Procedures with Records**: Showed how to pass records to procedures for modular programming.