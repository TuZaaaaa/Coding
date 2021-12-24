# function 

## 单行函数
- 操作数据对象
- 接受参数返回一个结果
- 只对一行进行变换
- 每行返回一个结果
- 可以转换数据类型
- 可以嵌套
- 参数可以是一列或一个值


### 字符函数
- 大小写控制函数
  - LOWER 转小写
  - UPPER 转大写
  - INITCAP 转大驼峰
- 字符控制函数
  - CONCAT 拼接
  - SUBSTR 截取
  - LENGTH 长度
  - INSTR 索引
  - LPAD | RPAD 左/右 补全
  - TRIM 去空格
  - REPLACE 替换


### 数字函数
- ROUND: 四舍五入
  - ROUND(45.926, 2) 45.93
- TRUNC: 截断
  - TRUNC(45.926, 2) 45.92
- MOD: 求余
  - MOD(1600, 300) 100

### 日期函数
#### 数学运算
- 在日期上加上或减去一个数字结果仍为日期
- 两个日期相减返回日期之间相差的天数
  - 日期不允许做加法运算，无意义
- 可以用数字除24来向日期中加上或减去天数

#### 日期函数
| 函数 | 描述 |
| --- | --- |
| MONTHS_BETWEEN | 两个日期相差的月数 |
| ADD_MONTHS | 向指定日期中加入若干月数 |
| NEXT_DAY | 指定日期的下一个星期对应的日期 |
| LAST_DAY | 本月的最后一天 |
| ROUND | 日期四舍五入 |
| TRUNC | 日期截断 |

#### 转换函数 
- 隐性
> Oracle 自动转换:

| 源数据类型 | 目标数据类型 | 
| --- | --- |
| VARCHAR2 or CHAR | NUMBER |
| VARCHAR2 or CHAR | DATE |
| NUMBER | VARCHAR2 |
| DATE | VARCHAR2 |
- 显性
> 显式数据类型转换：

- DATE
  - TO_CHAR --> CHARACTER
- CHARACTER 
  - TO_DATE --> DATE 
  - TO_NUMBER --> NUMBER 
- NUMBER 
  - TO_CHAR --> CHARACTER

> TO_CHAR 对日期的转换
> 格式：
>   - 必须包含在单引号中而且大小写敏感。
>   - 可以包含任意的有效的日期格式。
>   - 日期之间用逗号隔开

日期格式的元素：
| 格式 | 样式 |
| --- | ---|
| YYYY | 2004 |
| YEAR | TWO THOUSAND AND FOUR |
| MM | 02 |
| MONTH | JULY |
| MON | JUL |
| DY | MON |
| DAY | MONDAY |
| DD | 02 |

时间格式：
- HH24:MI:SS AM  15:45:32 PM
- 使用双引号向日期中添加字符
  - DD "of" MONTH  12 of OCTOBER

##### TO_CHAR 对日期的转换
```sql
  SELECT last_name,
        TO_CHAR(hire_date, 'DD Month YYYY')
        AS HIREDATE
  FROM   employees;
```

##### TO_CHAR 对数字的转换
`TO_CHAR(number, 'format_model')`
下面是在 TO_CHAR 函数中经常使用的几种格式：
| 符号 | 描述 |
| --- | --- |
| 9 | 数字 |
| 0 | 零 |
| $ | 美元符号 |
| L | 本地货币符号 |
| . | 小数点 |
| , | 千位符 |

```sql
  SELECT TO_CHAR(salary, '$99,999.00') SALARY
  FROM   employees
  WHERE  last_name = 'Ernst';
```

##### TO_DATE 对字符的转换
- 使用 TO_DATE:
`TO_DATE(char[,'format_model]')`
- 使用 TO_DATE 函数将字符转换成数字：
`TO_DATE('2021年10月29日 08:10:21', 'yyyy "年"mm"月"dd"日"hh:mm:ss') From dual`

##### TO_NUMBER 对字符的转换
- 使用 TO_NUMBER 函数将字符转换成日期：
`TO_NUMBER(char[, 'format_model'])`
- 使用 TO_NUMBER：
`TO_NUMBER('￥1, 234, 567, 890.00', 'L999, 999, 999, 99.99') from dual`

## 通用函数
这些函数适用于任何数据类型，同时也适用于空值：
- NVL (expr1, expr2)
- NVL2 (expr1, expr2, expr3)
- NULLIF (expr1, expr2)
- COALESCE (expr1, expr2, ..., exprn)

### NVL 函数
> 将空值转换成一个已知的值：
> 可以使用的数据类型有日期、字符、数字

函数的一般形式:
- NVL(commission_pct,0)
- NVL(hire_date,'01-JAN-97')
- NVL(job_id,'No Job Yet')

