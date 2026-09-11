# 03 - DELETE

`DELETE` is a DML (**Data Manipulation Language**) statement used to remove existing records from a table.

---

## 1. Basic DELETE Syntax

```sql
DELETE FROM TableName
WHERE condition;
```

The `WHERE` clause decides **which row or rows will be deleted**.

> **Important:** If you omit the `WHERE` clause, all records in the table will be deleted.

---

## 2. Show the Current Data

Before or after deleting records, we can check the table with:

```sql
SELECT *
FROM Employees;
```

---

## 3. Delete All Records

```sql
DELETE FROM Employees;
```

This deletes **all rows** from the `Employees` table.

SQL Server also allows:

```sql
DELETE Employees;
```

Both work in SQL Server, but this form is clearer and more common:

```sql
DELETE FROM Employees;
```

### Important

This deletes the **data only**.

The table itself still exists.

---

## 4. Delete Rows Using a Condition

We can use `WHERE` to delete only the rows that match a condition.

### Example — Delete Employees with `NULL` Salary

```sql
DELETE FROM Employees
WHERE Salary IS NULL;
```

This deletes only employees whose `Salary` is `NULL`.

---

## 5. Delete One Specific Record

```sql
DELETE FROM Employees
WHERE ID = 4;
```

This deletes only the employee whose `ID = 4`.

---

## 6. Why Is `WHERE` Important?

### Without `WHERE`

```sql
DELETE FROM Employees;
```

Result:

```text
All records are deleted.
```

### With `WHERE`

```sql
DELETE FROM Employees
WHERE ID = 4;
```

Result:

```text
Only ID = 4 is deleted.
```

### Rule

> Always check your `WHERE` condition before running a `DELETE`.

A useful habit is to test the condition first with `SELECT`:

```sql
SELECT *
FROM Employees
WHERE ID = 4;
```

If the `SELECT` returns exactly the row or rows you want to remove, then use the same condition in the `DELETE`.

---

## 7. DELETE Does Not Delete the Table

```sql
DELETE FROM Employees;
```

removes all rows, but keeps:

- the table
- the columns
- the data types
- the constraints

So after deleting all rows, `Employees` still exists.

---

## Quick Summary

| Case                       | Example                                       |
|----------------------------|-----------------------------------------------|
| Delete all rows            | `DELETE FROM Employees;`                      |
| Delete rows with condition | `DELETE FROM Employees WHERE Salary IS NULL;` |
| Delete one specific row    | `DELETE FROM Employees WHERE ID = 4;`         |
| Check rows before deleting | `SELECT * FROM Employees WHERE ID = 4;`       |
| No `WHERE` clause          | Deletes all rows                              |
| `DELETE` table structure?  | No — the table remains                        |

---

## Main Pattern to Remember

```sql
DELETE FROM TableName
WHERE condition;
```

`DELETE` means:

> Remove existing record(s) from a table.

And always remember:

```text
WHERE = Which rows should be deleted?
```
