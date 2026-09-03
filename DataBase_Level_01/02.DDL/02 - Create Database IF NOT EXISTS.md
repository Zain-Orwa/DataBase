# 02 - Create Database IF NOT EXISTS

To avoid an error when the database may already exist, check first:

```sql
IF NOT EXISTS (
    SELECT *
    FROM sys.databases
    WHERE name = 'koko4'
)
BEGIN
    CREATE DATABASE koko4;
END
```

## What happens

- If `koko4` does **not** exist → SQL Server creates it.
- If `koko4` already exists → `CREATE DATABASE` is skipped.
- This prevents the duplicate-database error.

## SSMS Tip — Execute Only Highlighted Code

If you highlight/select part of your SQL code and click **Execute**, SSMS runs only the highlighted code.

- **Code highlighted** → only the selected code runs.
- **Nothing highlighted** → the whole script runs.
- Useful for testing one SQL command at a time.
