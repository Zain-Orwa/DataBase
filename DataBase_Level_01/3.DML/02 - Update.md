# 02 - UPDATE

`UPDATE` is a DML (**Data Manipulation Language**) statement used to modify existing records in a table.

---

## 1. Basic UPDATE Syntax

```sql
UPDATE TableName
SET Column1 = Value1,
    Column2 = Value2
WHERE condition;
```

### Important

The `WHERE` clause decides **which row or rows will be updated**.

If you forget `WHERE`, SQL Server updates **all rows** in the table.

---

## 2. Update One Field in One Row

```sql
UPDATE Employees
SET Name = 'Mohammed Abu-Hadhoud'
WHERE ID = 2;
```

This updates only the `Name` of the employee whose `ID = 2`.

---

## 3. Update Multiple Fields in One Row

```sql
UPDATE Employees
SET Name = 'Mohammed Abu-Hadhoud',
    Salary = 5000
WHERE ID = 2;
```

This updates more than one column for the **same row**.

---

## 4. Update Multiple Rows Using One Condition

We can update many rows at once when they all follow the **same rule**.

### Example — Add 200 to salaries below 500

```sql
UPDATE Employees
SET Salary = Salary + 200
WHERE Salary < 500;
```

Every employee with a salary below `500` gets `200` added.

### Example — Increase salary by 10%

```sql
UPDATE Employees
SET Salary = Salary * 1.10
WHERE Salary <= 1000;
```

Every employee whose salary is less than or equal to `1000` gets the same 10% increase.

---

## 5. Update Multiple Specific Rows with Different Values

Sometimes we want to update several rows in **one statement**, but each row needs a **different value**.

That is when `CASE` is useful.

```sql
UPDATE Employees
SET
    Name = CASE ID
        WHEN 1 THEN 'Emp1'
        WHEN 2 THEN 'Emp2'
        WHEN 3 THEN 'Emp3'
        WHEN 4 THEN 'Emp4'
    END,

    Phone = CASE ID
        WHEN 1 THEN '012345678'
        WHEN 2 THEN '948847477'
        WHEN 3 THEN '876544333'
        WHEN 4 THEN '123432213'
    END

WHERE ID IN (1, 2, 3, 4);
```

### What happens here?

`CASE ID` checks the `ID` of each selected employee.

For `Name`:

```text
ID 1 → Emp1
ID 2 → Emp2
ID 3 → Emp3
ID 4 → Emp4
```

For `Phone`:

```text
ID 1 → 012345678
ID 2 → 948847477
ID 3 → 876544333
ID 4 → 123432213
```

And this part:

```sql
WHERE ID IN (1, 2, 3, 4);
```

limits the update to only those four employees.

---

## 6. What Is the Difference Between Normal UPDATE and UPDATE with CASE?

### Normal UPDATE

Use a normal `UPDATE` when the selected row or rows receive the **same value or same calculation**.

Example:

```sql
UPDATE Employees
SET Salary = Salary + 200
WHERE Salary < 500;
```

Every matching row follows the same rule:

```text
Salary = Salary + 200
```

### UPDATE with CASE

Use `CASE` when several selected rows must receive **different values**.

Example:

```sql
UPDATE Employees
SET Name = CASE ID
    WHEN 1 THEN 'Emp1'
    WHEN 2 THEN 'Emp2'
    WHEN 3 THEN 'Emp3'
    WHEN 4 THEN 'Emp4'
END
WHERE ID IN (1, 2, 3, 4);
```

Each row gets a different `Name` based on its `ID`.

---

## 7. When Should I Use Each One?

| Situation                               | Use                           | Example                                   |
|-----------------------------------------|-------------------------------|-------------------------------------------|
| Change one field in one row             | Normal `UPDATE`               | Change employee `ID = 2` name             |
| Change several fields in one row        | Normal `UPDATE`               | Change name and salary for `ID = 2`       |
| Change many rows using the same rule    | Normal `UPDATE` + `WHERE`     | Add `200` to every salary below `500`     |
| Change many rows by same percentage     | Normal `UPDATE` + calculation | Increase salaries by `10%`                |
| Change many rows to different values    | `UPDATE` + `CASE`             | Give IDs `1,2,3,4` different names/phones |
| Limit updates to specific IDs           | `WHERE ID IN (...)`           | Update only IDs `1,2,3,4`                 |

---

## 8. Simple Comparison

### Same value for several rows

```sql
UPDATE Employees
SET Salary = 5000
WHERE ID IN (1, 2, 3, 4);
```

Result:

```text
ID 1 → Salary 5000
ID 2 → Salary 5000
ID 3 → Salary 5000
ID 4 → Salary 5000
```

Use a **normal UPDATE**.

### Different value for each row

```sql
UPDATE Employees
SET Salary = CASE ID
    WHEN 1 THEN 1000
    WHEN 2 THEN 2000
    WHEN 3 THEN 3000
    WHEN 4 THEN 4000
END
WHERE ID IN (1, 2, 3, 4);
```

Result:

```text
ID 1 → Salary 1000
ID 2 → Salary 2000
ID 3 → Salary 3000
ID 4 → Salary 4000
```

Use **UPDATE with CASE**.

---

## 9. Why Is the `WHERE` Clause Very Important?

Without `WHERE`:

```sql
UPDATE Employees
SET Salary = 5000;
```

Every employee gets:

```text
Salary = 5000
```

With `WHERE`:

```sql
UPDATE Employees
SET Salary = 5000
WHERE ID = 2;
```

only employee `ID = 2` is updated.

### Rule

> Always check your `WHERE` condition before running an `UPDATE`.

A useful habit is to test the condition first:

```sql
SELECT *
FROM Employees
WHERE ID IN (1, 2, 3, 4);
```

If the `SELECT` returns exactly the rows you want, then use the same `WHERE` in your `UPDATE`.

---

## 10. Main Patterns to Remember

### One row

```sql
UPDATE Employees
SET Name = 'New Name'
WHERE ID = 2;
```

### Multiple columns in one row

```sql
UPDATE Employees
SET Name = 'New Name',
    Salary = 5000
WHERE ID = 2;
```

### Multiple rows — same rule

```sql
UPDATE Employees
SET Salary = Salary + 200
WHERE Salary < 500;
```

### Multiple rows — different values

```sql
UPDATE Employees
SET Name = CASE ID
    WHEN 1 THEN 'Emp1'
    WHEN 2 THEN 'Emp2'
    WHEN 3 THEN 'Emp3'
    WHEN 4 THEN 'Emp4'
END
WHERE ID IN (1, 2, 3, 4);
```

---

# Final Memory Rule

```text
Same value / same calculation
            ↓
       Normal UPDATE

Different value for each row
            ↓
      UPDATE + CASE
```

And always remember:

```text
WHERE = Which rows should change?
```
