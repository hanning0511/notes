Tags: #SQL

---

FETCH is an SQL command used along with ORDER BY clause with an OFFSET(Starting point) to retrieve or fetch selected rows sequentially using a cursor that moves and processes each row one at a time till the number of rows mentioned in the query are displayed.

- With FETCH the OFFSET clause is mandatory. You are not allowed to use, ORDER BY … FETCH.
- You are not allowed to combine TOP with OFFSET and FETCH.
- The OFFSET/FETCH row count expression can only be any arithmetic, constant, or parameter expression which will return an integer value.
- With the OFFSET and FETCH clause, the ORDER BY is mandatory to be used.

## Syntax

```sql
SELECT *
FROM table_name
ORDER BY col_name
OFFSET starting point
FETCH NEXT k(constant) ROWS ONLY;
```