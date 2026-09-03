# 04 - Drop Database

## 1. What is `DROP DATABASE`?

In SQL, `DROP DATABASE` is used to **permanently delete an entire database** from the Database Management System (DBMS).

Example:

```sql
DROP DATABASE koko;
```

This command deletes the database named `koko`.

---

## 2. What happens when you use `DROP DATABASE`?

When a database is dropped:

1. The database itself is deleted.
2. All tables inside the database are deleted.
3. All records/data inside those tables are deleted.
4. Other database objects inside it are also removed.

> ⚠️ `DROP DATABASE` is destructive. The deleted data is normally not recoverable unless you have a backup.

---

## 3. Basic Syntax

```sql
DROP DATABASE database_name;
```

Example:

```sql
DROP DATABASE school;
```

This deletes the entire `school` database.

---

## 4. Safer Syntax

In MySQL, you can use:

```sql
DROP DATABASE IF EXISTS school;
```

This means:

- If `school` exists → delete it.
- If `school` does not exist → do not produce an error just because it is missing.

---

## 5. `DROP DATABASE` vs `DROP TABLE`

These commands are different.

### Delete one table

```sql
DROP TABLE students;
```

Only the `students` table is deleted.

### Delete the entire database

```sql
DROP DATABASE school;
```

The whole `school` database and everything inside it are deleted.

---

## 6. Example

Suppose we have:

```text
school
├── students
├── courses
└── enrollment
```

If we run:

```sql
DROP TABLE students;
```

The result is:

```text
school
├── courses
└── enrollment
```

But if we run:

```sql
DROP DATABASE school;
```

The entire `school` database is removed.

---

## 7. Permissions

You need sufficient permission to drop a database.

For example, in MySQL, the user normally needs the `DROP` privilege.

Without the required permission, the DBMS will reject the command.

---

## 8. Important Rule

Before running:

```sql
DROP DATABASE database_name;
```

always make sure:

1. You selected the correct database name.
2. You no longer need the data.
3. You have a backup if the data may be needed later.

---

## 9. Quick Comparison

| Command | What it removes |
|---|---|
| `DROP DATABASE database_name;` | Entire database |
| `DROP TABLE table_name;` | One table |
| `DELETE FROM table_name;` | Rows/data from a table |
| `TRUNCATE TABLE table_name;` | All rows from a table, but keeps the table |

---

## 10. Example to Remember

```sql
CREATE DATABASE koko;
```

Creates the database.

```sql
DROP DATABASE koko;
```

Deletes the database.

Think of it like:

```text
CREATE DATABASE → create the whole container
DROP DATABASE   → destroy the whole container
```

---

## 11. Quick Self-Check

### Question 1

What does this do?

```sql
DROP DATABASE company;
```

**Answer:** It permanently deletes the `company` database and the objects/data stored inside it.

### Question 2

Which command deletes only one table?

```sql
DROP TABLE employees;
```

### Question 3

Which is safer?

```sql
DROP DATABASE company;
```

or

```sql
DROP DATABASE IF EXISTS company;
```

**Answer:** `DROP DATABASE IF EXISTS company;` avoids an error when the database does not exist.

---

## 12. Key Point

> `DROP DATABASE` removes the **entire database**, not just its data.

```sql
DROP DATABASE database_name;
```

Use it carefully.
