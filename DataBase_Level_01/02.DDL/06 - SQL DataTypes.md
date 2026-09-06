# 06 - SQL DataTypes

> **SQL Server / T-SQL**
>
> A **data type** defines what kind of value a column can store, how much space it may use, and what operations SQL Server can perform on it.

---

## 1. Main SQL Server Data Type Categories

```text
SQL Server Data Types
│
├── Exact numerics
├── Approximate numerics
├── Character strings
├── Unicode character strings
├── Date & time
├── Binary strings
└── Other / specialized types
```

---

# 2. Exact Numeric Data Types

Use these when the value must be stored **exactly**.

| Data Type      | What it stores             | Example column           | Real-world example           |
| -------------- | -------------------------- | ------------------------ | ---------------------------- |
| `TINYINT`      | Whole number from 0 to 255 | `Rating TINYINT`         | Rating `1`–`5`               |
| `SMALLINT`     | Small whole numbers        | `RoomNumber SMALLINT`    | Hotel room `412`             |
| `INT`          | Normal whole numbers       | `EmployeeID INT`         | Employee ID `10542`          |
| `BIGINT`       | Very large whole numbers   | `TransactionID BIGINT`   | Billions of transactions     |
| `BIT`          | `0`, `1`, or `NULL`        | `IsActive BIT`           | Active = `1`, inactive = `0` |
| `DECIMAL(p,s)` | Exact decimal number       | `Price DECIMAL(10,2)`    | `1499.99`                    |
| `NUMERIC(p,s)` | Same as `DECIMAL`          | `Weight NUMERIC(8,2)`    | `72.45` kg                   |
| `MONEY`        | Monetary value             | `Balance MONEY`          | `12500.50`                   |
| `SMALLMONEY`   | Smaller monetary range     | `DailyBudget SMALLMONEY` | `250.75`                     |

### Examples

```sql
Age TINYINT
EmployeeID INT
TotalViews BIGINT
IsActive BIT
Price DECIMAL(10,2)
```

### `DECIMAL(p,s)`

```sql
Price DECIMAL(10,2)
```

- `p` = **precision** → total number of digits
- `s` = **scale** → digits after the decimal point

Example:

```text
12345678.90
```

has 10 total digits and 2 digits after the decimal point.

### Best practice for money

Although SQL Server has `MONEY` and `SMALLMONEY`, for new financial/business calculations prefer a suitable `DECIMAL`, for example:

```sql
Price DECIMAL(12,2)
```

or when more fractional precision is needed:

```sql
ExchangeRate DECIMAL(18,6)
```

---

# 3. Approximate Numeric Data Types

Use these for **floating-point values** where a tiny approximation is acceptable.

| Data Type |                Approx. precision | Example column     | Real-world example       |
| --------- | -------------------------------: | ------------------ | ------------------------ |
| `REAL`    |                   about 7 digits | `Temperature REAL` | Sensor reading `21.7834` |
| `FLOAT`   | up to about 15 digits by default | `Distance FLOAT`   | Scientific measurement   |

### Example

```sql
Temperature REAL,
ScientificValue FLOAT
```

### Real-world idea

A weather sensor reading can tolerate a tiny floating-point approximation:

```sql
Temperature FLOAT
```

But a bank balance should normally be exact:

```sql
Balance DECIMAL(18,2)
```

**Do not use `FLOAT` or `REAL` when exact equality or exact financial arithmetic matters.**

---

# 4. Character String Data Types

These store **non-Unicode / code-page text**.

| Data Type      | Meaning              | Example column             | Real-world example         |
| -------------- | -------------------- | -------------------------- | -------------------------- |
| `CHAR(n)`      | Fixed-length text    | `CountryCode CHAR(2)`      | `AT`, `DE`, `FR`           |
| `VARCHAR(n)`   | Variable-length text | `Email VARCHAR(150)`       | `zain@example.com`         |
| `VARCHAR(MAX)` | Very large text      | `Description VARCHAR(MAX)` | Long article / description |
| `TEXT`         | Old large-text type  | —                          | Legacy databases           |

### `CHAR` vs `VARCHAR`

Use `CHAR` when the length is always fixed:

```sql
CountryCode CHAR(2)
```

Use `VARCHAR` when the length changes:

```sql
Email VARCHAR(150)
```

Example values:

```text
AT
zain@example.com
```

---

# 5. Unicode Character String Data Types

Unicode types are designed for text from many languages.

