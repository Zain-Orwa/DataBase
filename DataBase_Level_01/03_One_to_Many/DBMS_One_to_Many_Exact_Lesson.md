# One to Many to Relational Schema

> This study sheet follows the same order and structure as the lesson:
> **ERD → Relational Schema → Employees table → Departments table → explanation → wrong way.**

---

# ERD


### Crow's-foot cardinality in the ERD

```text
Employee   0..M  ───── Works In ─────  1..1   Department
              o<                         ||
```

- `o<` = **zero or many employees**
- `||` = **exactly one department**

---

# Relational Schema


```text
Employee                               Department

┌────┬──────────────────┐             ┌────┬───────────────┐
│    │ Employee         │             │    │ Department    │
├────┼──────────────────┤             ├────┼───────────────┤
│ PK │ EmpID            │             │ PK │ DeptID        │
│    │ FirstName        │             │    │ Name          │
│    │ LastName         │             └────┴───────────────┘
│    │ BirthDate        │                    │
│ FK │ DeptID           │  o<────────────── │
│    │ StartDate        │
└────┴──────────────────┘
        MANY                           ONE
```

---

# Employees

```text
┌─────────┬────────────┬─────────────┬────────────┬──────────┬────────────┐
│ EmpID   │ FirstName  │ LastName    │ BirthDate  │ DeptID   │ StartDate  │
├─────────┼────────────┼─────────────┼────────────┼──────────┼────────────┤
│ 1       │ Mohammed   │ Abu-Hadhoud │ 6-11-1977  │ 1        │ 1/1/2010   │
│ 2       │ Ali        │ Amjad       │ 12-3-2000  │ 1        │ 2/2/2022   │
│ 3       │ Maha       │ Omaran      │ 11-6-2003  │ 2        │ 3/3/2021   │
│ 4       │ Fidaa      │ Safwan      │ 6-6-1991   │ 4        │ 4/4/2014   │
└─────────┴────────────┴─────────────┴────────────┴──────────┴────────────┘
```

- `EmpID` = **Primary Key**
- `DeptID` = **Foreign Key**

---

# Departments

```text
┌──────────┬────────────┐
│ DeptID   │ Name       │
├──────────┼────────────┤
│ 1        │ IT         │
│ 2        │ Finance    │
│ 3        │ Marketing  │
│ 4        │ Sales      │
└──────────┴────────────┘
```

- `DeptID` = **Primary Key**

---

# How the lesson converts the ERD to the Relational Schema

## 1. Create a table for each entity with its attributes

From the ERD:

```text
Employee                               Department
────────                               ──────────
EmpID                                  DeptID
FirstName                              Name
LastName
BirthDate
```

So we create:

```text
Employee table
Department table
```

## 2. Take the Primary Key from the "ONE" side and put it as a Foreign Key in the "MANY" side

```text
            ONE SIDE                          MANY SIDE

          Department                         Employee
        ┌─────────────┐                    ┌─────────────┐
        │ PK DeptID   │ ─────────────────► │ FK DeptID   │
        │ Name        │                    │ EmpID       │
        └─────────────┘                    │ FirstName   │
                                           │ LastName    │
                                           │ BirthDate   │
                                           │ StartDate   │
                                           └─────────────┘
```

The lesson's rule is:

> **We take the primary key from the "one" side and put it as foreign key in the "many" side.**

---

# Why does `DeptID` go inside `Employee`?

Look at the data:

```text
Employee                            DeptID
──────────────────────────────────────────
Mohammed                              1
Ali                                   1
Maha                                  2
Fidaa                                 4
```

And then look at `Department`:

```text
DeptID        Name
────────────────────
1             IT
2             Finance
3             Marketing
4             Sales
```

Therefore:

```text
Mohammed ─── DeptID 1 ──┐
                         ├────► IT
Ali ──────── DeptID 1 ──┘

Maha ─────── DeptID 2 ───────► Finance

Fidaa ────── DeptID 4 ───────► Sales
```

The employee row only needs the department's key.

---

# ✅ Correct Way

