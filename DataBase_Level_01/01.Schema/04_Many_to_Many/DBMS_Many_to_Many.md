# DBMS — Many-to-Many Relationship (M:N)
## Student ↔ Course → Enrollment

This lesson follows the class example in a clean order:

1. Read the ERD
2. Understand why M:N needs another relation
3. Convert the M:N relationship into a relational schema
4. Place relationship attributes
5. Compare composite PK vs `EnrollmentID`
6. Understand retakes
7. Understand `NULL` vs `0`
8. Review the final tables and mapping

---

# 1. ERD

```text
                    ┌─────────────┐
      StudentID ────│   Student   │
                    │             │──── FirstName
                    │             │──── LastName
                    │             │──── BirthDate
                    │             │──── Age (derived)
                    │             │──── Phone (multivalued)
                    └──────┬──────┘
                           │
                          o<
                           │
                      ◇ Enrolled ◇
                       /         \
                 EnrollDate     Grade
                           │
                          >o
                           │
                    ┌──────┴──────┐
        CourseID ───│    Course   │
                    │             │──── Name
                    │             │──── CreditHours
                    └─────────────┘
```

### Cardinality

```text
Student  0..M  ───── Enrolled ─────  0..M  Course
            o<                           >o
```

Meaning:

```text
One Student can enroll in many Courses.
One Course can have many Students.
```

This is a **many-to-many relationship**.

---

# 2. Why can't we keep only Student and Course?

## Why not put `CourseID` directly inside Student?

Because one Student can take many Courses.

If Maha takes Course 2 and Course 4, one Student row would somehow need:

```text
StudentID = 3
CourseID  = 2, 4, ...
```

That is not a clean relational design.

Another bad solution would be repeating Maha:

```text
❌ WRONG

┌───────────┬───────────┬──────────┬──────────┐
│ StudentID │ FirstName │ LastName │ CourseID │
├───────────┼───────────┼──────────┼──────────┤
│ 3         │ Maha      │ Omaran   │ 2        │
│ 3         │ Maha      │ Omaran   │ 4        │
└───────────┴───────────┴──────────┴──────────┘
              ↑
          repeated data
```

This creates redundancy.

## Why not put `StudentID` directly inside Course?

The same problem appears in the opposite direction because one Course can contain many Students.

So M:N cannot be represented cleanly by putting one foreign key in only one of the two entity tables.

---

# 3. Rule for converting M:N to a relational schema

For:

```text
Entity A  M ───────── N  Entity B
```

create another relation/table for the relationship:

```text
Entity A  1 ─────< M  Relationship  M >───── 1  Entity B
```

For this example:

```text
Student  M ───── Enrolled ───── N  Course
```

becomes:

```text
Student  1 ─────< M  Enrollment  M >───── 1  Course
```

So one M:N relationship becomes **two 1:M relationships**.

---

# 4. Relational Schema

```text
┌────────────────────┐
│ Student            │
├────────────────────┤
│ PK StudentID       │
│ FirstName          │
│ LastName           │
│ BirthDate          │
└─────────┬──────────┘
          │ 1
          │
          │ M
          ▼
┌────────────────────┐
│ Enrollment         │
├────────────────────┤
│ PK EnrollmentID    │
│ FK StudentID       │
│ FK CourseID        │
│ EnrollDate         │
│ Grade              │
└─────────┬──────────┘
          │ M
          │
          │ 1
          ▼
┌────────────────────┐
│ Course             │
├────────────────────┤
│ PK CourseID        │
│ Name               │
│ CreditHours        │
└────────────────────┘
```

Mapping:

```text
Student.StudentID
        │
        └──────────────► Enrollment.StudentID (FK)

Course.CourseID
        │
        └──────────────► Enrollment.CourseID (FK)
```

---

# 5. Why do both foreign keys go into Enrollment?

Every Enrollment row must answer:

```text
WHO enrolled?
        ↓
StudentID

IN WHICH course?
        ↓
CourseID
```

Example:

```text
StudentID = 3
CourseID  = 4
```

means:

```text
Maha enrolled in Marketing.
```

The bridge table connects one Student row to one Course row.

---

# 6. Why do `EnrollDate` and `Grade` belong in Enrollment?

## Why not put `Grade` in Student?

Because a Student can have different grades in different Courses:

```text
Maha:
Database   → 50
Marketing  → 45
C#         → 80
```

So `Grade` describes a particular Student-Course enrollment.

## Why not put `EnrollDate` in Course?

Because different Students can enroll in the same Course on different dates.

Therefore:

```text
EnrollDate
Grade
```

belong to:

```text
Enrollment
```

They are attributes of the relationship itself.

---

# 7. Why does Enrollment reduce redundancy?

Without Enrollment, a retake could force repeated Student and Course data:

```text
❌ WRONG

┌───────────┬───────────┬──────────┬───────────┬────────────┬───────┐
│ StudentID │ FirstName │ LastName │ Course    │ EnrollDate │ Grade │
├───────────┼───────────┼──────────┼───────────┼────────────┼───────┤
│ 3         │ Maha      │ Omaran   │ Marketing │ 15/1/2021  │ 45    │
│ 3         │ Maha      │ Omaran   │ Marketing │ 25/6/2022  │ NULL  │
└───────────┴───────────┴──────────┴───────────┴────────────┴───────┘
              ↑          ↑              ↑
              └──────── repeated data ──┘
```

With Enrollment:

```text
STUDENT
┌───────────┬───────────┬──────────┬────────────┐
│ StudentID │ FirstName │ LastName │ BirthDate  │
├───────────┼───────────┼──────────┼────────────┤
│ 3         │ Maha      │ Omaran   │ 11-6-2003  │
└───────────┴───────────┴──────────┴────────────┘
              stored once
```

```text
ENROLLMENT
┌──────────────┬───────────┬──────────┬────────────┬───────┐
│ EnrollmentID │ StudentID │ CourseID │ EnrollDate │ Grade │
├──────────────┼───────────┼──────────┼────────────┼───────┤
│ 4            │ 3         │ 4        │ 15/1/2021  │ 45    │
│ 5            │ 3         │ 4        │ 25/6/2022  │ NULL  │
└──────────────┴───────────┴──────────┴────────────┴───────┘
```

The Student data is stored once.

The Course data is stored once.

Only the new enrollment event is added.

---

# 8. Primary-key design — two cases

## CASE A — Composite Primary Key

We could define:

```text
PRIMARY KEY (StudentID, CourseID)
```

Example:

```text
┌───────────┬──────────┬────────────┬───────┐
│ StudentID │ CourseID │ EnrollDate │ Grade │
├───────────┼──────────┼────────────┼───────┤
│ 3         │ 4        │ 15/1/2021  │ 45    │
└───────────┴──────────┴────────────┴───────┘
```

The pair:

```text
(StudentID = 3, CourseID = 4)
```

must be unique.

---

# 9. Why can `(StudentID, CourseID)` be too limiting?

Suppose Maha wants to retake Course 4.

First attempt:

```text
(StudentID, CourseID) = (3, 4)
```

Second attempt:

```text
(StudentID, CourseID) = (3, 4)
```

Both rows have the same composite primary key.

```text
❌ Duplicate primary key
```

So the second enrollment would be rejected.

Diagram:

```text
COMPOSITE PK = (StudentID, CourseID)

Attempt 1:
┌───────────┬──────────┐
│ StudentID │ CourseID │
├───────────┼──────────┤
│ 3         │ 4        │  ← unique
└───────────┴──────────┘

Attempt 2:
┌───────────┬──────────┐
│ StudentID │ CourseID │
├───────────┼──────────┤
│ 3         │ 4        │  ← duplicate PK ❌
└───────────┴──────────┘
```

This design enforces:

> A Student may enroll in a specific Course only once.

That is too restrictive if retakes are allowed.

---

# 10. CASE B — Separate `EnrollmentID` primary key

Use:

```text
EnrollmentID = PK
StudentID    = FK
CourseID     = FK
```

Now the same Student can take the same Course again:

```text
┌──────────────┬───────────┬──────────┬────────────┬───────┐
│ EnrollmentID │ StudentID │ CourseID │ EnrollDate │ Grade │
├──────────────┼───────────┼──────────┼────────────┼───────┤
│ 4            │ 3         │ 4        │ 15/1/2021  │ 45    │
│ 5            │ 3         │ 4        │ 25/6/2022  │ NULL  │
└──────────────┴───────────┴──────────┴────────────┴───────┘
       ↑
       └── each enrollment has its own identity
```

The pair:

```text
StudentID = 3
CourseID  = 4
```

may appear more than once because those two columns are now foreign keys, not the whole primary key.

The rows remain unique because:

```text
EnrollmentID 4 ≠ EnrollmentID 5
```

---

# 11. Important point about composite primary keys

A composite primary key is **not automatically wrong**.

It is valid when the business rule is:

```text
A Student may take each Course only once.
```

But our requirement is different:

```text
retakes
re-enrollment
multiple attempts
history
```