| Data Type       | Meaning                      | Example column           | Real-world example            |
| --------------- | ---------------------------- | ------------------------ | ----------------------------- |
| `NCHAR(n)`      | Fixed-length Unicode text    | `LanguageCode NCHAR(2)`  | `ar`, `de`                    |
| `NVARCHAR(n)`   | Variable-length Unicode text | `FullName NVARCHAR(100)` | English, Arabic, German names |
| `NVARCHAR(MAX)` | Very large Unicode text      | `Article NVARCHAR(MAX)`  | Multilingual article          |
| `NTEXT`         | Old large Unicode type       | —                        | Legacy databases              |

### Example

```sql
FullName NVARCHAR(100)
```

This is a good choice when names may contain characters from different languages.

Example:

```sql
INSERT INTO People (FullName)
VALUES (N'محمد أحمد');
```

### `VARCHAR` vs `NVARCHAR`

```text
VARCHAR   → normal character data / depends on collation
NVARCHAR  → Unicode text
```

For beginner projects where multilingual text may appear, `NVARCHAR` is often the easier safe choice.

---

# 6. Date & Time Data Types

| Data Type        | Stores                        | Example column               | Real-world example        |
| ---------------- | ----------------------------- | ---------------------------- | ------------------------- |
| `DATE`           | Date only                     | `BirthDate DATE`             | `2000-05-21`              |
| `TIME`           | Time only                     | `OpeningTime TIME`           | `08:30:00`                |
| `DATETIME2`      | Date + time                   | `CreatedAt DATETIME2`        | Order creation timestamp  |
| `DATETIMEOFFSET` | Date + time + UTC offset      | `MeetingTime DATETIMEOFFSET` | International meeting     |
| `DATETIME`       | Older date + time type        | `CreatedAt DATETIME`         | Legacy application        |
| `SMALLDATETIME`  | Date + time, minute precision | `Appointment SMALLDATETIME`  | Simple appointment system |

### Examples

```sql
BirthDate DATE,
OpeningTime TIME,
CreatedAt DATETIME2,
MeetingTime DATETIMEOFFSET
```

### Real-world examples

```text
BirthDate   → 1998-10-14
OpeningTime → 08:30:00
CreatedAt   → 2026-09-06 17:30:15
```

### Best choice for new applications

For most new SQL Server applications:

```sql
DATE
TIME
DATETIME2
DATETIMEOFFSET
```

are generally preferable to older `DATETIME` / `SMALLDATETIME` when their capabilities match your needs.

---

# 7. Binary String Data Types

Binary types store **bytes**, not normal readable text.

| Data Type        | Meaning               | Example column             | Real-world example      |
| ---------------- | --------------------- | -------------------------- | ----------------------- |
| `BINARY(n)`      | Fixed-length bytes    | `Hash BINARY(32)`          | Fixed-size SHA-256 hash |
| `VARBINARY(n)`   | Variable-length bytes | `Signature VARBINARY(512)` | Digital signature       |
| `VARBINARY(MAX)` | Large binary data     | `FileData VARBINARY(MAX)`  | PDF/image/file bytes    |
| `IMAGE`          | Old large binary type | —                          | Legacy image storage    |

### Examples

```sql
PasswordHash BINARY(32),
DocumentData VARBINARY(MAX)
```

---

# 8. Other / Specialized SQL Server Data Types

These are useful for more specialized problems.

| Data Type          | What it is used for                       | Real-world example                |
| ------------------ | ----------------------------------------- | --------------------------------- |
| `UNIQUEIDENTIFIER` | GUID / globally unique identifier         | Distributed order ID              |
| `ROWVERSION`       | Automatically generated row-version value | Detect whether a row changed      |
| `HIERARCHYID`      | Position in a hierarchy/tree              | Company organization tree         |
| `XML`              | XML documents/data                        | XML configuration or message      |
| `SQL_VARIANT`      | Can store values of several SQL types     | Rare mixed-type metadata          |
| `GEOMETRY`         | Flat-coordinate spatial data              | Floor plan / local map            |
| `GEOGRAPHY`        | Earth-based spatial data                  | GPS latitude/longitude            |
| `CURSOR`           | Reference to a cursor                     | Stored-procedure processing       |
| `TABLE`            | Table-valued variable/type                | Temporary rows inside T-SQL logic |

---

## `UNIQUEIDENTIFIER` Example

```sql
UserID UNIQUEIDENTIFIER
```

A real value could look like:

```text
6F9619FF-8B86-D011-B42D-00C04FC964FF
```

It is useful when IDs must remain unique across different systems.

---

## `ROWVERSION` Example

```sql
Version ROWVERSION
```

Real-world use:

```text
User A opens a customer record.
User B changes it.
ROWVERSION helps the application detect that the row changed
before User A overwrites User B's update.
```

---

## `HIERARCHYID` Example

Think of a company:

```text
CEO
├── IT Manager
│   ├── Developer
│   └── DBA
└── Sales Manager
```

`HIERARCHYID` can represent positions inside that tree.

