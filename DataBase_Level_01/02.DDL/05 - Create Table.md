# 05 - Create Table

A **table** stores data in rows and columns.

To create a table in SQL Server, use:

```sql
CREATE TABLE TableName
(
    Column1 DataType,
    Column2 DataType,
    Column3 DataType
);
```

## Example — Employees

```sql
CREATE TABLE Employees
(
    ID INT NOT NULL,
    Name NVARCHAR(50) NOT NULL,
    Phone NVARCHAR(50) NULL,
    Salary SMALLMONEY NULL,
    PRIMARY KEY (ID)
);
```

## Example — Departments

```sql
CREATE TABLE Departments
(
    DepID INT NOT NULL,
    Name NVARCHAR(50) NOT NULL,
    Location NVARCHAR(50) NULL,
    PRIMARY KEY (DepID)
);
```

You can create more than one table in the **same SQL script**:

```sql
CREATE TABLE Employees
(
    ID INT NOT NULL,
    Name NVARCHAR(50) NOT NULL,
    Phone NVARCHAR(50) NULL,
    Salary SMALLMONEY NULL,
    PRIMARY KEY (ID)
);

CREATE TABLE Departments
(
    DepID INT NOT NULL,
    Name NVARCHAR(50) NOT NULL,
    Location NVARCHAR(50) NULL,
    PRIMARY KEY (DepID)
);
```

---

## Column Names: `FirstName`, `[FirstName]`, or `"FirstName"`?

SQL Server allows several ways to write column names.

### 1. Without brackets or quotes — Best for normal names

```sql
FirstName NVARCHAR(25)
```

This is the **preferred style** when the name is simple and valid.

Examples:

```sql
FirstName
LastName
Phone
Salary
DateOfBirth
```

### 2. Square brackets `[ ]`

```sql
[FirstName] NVARCHAR(25)
```

This works, but the brackets are unnecessary for a simple name like `FirstName`.

Brackets are useful when a column name contains spaces or conflicts with a SQL keyword:

```sql
[First Name] NVARCHAR(25)
[Order] INT
```

### 3. Double quotes `" "`

```sql
"FirstName" NVARCHAR(25)
```

SQL Server can also use double quotes for identifiers when `QUOTED_IDENTIFIER` is enabled.

Example:

```sql
"First Name" NVARCHAR(25)
```

For normal SQL Server code, square brackets are more commonly used when an identifier really needs delimiters.

---

## Best Practice

Prefer simple names that need **no brackets and no quotes**:

```sql
FirstName
MiddleName
LastName
```

Instead of:

```sql
[First Name]
"Middle Name"
[Last Name]
```

So this is cleaner:

```sql
FirstName NVARCHAR(25)
```

than:

```sql
[FirstName] NVARCHAR(25)
```

or:

```sql
"FirstName" NVARCHAR(25)
```

## Remember

- ✅ `FirstName` → best for normal column names
- ✅ `[First Name]` → useful if the name contains spaces or a reserved word
- ✅ `"First Name"` → also possible in SQL Server when quoted identifiers are enabled
- ⚠️ Best practice: avoid spaces and reserved SQL words in column names