So for this example:

```text
PRIMARY KEY (StudentID, CourseID)
```

is too restrictive.

A separate:

```text
EnrollmentID
```

is a better fit.

Another technically valid design could be:

```text
PRIMARY KEY (StudentID, CourseID, AttemptNo)
```

But `EnrollmentID` is simpler to reference and extend.

---

# 12. Why is retaking a Course easy now?

Suppose Maha fails Marketing:

```text
EnrollmentID = 4
StudentID    = 3
CourseID     = 4
Grade        = 45
```

Later she retakes it:

```text
EnrollmentID = 5
StudentID    = 3
CourseID     = 4
Grade        = NULL
```

Diagram:

```text
Maha (StudentID 3)
        │
        ├──── EnrollmentID 4 ─── CourseID 4 ─── Grade 45
        │
        └──── EnrollmentID 5 ─── CourseID 4 ─── Grade NULL
```

No Student row is duplicated.

No Course row is duplicated.

Only a new Enrollment row is created.

---

# 13. Another reason for Enrollment — history

Enrollment is not only a technical bridge.

It represents a real business event:

```text
"A Student enrolled in a Course."
```

That event can have its own information:

```text
EnrollmentID
StudentID
CourseID
EnrollDate
Grade
AttemptNo
Status
Semester
Pass/Fail
CompletionDate
```

So the relationship table gives us a history of every enrollment attempt.

Example:

```text
Maha
 │
 ├── Marketing — Attempt 1 — Grade 45   — Failed
 │
 └── Marketing — Attempt 2 — Grade NULL — In progress
```

---

# 14. `NULL` vs `0` for an unknown Grade

If the new attempt has not been graded yet:

```text
✅ Grade = NULL
```

Do not use:

```text
❌ Grade = 0
```

because they mean different things:

```text
NULL = no known grade yet / not graded
0    = the Student actually received a grade of zero
```

---

# 15. Why can `0` give wrong statistics?

Suppose the known grades are:

```text
45
50
80
95
```

and one enrollment has no Grade yet.

Correct:

```text
45, 50, 80, 95, NULL
```

Ignoring the unknown value:

```text
Median = (50 + 80) / 2 = 65
```

If we incorrectly store `0`:

```text
0, 45, 50, 80, 95
```

then:

```text
Median = 50
```

So:

```text
Correct median = 65
Wrong median   = 50
```

A fake zero can also distort:

```text
AVG()
MIN()
median
pass/fail statistics
reports
```

Use `NULL` when the value is unknown or does not exist yet.

---

# 16. Final Students table

```text
┌───────────┬────────────┬─────────────┬────────────┐
│ StudentID │ FirstName  │ LastName    │ BirthDate  │
├───────────┼────────────┼─────────────┼────────────┤
│ 1         │ Mohammed   │ Abu-Hadhoud │ 6-11-1977  │
│ 2         │ Ali        │ Amjad       │ 12-3-2000  │
│ 3         │ Maha       │ Omaran      │ 11-6-2003  │
│ 4         │ Fidaa      │ Safwan      │ 6-6-1991   │
└───────────┴────────────┴─────────────┴────────────┘
```

```text
StudentID = PK
```

---

# 17. Final Courses table

```text
┌──────────┬────────────┬─────────────┐
│ CourseID │ Name       │ CreditHours │
├──────────┼────────────┼─────────────┤
│ 1        │ OOP        │ 3           │
│ 2        │ Database   │ 3           │
│ 3        │ C#         │ 3           │
│ 4        │ Marketing  │ 1           │
└──────────┴────────────┴─────────────┘
```

```text
CourseID = PK
```

---

# 18. Final Enrollment table

```text
┌──────────────┬───────────┬──────────┬────────────┬───────┐
│ EnrollmentID │ StudentID │ CourseID │ EnrollDate │ Grade │
├──────────────┼───────────┼──────────┼────────────┼───────┤
│ 1            │ 1         │ 2        │ 1/1/2000   │ 95    │
│ 2            │ 1         │ 3        │ 20/1/2005  │ 80    │
│ 3            │ 2         │ 2        │ 5/5/2022   │ 50    │
│ 4            │ 3         │ 4        │ 15/1/2021  │ 45    │
│ 5            │ 3         │ 4        │ 25/6/2022  │ NULL  │
└──────────────┴───────────┴──────────┴────────────┴───────┘
```

Keys:

```text
EnrollmentID = PK
StudentID    = FK → Student.StudentID
CourseID     = FK → Course.CourseID
```

