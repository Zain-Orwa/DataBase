# 03 - Switch Database

In SQL Server Management Studio (SSMS), there are **two common ways** to choose which database your query will run against.

---

## 1. Switch Database Using the SSMS Interface

At the top of the query window, SSMS has a **database dropdown**.

For example, it may currently show:

```text
master
```

You can click the dropdown and choose another database such as:

```text
koko
DB1
DB2
```

After selecting a database, the queries in that query window will run against the selected database.

### Why this method can be risky

This method works, but it is easier to make a mistake because a developer may:

- Forget which database is selected.
- Open a new query window that uses a different database.
- Accidentally execute a query against the wrong database.

So we should **not rely only on the interface selection**.

---

## 2. Switch Database in the SQL Script — Recommended

A safer and clearer way is to specify the database directly in the script using `USE`.

```sql
USE koko;
```

This tells SQL Server:

> Run the following SQL statements using the `koko` database.

### Example

```sql
USE koko;

CREATE TABLE Students
(
    ID INT,
    Name VARCHAR(50)
);
```

Here, SQL Server first switches to the `koko` database and then creates the `Students` table there.

### Why this way is better

- The database being used is written directly in the script.
- Another developer can immediately see which database the script expects.
- You do not have to depend on remembering the SSMS dropdown selection.
- It reduces the chance of accidentally running commands against the wrong database.

**Best practice:** Put the `USE database_name;` statement near the beginning of your SQL script when the script is intended for a specific database.

---

## Another Option — Fully Qualified Object Name

SQL Server can also reference an object in another database without switching the current database.

```sql
SELECT *
FROM koko.dbo.Students;
```

The format is:

```text
DatabaseName.SchemaName.ObjectName
```

This does **not actually switch the active database**. It simply tells SQL Server exactly where that object is located.

---

## Quick Summary

| Method | Example | Recommendation |
|---|---|---|
| SSMS database dropdown | Select `koko` from the dropdown | Works, but easier to forget |
| `USE` statement | `USE koko;` | ✅ Recommended for scripts |
| Fully qualified name | `koko.dbo.Students` | Useful when accessing a specific object in another database |

### Remember

```sql
USE koko;
```

is the clearest way to make the intended database part of the SQL script itself.
