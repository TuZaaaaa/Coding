# PL/SQL procedure

## syntax

```sql
CREATE [OR REPLACE ] PROCEDURE procedure_name (parameter_list)     
IS
[declaration statements]

BEGIN

[execution statements]

EXCEPTION

[exception handler]

END [procedure_name ];
```

## procedure header

### parameter
> Each parameter can be in either IN, OUT, or INOUT mode.
> The parameter mode specifies whether a parameter can be read from or write to.
> Default mode is IN.


## procedure body

- Declaration section
> A PL/SQL block has a declaration section where you declare variables, allocate memory for cursors, and define data types. 

- Executable section
> A PL/SQL block has an executable section.
> An executable section starts with the keyword BEGIN and ends with the keyword END.
> The executable section must have a least one executable statement, even if it is the NULL statement which does nothing.

- Exception-handling section
> A PL/SQL block has an exception-handling section that starts with the keyword EXCEPTION.
> The exception-handling section is where you catch and handle exceptions raised by the code in the execution section.

## Executing a PL/SQL procedure

```sql
EXECUTE procedure_name(arguments);

EXEC procedure_name(arguments);
```

## Removing a procedure

```sql
DROP PROCEDURE procedure_name; 
```