Rows 4 and 5 demonstrate a retake:

```text
StudentID 3 + CourseID 4
StudentID 3 + CourseID 4
```

Same Student.

Same Course.

Different enrollment attempts.

---

# 19. What about the `Phone` attribute in the ERD?

`Phone` is shown as a multivalued attribute.

One Student can have multiple phone numbers.

Instead of:

```text
Phone1
Phone2
Phone3
...
```

inside Student, create another table:

```text
┌────────────────────┐
│ Student            │
├────────────────────┤
│ PK StudentID       │
│ FirstName          │
│ LastName           │
│ BirthDate          │
└─────────┬──────────┘
          │ 1
          │
          │ M
          ▼
┌────────────────────┐
│ Phone              │
├────────────────────┤
│ PK PhoneID         │
│ FK StudentID       │
│ Phone              │
└────────────────────┘
```

So:

```text
Student 1 ─────< M Phone
```

This is separate from the Student-Course M:N relationship.

---

# 20. Full final relational schema

```text
                         ┌────────────────────┐
                         │ Phone              │
                         ├────────────────────┤
                         │ PK PhoneID         │
                         │ FK StudentID       │
                         │ Phone              │
                         └─────────▲──────────┘
                                   │ M
                                   │
                                   │ 1
┌────────────────────┐             │
│ Student            │─────────────┘
├────────────────────┤
│ PK StudentID       │
│ FirstName          │
│ LastName           │
│ BirthDate          │
└─────────┬──────────┘
          │ 1
          │
          │ M
          ▼
┌────────────────────┐
│ Enrollment         │
├────────────────────┤
│ PK EnrollmentID    │
│ FK StudentID       │
│ FK CourseID        │
│ EnrollDate         │
│ Grade              │
└─────────┬──────────┘
          │ M
          │
          │ 1
          ▼
┌────────────────────┐
│ Course             │
├────────────────────┤
│ PK CourseID        │
│ Name               │
│ CreditHours        │
└────────────────────┘
```

---

# 21. Complete mapping

```text
ERD
────────────────────────────────────────────────────

Student  M ───────── Enrolled ───────── N  Course
                         │
                   EnrollDate
                      Grade


                    ↓ MAPPING ↓


RELATIONAL SCHEMA
────────────────────────────────────────────────────

Student
   │
   │ 1
   │
   │ M
   ▼
Enrollment
   ▲
   │ M
   │
   │ 1
Course
```

The M:N relationship becomes:

```text
Enrollment
```

It receives:

```text
StudentID  ← Student PK becomes FK
CourseID   ← Course PK becomes FK
```

plus relationship attributes:

```text
EnrollDate
Grade
```

and in this design:

```text
EnrollmentID ← new PK
```

---

# 22. Why-question summary

## Why create Enrollment?

Because Student and Course have an M:N relationship, and the bridge table represents each Student-Course association as its own row.

## Why put `StudentID` and `CourseID` in Enrollment?

Because every Enrollment must identify which Student and which Course are connected.

## Why are they foreign keys?

Because they reference the primary keys of Student and Course.

## Why not repeat Student or Course information?

Because that creates redundancy and update problems.

## Why put `Grade` in Enrollment?

Because a Grade belongs to one Student taking one Course.

## Why put `EnrollDate` in Enrollment?

Because the date describes the enrollment event.

## Why not use `(StudentID, CourseID)` as the only PK here?

Because it permits only one row for a Student-Course pair and therefore blocks retakes.

## Why use `EnrollmentID`?

Because every enrollment attempt gets its own unique identity.

## Why use `NULL` when Grade is unknown?

Because `NULL` means no Grade is known yet, while `0` is a real numeric Grade and can distort calculations.

---

# 23. Final rule to remember

```text
M:N RELATIONSHIP
       │
       ▼
CREATE A RELATIONSHIP / BRIDGE TABLE
       │
       ├── FK from first entity
       ├── FK from second entity
       ├── relationship attributes
       └── suitable primary key
```

For this example:

```text
Student M:N Course
        │
        ▼
    Enrollment
```

Final design:

```text
Enrollment
────────────────
PK  EnrollmentID
FK  StudentID
FK  CourseID
    EnrollDate
    Grade
```

Result:

```text
Student  1 ─────< M  Enrollment  M >───── 1  Course
```

This design:

- reduces Student and Course redundancy,
- stores relationship attributes in the correct place,
- supports retakes and multiple enrollment attempts,
- preserves enrollment history,
- keeps foreign-key relationships clear,
- and represents an unknown Grade correctly with `NULL`.