```text
DEPARTMENT
┌──────────┬────────────┐
│ DeptID   │ Name       │
├──────────┼────────────┤
│ 1        │ IT         │
│ 2        │ Finance    │
│ 3        │ Marketing  │
│ 4        │ Sales      │
└──────────┴────────────┘


EMPLOYEE
┌─────────┬────────────┬──────────┐
│ EmpID   │ FirstName  │ DeptID   │
├─────────┼────────────┼──────────┤
│ 1       │ Mohammed   │ 1        │
│ 2       │ Ali        │ 1        │
│ 3       │ Maha       │ 2        │
│ 4       │ Fidaa      │ 4        │
└─────────┴────────────┴──────────┘
```

Here:

```text
DeptID = 1
```

means:

```text
IT
```

The word `IT` is stored once in `Department`.

---

# ❌ Wrong Way — Put Department Data in the MANY Side

Suppose we do this instead:

```text
EMPLOYEE
┌─────────┬────────────┬────────────────┬────────────────────┐
│ EmpID   │ FirstName  │ DepartmentName │ DepartmentLocation │
├─────────┼────────────┼────────────────┼────────────────────┤
│ 1       │ Mohammed   │ 🔴 IT          │ 🔴 Vienna          │
│ 2       │ Ali        │ 🔴 IT          │ 🔴 Vienna          │
│ 3       │ Sara       │ 🔴 IT          │ 🔴 Vienna          │
│ 4       │ Adam       │ 🔴 IT          │ 🔴 Vienna          │
└─────────┴────────────┴────────────────┴────────────────────┘
```

The redundancy is here:

```text
                           SAME VALUES REPEATED
                                  ↓
Mohammed  ───────────────►  🔴 IT   🔴 Vienna
Ali       ───────────────►  🔴 IT   🔴 Vienna
Sara      ───────────────►  🔴 IT   🔴 Vienna
Adam      ───────────────►  🔴 IT   🔴 Vienna
                                  ↑
                             REDUNDANCY
```

Instead of storing the department once, we repeat the department information for every employee.

---

# Why the Wrong Way Is a Problem

Suppose the IT department moves:

```text
Vienna  →  Krems
```

If department data is repeated in `Employee`, we might get:

```text
┌────────────┬────────────┬────────────┐
│ Employee   │ Department │ Location   │
├────────────┼────────────┼────────────┤
│ Mohammed   │ IT         │ Krems      │
│ Ali        │ IT         │ Krems      │
│ Sara       │ IT         │ Vienna  ❌  │
│ Adam       │ IT         │ Krems      │
└────────────┴────────────┴────────────┘
```

Now:

```text
IT = Krems
IT = Vienna
```

The database contains conflicting values.

---

# ✅ With the Correct Design

We change only one row:

```text
DEPARTMENT
┌──────────┬────────────┬────────────┐
│ DeptID   │ Name       │ Location   │
├──────────┼────────────┼────────────┤
│ 1        │ IT         │ Krems      │
│ 2        │ Finance    │ Vienna     │
│ 3        │ Marketing  │ Vienna     │
│ 4        │ Sales      │ Vienna     │
└──────────┴────────────┴────────────┘
```

The employee records still contain only:

```text
Mohammed   → DeptID 1
Ali        → DeptID 1
Sara       → DeptID 1
Adam       → DeptID 1
```

All four point to the same department record:

```text
                 ┌─────────────────────┐
                 │ DeptID = 1          │
                 │ Name   = IT         │
                 │ Location = Krems    │
                 └──────────┬──────────┘
                            │
                   ┌────────┼────────┐
                   │        │        │
                   ▼        ▼        ▼
               Mohammed    Ali      Sara ...
```

---

# Final Rule to Remember

```text
                 ONE                           MANY

            Department                     Employee
         ┌──────────────┐                ┌──────────────┐
         │ PK DeptID    │───────────────►│ FK DeptID    │
         │ Name         │                │ PK EmpID     │
         └──────────────┘                │ FirstName    │
                                         │ LastName     │
                                         │ BirthDate    │
                                         │ StartDate    │
                                         └──────────────┘
```

> **For a 1:M relationship, take the Primary Key from the ONE side and place it as a Foreign Key in the MANY side.**
