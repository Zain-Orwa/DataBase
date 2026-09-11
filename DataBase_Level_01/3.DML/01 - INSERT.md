# 01 - INSERT

`INSERT` is a DML (**Data Manipulation Language**) statement used to add new records to an existing table.

This lesson uses the `Employees` table examples from your practice script.

---

## 1. Show the Current Data

Before or after inserting records, we can check the table with:

```sql
SELECT *
FROM Employees;
```

This displays all rows and all columns in the `Employees` table.

---

## 2. Insert One Record

### Syntax

```sql
INSERT INTO TableName
VALUES (value1, value2, value3, ...);
```

### Example

```sql
INSERT INTO Employees
VALUES (10, 'Emp10', '079939', 1000);
```

This inserts one complete record into the `Employees` table.

---

## 3. Insert a Record with `NULL` Values

If a column allows `NULL`, we can insert `NULL` for that column.

```sql
INSERT INTO Employees
VALUES (11, 'Emp11', NULL, NULL);
```

Here:

```text
ID      → 11
Name    → Emp11
Phone   → NULL
Salary  → NULL
```

`NULL` means that no value is stored for that field.

---

## 4. Insert Multiple Records at Once

We can insert several rows with one `INSERT` statement.

```sql
INSERT INTO Employees
VALUES
    (2, 'Emp2', '552221', 700),
    (3, 'Emp3', '55554', 300),
    (4, 'Emp4', '322344', 400);
```

This inserts three records at the same time.

---

## 5. Insert Only Selected Columns

We do not always have to provide values for every column.

We can specify exactly which columns we want to insert into:

```sql
INSERT INTO Employees (ID, Name)
VALUES (5, 'Emp5');
```

The other columns will receive `NULL` if they allow `NULL`, or their default value if one exists.

---

## 6. What Happens If We Forget a `NOT NULL` Column?

If a required column is defined as `NOT NULL` and has no default value, SQL Server will return an error if we do not provide a value for it.

Example:

```sql
INSERT INTO Employees (ID)
VALUES (5);
```

If `Name` is defined as:

```sql
Name NVARCHAR(50) NOT NULL
```

then this insert fails because `Name` must have a value.

---

## 7. Recommended INSERT Style

Even when inserting values for every column, it is usually clearer to specify the column names:

```sql
INSERT INTO Employees (ID, Name, Phone, Salary)
VALUES (10, 'Emp10', '079939', 1000);
```

This is easier to read and safer if the table structure changes later.

---

## Quick Summary

| Case                    | Example                                                        |
|-------------------------|----------------------------------------------------------------|
| Insert one row          | `INSERT INTO Employees VALUES (...);`                          |
| Insert `NULL`           | `VALUES (11, 'Emp11', NULL, NULL);`                            |
| Insert multiple rows    | `VALUES (...), (...), (...);`                                  |
| Insert selected columns | `INSERT INTO Employees (ID, Name) VALUES (5, 'Emp5');`         |
| Missing `NOT NULL`      | Error if no value/default is provided                          |
| Check inserted data     | `SELECT * FROM Employees;`                                     |

---

## Main Pattern to Remember

```sql
INSERT INTO TableName (Column1, Column2, ...)
VALUES (Value1, Value2, ...);
```

`INSERT` means:

> Add new record(s) to a table.
