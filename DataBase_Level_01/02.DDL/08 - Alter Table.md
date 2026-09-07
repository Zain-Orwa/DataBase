# 08 - Alter Table

`ALTER TABLE` is used to change the structure of an existing table.

With `ALTER TABLE`, we can:

- Add a column
- Rename a column
- Rename a table
- Modify a column
- Delete a column

---

## 1. Add Column

We can add a new column using `ALTER TABLE` with the `ADD` clause.

### Syntax

```sql
ALTER TABLE TableName
ADD ColumnName DataType;
```

### Example

```sql
ALTER TABLE Employees
ADD Gendor CHAR(1);
```

This adds a new column named `Gendor` to the `Employees` table.

---

## 2. Rename Column

### Most Databases

In many databases, a column can be renamed using:

```sql
ALTER TABLE Employees
RENAME COLUMN Gendor TO Gender;
```

This changes the column name from `Gendor` to `Gender`.

### SQL Server

In SQL Server, the lesson uses `sp_rename` instead of `ALTER TABLE ... RENAME COLUMN`.

### Syntax

```sql
EXEC sp_rename 'table_name.old_column_name',
               'new_column_name',
               'COLUMN';
```

### Example

```sql
EXEC sp_rename 'Employees.Gendor',
               'Gender',
               'COLUMN';
```

---

## 3. Rename a Table

### Most Databases

```sql
ALTER TABLE OldTableName
RENAME TO NewTableName;
```

### SQL Server

In SQL Server, the lesson uses `sp_rename`.

### Syntax

```sql
EXEC sp_rename 'old_table_name',
               'new_table_name';
```

### Example

```sql
EXEC sp_rename 'Employees',
               'Staff';
```

This renames the `Employees` table to `Staff`.

---

## 4. Modify a Column

We can change a column definition, such as its data type or size.

### SQL Server

```sql
ALTER TABLE Employees
ALTER COLUMN Name VARCHAR(100);
```

### MySQL

```sql
ALTER TABLE Employees
MODIFY COLUMN Name VARCHAR(100);
```

### Oracle

```sql
ALTER TABLE Employees
MODIFY Name VARCHAR(100);
```

### PostgreSQL

```sql
ALTER TABLE Employees
ALTER COLUMN Name TYPE VARCHAR(100);
```

This example changes the `Name` column so it can store up to 100 characters.

---

## 5. Delete a Column

We can remove a column using `ALTER TABLE` with the `DROP COLUMN` clause.

### Syntax

```sql
ALTER TABLE TableName
DROP COLUMN ColumnName;
```

### Example

```sql
ALTER TABLE Employees
DROP COLUMN Gender;
```

This removes the `Gender` column from the `Employees` table.

---

## Quick Summary

| Action        | SQL Server Example                                       |
|---------------|----------------------------------------------------------|
| Add column    | `ALTER TABLE Employees ADD Gendor CHAR(1);`              |
| Rename column | `EXEC sp_rename 'Employees.Gendor', 'Gender', 'COLUMN';` |
| Rename table  | `EXEC sp_rename 'Employees', 'Staff';`                   |
| Modify column | `ALTER TABLE Employees ALTER COLUMN Name VARCHAR(100);`  |
| Delete column | `ALTER TABLE Employees DROP COLUMN Gender;`              |

---

## Main Pattern to Remember

```sql
ALTER TABLE TableName
...
```

`ALTER TABLE` means:

> Change something about an already existing table.
