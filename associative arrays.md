### Associative Arrays in Oracle PL/SQL

An **associative array** (also known as **index-by table**) in Oracle PL/SQL is a collection type that can be indexed using non-consecutive integer values or string values. This allows for more flexible and dynamic indexing compared to traditional arrays, where the index must be sequential.

#### Characteristics of Associative Arrays:
- They are unbounded, meaning they can grow dynamically.
- They are sparsely populated, allowing non-consecutive indexing.
- Indexed by a **VARCHAR2** or **PLS_INTEGER** type.
- They are stored in memory and, therefore, cannot be directly manipulated in SQL queries.
- No need to define the maximum size of an associative array.

### Syntax for Declaring an Associative Array:

```plsql
TYPE array_type IS TABLE OF element_type INDEX BY index_type;
```

Where:
- `array_type`: Name of the associative array type.
- `element_type`: The type of data stored in the array (e.g., `NUMBER`, `VARCHAR2`, etc.).
- `index_type`: This can be either `VARCHAR2` or `PLS_INTEGER`.

---

### 1. **Basic Example of Associative Array**

Here’s a simple example where we declare an associative array indexed by integers.

#### Example 1: Declaring, Initializing, and Accessing Associative Array with Integer Index

```plsql
DECLARE
  -- Declare an associative array
  TYPE NumberArray IS TABLE OF NUMBER INDEX BY PLS_INTEGER;
  num_arr NumberArray;  -- Array variable
BEGIN
  -- Initialize array elements
  num_arr(1) := 100;
  num_arr(5) := 250;
  num_arr(10) := 500;

  -- Access and display array elements
  DBMS_OUTPUT.PUT_LINE('Element at index 1: ' || num_arr(1));
  DBMS_OUTPUT.PUT_LINE('Element at index 5: ' || num_arr(5));
  DBMS_OUTPUT.PUT_LINE('Element at index 10: ' || num_arr(10));
END;
/
```

#### Output:
```
Element at index 1: 100
Element at index 5: 250
Element at index 10: 500
```

In this basic example, we create an associative array, assign values to specific indexes, and then retrieve and print those values using `DBMS_OUTPUT`.

---

### 2. **Using Associative Arrays with Strings as Indexes**

In this example, we declare an associative array indexed by `VARCHAR2` instead of `PLS_INTEGER`. This is useful when you want to use string keys for your array elements.

#### Example 2: Associative Array with String Index

```plsql
DECLARE
  -- Declare an associative array with VARCHAR2 as index
  TYPE EmployeeArray IS TABLE OF VARCHAR2(100) INDEX BY VARCHAR2(30);
  emp_arr EmployeeArray;  -- Array variable
BEGIN
  -- Initialize array elements using string keys
  emp_arr('EMP001') := 'John Doe';
  emp_arr('EMP002') := 'Jane Smith';
  emp_arr('EMP003') := 'Alice Johnson';

  -- Access and display array elements
  DBMS_OUTPUT.PUT_LINE('EMP001: ' || emp_arr('EMP001'));
  DBMS_OUTPUT.PUT_LINE('EMP002: ' || emp_arr('EMP002'));
  DBMS_OUTPUT.PUT_LINE('EMP003: ' || emp_arr('EMP003'));
END;
/
```

#### Output:
```
EMP001: John Doe
EMP002: Jane Smith
EMP003: Alice Johnson
```

Here, instead of using integers as indices, we use employee IDs (`EMP001`, `EMP002`, etc.) to index the array. This approach is very helpful when managing data that requires meaningful string keys.

---

### 3. **Iterating Over an Associative Array**

To traverse or loop through the elements of an associative array, we can use the `FIRST` and `NEXT` methods, which allow access to elements without knowing the index order.

#### Example 3: Looping through Associative Array with Integer Index

```plsql
DECLARE
  TYPE NumberArray IS TABLE OF NUMBER INDEX BY PLS_INTEGER;
  num_arr NumberArray;  -- Array variable
  idx PLS_INTEGER;  -- Variable to store the current index
BEGIN
  -- Initialize array elements
  num_arr(1) := 100;
  num_arr(5) := 250;
  num_arr(10) := 500;

  -- Loop through the array
  idx := num_arr.FIRST;  -- Get the first index
  WHILE idx IS NOT NULL LOOP
    DBMS_OUTPUT.PUT_LINE('Element at index ' || idx || ': ' || num_arr(idx));
    idx := num_arr.NEXT(idx);  -- Move to the next index
  END LOOP;
END;
/
```

#### Output:
```
Element at index 1: 100
Element at index 5: 250
Element at index 10: 500
```

Here, we use the `FIRST` and `NEXT` methods to navigate through the array elements. This allows us to loop through the array in an efficient way without hardcoding the indices.

---

### 4. **Advanced Example: Associative Array in a Procedure**

Let’s now look at how an associative array can be passed into a procedure.

#### Example 4: Passing Associative Array as a Parameter to a Procedure

```plsql
DECLARE
  -- Declare an associative array with INTEGER index
  TYPE GradeArray IS TABLE OF NUMBER INDEX BY PLS_INTEGER;

  -- Procedure that processes the array
  PROCEDURE PrintGrades(grades IN GradeArray) IS
    idx PLS_INTEGER;
  BEGIN
    idx := grades.FIRST;
    WHILE idx IS NOT NULL LOOP
      DBMS_OUTPUT.PUT_LINE('Student ' || idx || ' Grade: ' || grades(idx));
      idx := grades.NEXT(idx);
    END LOOP;
  END;

  -- Array variable
  student_grades GradeArray;
BEGIN
  -- Initialize array elements
  student_grades(101) := 85;
  student_grades(102) := 90;
  student_grades(103) := 75;

  -- Call procedure to print grades
  PrintGrades(student_grades);
END;
/
```

#### Output:
```
Student 101 Grade: 85
Student 102 Grade: 90
Student 103 Grade: 75
```

This advanced example demonstrates how an associative array can be passed as a parameter to a procedure. The procedure `PrintGrades` takes an associative array as input, loops through the array, and prints the grades of the students.

---

### 5. **Advanced Example: Working with Associative Arrays and SQL Queries**

In this example, we use associative arrays to store data fetched from a SQL query.

#### Example 5: Associative Array with SQL Query

```plsql
DECLARE
  -- Declare an associative array with VARCHAR2 index
  TYPE SalaryArray IS TABLE OF NUMBER INDEX BY VARCHAR2(10);
  emp_salaries SalaryArray;

  -- Variable to hold employee name and salary
  v_emp_name VARCHAR2(50);
  v_salary NUMBER;

BEGIN
  -- Fetch employees and their salaries
  FOR rec IN (SELECT empno, ename, sal FROM emp WHERE deptno = 10) LOOP
    -- Store employee salary using empno as key
    emp_salaries(rec.empno) := rec.sal;
  END LOOP;

  -- Access and display the associative array data
  FOR empno IN emp_salaries.FIRST .. emp_salaries.LAST LOOP
    IF emp_salaries.EXISTS(empno) THEN
      DBMS_OUTPUT.PUT_LINE('EmpNo: ' || empno || ', Salary: ' || emp_salaries(empno));
    END IF;
  END LOOP;
END;
/
```

This example demonstrates how you can populate an associative array from a SQL query result and iterate over the elements.

---

### Conclusion

Associative arrays in Oracle PL/SQL are powerful tools for managing collections of data indexed by integers or strings. They allow for dynamic storage, flexible indexing, and efficient in-memory data handling, making them ideal for many use cases, including passing data between PL/SQL blocks, handling SQL query results, and more.

