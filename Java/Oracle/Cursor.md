# Cursor
> A cursor is a pointer that points to a result of a query.


## Implicit cursors
> Whenever Oracle executes an SQL statements such as `select into`, `insert`, `update`, `delete`
> it automatically creates an implicit cursor.

Oracle internally manages the whole execution cycle of implicit cursors and reveals only the cursor’s information and statuses such as SQL%ROWCOUNT, SQL%ISOPEN, SQL%FOUND, and SQL%NOTFOUND.

The implicit cursor is not elegant when the query returns zero or multiple rows which cause NO_DATA_FOUND or TOO_MANY_ROWS exception respectively.

## Explicit cursors
> An explicit cursor is an SELECT statement declared explicitly in the declaration section of the current block or a package specification.

- execution cycle
  - open
  - fetch
  - close

### Declare a cursor

```sql
CURSOR cursor_name [(parameter_list)]
IS
cursor_query;
```

- parameter is optional

### Open a cursor

```sql
OPEN cursor_name;
```

### Fetch from a cursor
> The FETCH statement places the contents of the current row into variables.
> To retrieve all rows in a result set, you need to fetch each row till the last one.

```sql
FETCH cursor_name INTO variable_list;
```

### Closing a cursor
> After fetching all rows, you need to close the cursor with the CLOSE statement.
> Closing a cursor instructs Oracle to release allocated memory at an appropriate time.
> If you declare a cursor in an anonymous block, procedure, or function,
> the cursor will automatically be closed when the execution of these objects end.

```sql
CLOSE cursor_name;
```

### Explicit Cursor Attributes
> A cursor has four attributes to which you can reference in the following format:

```sql
cursor_name%attribute
```

1. %ISOPEN
> This attribute is TRUE if the cursor is open or FALSE if it is not.

2. %FOUND
> This attribute has four values:

- NULL before the first fetch
- TRUE if a record was fetched successfully
- FALSE if no row returned
- INVALID_CURSOR if the cursor is not opened

3. %NOTFOUND
> This attribute has four values:

- NULL before the first fetch
- FALSE if a record was fetched successfully
- TRUE if no row returned
- INVALID_CURSOR if the cursor is not opened

4. %ROWCOUNT
> The %ROWCOUNT attribute returns the number of rows fetched from the cursor. If the cursor is not opened, this attribute returns INVALID_CURSOR.
 