---

## `GEOGRAPHY` Example

```sql
Location GEOGRAPHY
```

Real-world use:

```text
Store location
Delivery location
GPS position
```

---

## `GEOMETRY` Example

```sql
Shape GEOMETRY
```

Real-world use:

```text
Room layout
Building floor plan
2D engineering drawing
```

---

# 9. Deprecated Types — Important

Avoid these in **new SQL Server development**:

```text
TEXT
NTEXT
IMAGE
```

Use:

```text
TEXT   → VARCHAR(MAX)
NTEXT  → NVARCHAR(MAX)
IMAGE  → VARBINARY(MAX)
```

---

# 10. One Realistic Table Using Different Data Types

Imagine an online store:

```sql
CREATE TABLE Customers
(
    CustomerID BIGINT NOT NULL,
    CustomerGuid UNIQUEIDENTIFIER NOT NULL,
    FullName NVARCHAR(100) NOT NULL,
    Email VARCHAR(150) NOT NULL,
    CountryCode CHAR(2) NOT NULL,
    BirthDate DATE NULL,
    CreditLimit DECIMAL(12,2) NULL,
    IsActive BIT NOT NULL,
    CreatedAt DATETIME2 NOT NULL,
    ProfileImage VARBINARY(MAX) NULL,
    Version ROWVERSION,

    PRIMARY KEY (CustomerID)
);
```

### Why each type?

| Column         | Type               | Why?                                         |
| -------------- | ------------------ | -------------------------------------------- |
| `CustomerID`   | `BIGINT`           | Can support a very large number of customers |
| `CustomerGuid` | `UNIQUEIDENTIFIER` | Unique ID across systems                     |
| `FullName`     | `NVARCHAR(100)`    | Supports multilingual names                  |
| `Email`        | `VARCHAR(150)`     | Variable-length text                         |
| `CountryCode`  | `CHAR(2)`          | Always exactly 2 characters                  |
| `BirthDate`    | `DATE`             | No time is needed                            |
| `CreditLimit`  | `DECIMAL(12,2)`    | Exact monetary number                        |
| `IsActive`     | `BIT`              | Yes / No                                     |
| `CreatedAt`    | `DATETIME2`        | Precise date and time                        |
| `ProfileImage` | `VARBINARY(MAX)`   | Binary file data                             |
| `Version`      | `ROWVERSION`       | Detect row changes                           |

---

# 11. Quick Choosing Guide

```text
Whole number?
    → INT
    → BIGINT if INT is too small

Exact decimal / money calculation?
    → DECIMAL(p,s)

Approximate scientific measurement?
    → FLOAT / REAL

Normal variable text?
    → VARCHAR(n)

Multilingual text?
    → NVARCHAR(n)

Fixed-length code?
    → CHAR(n) / NCHAR(n)

Date only?
    → DATE

Time only?
    → TIME

Date + time?
    → DATETIME2

Date + time + UTC offset?
    → DATETIMEOFFSET

True / False?
    → BIT

Binary file / bytes?
    → VARBINARY(MAX)

Globally unique ID?
    → UNIQUEIDENTIFIER

GPS / Earth coordinates?
    → GEOGRAPHY
```

---

# 12. Corrections / Important Notes From the Course Material

A few details in older diagrams/tables are easy to copy incorrectly:

1. `REAL` is approximately **7 digits** of precision.
2. SQL Server `FLOAT` with the default precision is approximately **15 digits**.
3. `SMALLINT` ranges from **-32,768 to 32,767**.
4. `TEXT`, `NTEXT`, and `IMAGE` are legacy/deprecated types for new development.
5. For financial calculations, prefer an appropriate `DECIMAL(p,s)` rather than relying on floating-point types.

---

# 13. Core Rule to Remember

> Choose the **smallest appropriate type that correctly represents the real-world value**, but never sacrifice correctness just to save a few bytes.

Examples:

```sql
Age TINYINT
EmployeeID INT
Price DECIMAL(10,2)
Name NVARCHAR(100)
BirthDate DATE
CreatedAt DATETIME2
IsActive BIT
```

---

## References

- Microsoft Learn — Data types (Transact-SQL):  
  https://learn.microsoft.com/sql/t-sql/data-types/data-types-transact-sql

- Microsoft Learn — int, bigint, smallint, tinyint:  
  https://learn.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql

- Microsoft Learn — decimal and numeric:  
  https://learn.microsoft.com/sql/t-sql/data-types/decimal-and-numeric-transact-sql

- Microsoft Learn — float and real:  
  https://learn.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql

- Microsoft Learn — ntext, text, and image:  
  https://learn.microsoft.com/sql/t-sql/data-types/ntext-text-and-image-transact-sql
