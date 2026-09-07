# 09 - Backup Database

A database backup creates a copy of the database so the data can be restored if the database is damaged, corrupted, or lost.

This lesson covers:

- Full database backup
- Differential database backup
- Restore from backup
- `WITH NORECOVERY`
- `WITH RECOVERY`
- Correct restore order

---

## 1. Full Database Backup

A full backup creates a backup of the whole database.

### Syntax

```sql
BACKUP DATABASE DatabaseName
TO DISK = 'Path\\BackupFile.bak';
```

### Example

```sql
BACKUP DATABASE MyDatabase1
TO DISK = 'C:\\MyDatabase1_backup.bak';
```

This creates a backup file named:

```text
MyDatabase1_backup.bak
```

inside the `C:` drive.

### `.bak`

Using the `.bak` extension for SQL Server backup files is a common convention.

It is not mandatory, but it makes backup files easy to recognize.

---

## 2. Where Should the Backup Be Stored?

For learning, you can place a backup directly on a drive such as:

```text
C:\MyDatabase1_backup.bak
```

For a real system, it is safer to store the backup on a **different disk or storage location** from the database files.

Why?

```text
Database + Backup on same disk
            ↓
        Disk fails
            ↓
Database lost + Backup lost
```

Better:

```text
Database      → Disk C:
Backup        → Disk D: / external storage / backup server
```

---

## 3. Differential Database Backup

A differential backup stores the database changes made **since the last full database backup**.

### Syntax

```sql
BACKUP DATABASE DatabaseName
TO DISK = 'Path\\BackupFile.bak'
WITH DIFFERENTIAL;
```

### Example

```sql
BACKUP DATABASE MyDatabase1
TO DISK = 'C:\\MyDatabase1_DIFF.bak'
WITH DIFFERENTIAL;
```

### Idea

```text
Full Backup
    │
    ├── Change 1
    ├── Change 2
    └── Change 3
         │
         └── Differential Backup
             contains the changes since the Full Backup
```

A differential backup is usually faster and smaller than making another full backup when only part of the database has changed.

> Accuracy note: A differential backup contains changes since the last full backup. It does not have to be physically appended to the same `.bak` file.

---

## 4. Full Backup vs Differential Backup

| Backup Type  | What it contains                   | Depends on                    |
|--------------|------------------------------------|-------------------------------|
| Full         | The whole database                 | Nothing before it             |
| Differential | Changes since the last full backup | The last full database backup |

Example:

```text
Monday     → Full Backup
Tuesday    → Differential
Wednesday  → Differential
Thursday   → Differential
```

The Thursday differential contains the changes made since the Monday full backup.

---

## 5. Restore Database From Backup

To restore a database backup in SQL Server, use `RESTORE DATABASE`.

### Syntax

```sql
RESTORE DATABASE DatabaseName
FROM DISK = 'Path\\BackupFile.bak';
```

### Example

```sql
RESTORE DATABASE MyDatabase1
FROM DISK = 'C:\\MyDatabase1.bak';
```

This restores the backup file into the database named `MyDatabase1`.

---

## 6. Restoring a Full Backup + Differential Backup

When restoring a full backup followed by a differential backup, the order is important:

```text
1. Restore Full Backup
2. Restore Differential Backup
```

The database must remain in the restoring state after the full backup so that the differential backup can still be applied.

That is why we use:

```sql
WITH NORECOVERY
```

for the first restore.

Then, on the final restore, we use:

```sql
WITH RECOVERY
```

to finish the restore process and open the database for normal use.

---

## 7. Complete Restore Example

```sql
USE master;
GO

-- Step 1: Restore Full Backup
RESTORE DATABASE DB1
FROM DISK = 'D:\\db1_FULL.bak'
WITH NORECOVERY;
GO

-- Step 2: Restore Differential Backup
RESTORE DATABASE DB1
FROM DISK = 'D:\\db1_DIFF.bak'
WITH RECOVERY;
GO
```

---

## 8. `WITH NORECOVERY`

```sql
WITH NORECOVERY
```

means:

> Restore this backup, but do **not** finish opening the database yet.

The database stays in a restoring state because more backup files are expected.

Use it for the earlier restore steps.

Example:

```sql
RESTORE DATABASE DB1
FROM DISK = 'D:\\db1_FULL.bak'
WITH NORECOVERY;
```

---

## 9. `WITH RECOVERY`

```sql
WITH RECOVERY
```

means:

> Finish the restore process and make the database available for normal use.

Use it on the **last restore step**.

Example:

```sql
RESTORE DATABASE DB1
FROM DISK = 'D:\\db1_DIFF.bak'
WITH RECOVERY;
```

---

## 10. Restore Order

```text
Full Backup
    ↓
WITH NORECOVERY
    ↓
Differential Backup
    ↓
WITH RECOVERY
    ↓
Database Online
```

### Important

Do **not** use `WITH RECOVERY` after the full backup if you still need to restore a differential backup afterward.

Once recovery is completed, that restore sequence is finished.

---

## 11. Quick Summary

| Action                     | SQL Server Command / Option                                   |
|----------------------------|---------------------------------------------------------------|
| Full backup                | `BACKUP DATABASE ... TO DISK = 'file.bak';`                   |
| Differential backup        | `BACKUP DATABASE ... TO DISK = 'file.bak' WITH DIFFERENTIAL;` |
| Restore backup             | `RESTORE DATABASE ... FROM DISK = 'file.bak';`                |
| More restores will follow  | `WITH NORECOVERY`                                             |
| Final restore              | `WITH RECOVERY`                                               |

---

## 12. Main Commands to Remember

### Full Backup

```sql
BACKUP DATABASE DB1
TO DISK = 'D:\\db1_FULL.bak';
```

### Differential Backup

```sql
BACKUP DATABASE DB1
TO DISK = 'D:\\db1_DIFF.bak'
WITH DIFFERENTIAL;
```

### Restore Full Backup

```sql
RESTORE DATABASE DB1
FROM DISK = 'D:\\db1_FULL.bak'
WITH NORECOVERY;
```

### Restore Differential Backup

```sql
RESTORE DATABASE DB1
FROM DISK = 'D:\\db1_DIFF.bak'
WITH RECOVERY;
```

---

## 13. Core Rule to Remember

```text
BACKUP:
Full → Differential

RESTORE:
Full WITH NORECOVERY
        ↓
Differential WITH RECOVERY
```

And for real systems:

> Keep backups on storage that is separate from the database storage whenever possible.