```sql
  SELECT last_name, salary, NVL(commission_pct, 0),
    (salary*12) + (salary*12*NVL(commission_pct, 0)) AN_SAL
  FROM employees;
```
### NVL2 函数
> NVL2 (expr1, expr2, expr3) : expr1不为NULL，返回expr2；为NULL，返回expr3
```sql
  SELECT last_name,  salary, commission_pct,
        NVL2(commission_pct, 
              'SAL+COMM', 'SAL') income
  FROM   employees WHERE department_id IN (50, 80);
```
 
### NULLIF 函数
> NULLIF (expr1, expr2) :  相等返回NULL，不等返回expr1 
```sql
  SELECT first_name, LENGTH(first_name) "expr1", 
        last_name,  LENGTH(last_name)  "expr2",
        NULLIF(LENGTH(first_name), LENGTH(last_name)) result
  FROM   employees;
```
 
### COALESCE 函数
- COALESCE 与 NVL 相比的优点在于 COALESCE 可以同时处理交替的多个值
- 如果第一个表达式为空，则返回下一个表达式，对其他的参数进行 COALESCE

```sql
  SELECT   last_name,
          COALESCE(commission_pct, salary, 10) comm
  FROM     employees
  ORDER BY commission_pct;
```

### 条件表达式
> 在 SQL 语句中使用IF-THEN-ELSE 逻辑

使用两种方法:
- CASE 表达式
- DECODE 函数

#### case
- if-else-then
```sql
  CASE expr WHEN comparison_expr1 THEN return_expr1
          [WHEN comparison_expr2 THEN return_expr2
            WHEN comparison_exprn THEN return_exprn
            ELSE else_expr]
  END
```

- case
```sql
  SELECT last_name, job_id, salary,
        CASE job_id WHEN 'IT_PROG'  THEN  1.10*salary
                    WHEN 'ST_CLERK' THEN  1.15*salary
                    WHEN 'SA_REP'   THEN  1.20*salary
        ELSE      salary END     "REVISED_SALARY"
  FROM   employees;
```

#### decode
- if-else-then
```sql
  DECODE(col|expression, search1, result1 ,
                [, search2, result2,...,]
                [, default])
```
- decode
```sql
  SELECT last_name, job_id, salary,
        DECODE(job_id, 'IT_PROG',  1.10*salary,
                        'ST_CLERK', 1.15*salary,
                        'SA_REP',   1.20*salary,
                          salary)
        REVISED_SALARY
  FROM   employees;
```

### 嵌套函数
- 单行函数可以嵌套
- 嵌套函数的执行顺序是由内到外
```sql
  SELECT last_name,
        NVL(TO_CHAR(manager_id), 'No Manager')
  FROM   employees
  WHERE  manager_id IS NULL;
```

## 分组函数
> 分组函数作用于一组数据，并对一组数据返回一个值

组函数类型：
- AVG 
- COUNT 
- MAX 
- MIN 
- STDDEV
- SUM

### AVG & SUM
> 可以对数值型数据使用 AVG 和 SUM 函数 
```sql
  SELECT AVG(salary), MAX(salary),
                MIN(salary), SUM(salary)
  FROM   employees
  WHERE  job_id LIKE '%REP%';
```

### MIN & MAX
> 可以对任意数据类型的数据使用 MIN 和 MAX 函数 
```sql
  SELECT MIN(hire_date), MAX(hire_date)
  FROM	  employees;
```

### COUNT
> COUNT(*) 返回表中记录总数，适用于任意数据类型 
> 返回expr不为空的记录总数。

```sql
  SELECT COUNT(*)
  FROM	  employees
  WHERE  department_id = 50;
```

### 空值处理
- 组函数忽略空值
```sql
  SELECT AVG(commission_pct)
  FROM   employees;
```
- 组函数中使用 NVL 函数
> NVL函数使分组函数无法忽略空值
```sql
  SELECT AVG(NVL(commission_pct, 0))
  FROM   employees;
```

> DISTINCT 关键字
> - COUNT(DISTINCT expr)返回 expr 非空且不重复的记录总数
```sql
  SELECT COUNT(DISTINCT department_id)
  FROM employees;
```

## GROUP BY 子句
> 在SELECT 列表中所有未包含在组函数中的列都应该包含在 GROUP BY 子句中 
```sql
  SELECT   department_id, AVG(salary)
  FROM     employees
  GROUP BY department_id ;
```

### 非法使用组函数
- 所有包含于 SELECT 列表中，而未包含于组函数中的列都必须包含于 GROUP BY 子句中
- 不能在 WHERE 子句中使用组函数
- 可以在 HAVING 子句中使用组函数

## HAVING 子句
使用 HAVING 过滤分组:
-	行已经被分组。
-	使用了组函数。
-	满足HAVING 子句中条件的分组将被显示
```sql
  SELECT	column, group_function
  FROM		table
  [WHERE	condition]
  [GROUP BY	group_by_expression]
  [HAVING	group_condition]
  [ORDER BY	column];
```