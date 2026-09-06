# 07 - Drop Table

`DROP TABLE` permanently deletes a table from the database.

## Basic Syntax

```sql
DROP TABLE Employees;
```

This removes the `Employees` table completely, including its data.

## Safer Version — `IF EXISTS`

```sql
DROP TABLE IF EXISTS Employees;
```

- If `Employees` exists → SQL Server drops it.
- If it does not exist → no "table does not exist" error is raised.

## Example

```sql
USE DB1;

DROP TABLE IF EXISTS Employees;
```

## Important

`DROP TABLE` deletes the **table structure and all rows inside it**.

It is different from:

```sql
DELETE FROM Employees;
```

`DELETE` removes rows but keeps the table.

## Remember

- `DROP TABLE` → deletes the whole table.
- `DELETE` → deletes data but keeps the table.
- `DROP TABLE IF EXISTS` → safer when you are not sure whether the table exists.
