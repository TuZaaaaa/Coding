# Variables

## syntax

```sql
variable_name datatype [NOT NULL] [:= initial_value];
```

- :=/default used for default value
- **local** variable names should start with **l_** and **global** variable names should have a prefix of **g_**
- PL/SQL treats a zero-length string as a NULL value


## Anchored declarations
> a varable whose data type anchor to a table column or another variable

```sql
DECLARE
  l_customer_name customers.name%TYPE;
  l_credit_limit customers.credit_limit%TYPE;
BEGIN
  SELECT
    name, credit_limit
  INTO
    l_customer_name, l_credit_limit
  FROM
    customers
  WHERE
    customer_id = 38;

  DBMS_OUTPUT.PUT_LINE(l_customer_name || ':' || l_credit_limit );
END;
/
```
