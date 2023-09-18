The `DROP COLUMN` command is used to delete a column in an existing table.

The following SQL deletes the "ContactName" column from the "Customers" table:

```sql
ALTER TABLE Customers
DROP COLUMN ContactName;
```