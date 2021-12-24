# Query
- 多表查询
- 子查询

## 多表查询
> 笛卡尔集
> 笛卡尔集会在下面条件下产生:
>   - 省略连接条件
>   - 连接条件无效
>   - 所有表中的所有行互相连接
> 
> 为了避免笛卡尔集，可以在 WHERE 加入有效的连接条件

### Oracle 连接
使用连接在多个表中查询数据：
- 在 WHERE 子句中写入连接条件。
- 在表中有相同列时，在列名之前加上表名前缀
```sql
    SELECT	table1.column, table2.column
    FROM	table1, table2
    WHERE	table1.column1 = table2.column2;
```

### 等值连接
```sql
SELECT employees.employee_id, employees.last_name, 
       employees.department_id, departments.department_id,
       departments.location_id
FROM   employees, departments
WHERE  employees.department_id = departments.department_id;
```

> - 区分重复的列名：
>   - 使用表名前缀在多个表中区分相同的列
>   - 在不同表中具有相同列名的列可以用表的别名加以区分
> - 表单别名：
>   - 使用别名可以简化查询
>   - 使用表名前缀可以提高执行效率
> - 连接多个表：
>   - 连接 n个表,至少需要 n-1个连接条件

### 非等值连接
```sql
    SELECT e.last_name, e.salary, j.grade_level
    FROM   employees e, job_grades j
    WHERE  e.salary 
        BETWEEN j.lowest_sal AND j.highest_sal;
```

### 内连接
> 合并具有同一列的两个以上的表的行,
> 结果集中不包含一个表与另一个表不匹配的行

#### 外连接
> 使用外连接可以查询不满足连接条件的数据。
> 外连接的符号是 (+)
```sql
    SELECT	table1.column, table2.column
    FROM	table1, table2
    WHERE	table1.column(+) = table2.column;
```

### 自连接
- NATURAL JOIN 子句，会以两个表中具有相同名字的列为条件创建等值连接
- 在表中查询满足等值条件的数据
- 如果只是列名相同而数据类型不同，则会产生错误
```sql
    SELECT department_id, department_name,
        location_id, city
    FROM   departments
    NATURAL JOIN locations;
```

> 叉集（了解）
>   - 使用CROSS JOIN 子句使连接的表产生叉集
>   - 叉集和笛卡尔集是相同的
```sql
    SELECT last_name, department_name
    FROM   employees
    CROSS JOIN departments ;
```

### 连接方式
#### USING 子句
- 在NATURAL JOIN 子句创建等值连接时，可以使用 USING 子句指定等值连接中需要用到的列
- 使用 USING 可以在有多个列满足条件时进行选择
- 不要给选中的列中加上表名前缀或别名
- JOIN 和 USING 子句经常同时使用

```sql
    SELECT e.employee_id, e.last_name, d.location_id
    FROM   employees e JOIN departments d
    USING (department_id) ;
```

#### ON 子句
- 自然连接中是以具有相同名字的列为连接条件的
- 可以使用 ON 子句指定额外的连接条件
- 这个连接条件是与其它条件分开的
- ON 子句使语句具有更高的易读性

### 满外连接
> 两个表在连接过程中除了返回满足连接条件的行以外还返回两个表中不满足条件的行，
> 这种连接称为满外连接
```sql
    SELECT e.last_name, e.department_id, d.department_name
    FROM   employees e
    FULL OUTER JOIN departments d
    ON   (e.department_id = d.department_id) ;
```