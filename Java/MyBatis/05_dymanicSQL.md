# dymanicSQL
## if
```xml
    <select id="getEmpsByConditionIf" resultType="org.rabbit.mybatis.bean.Employee">
        select * from tbl_employee where
        <!--
            test: 判断表达式（OGNL）
        -->
        <if test="id != null">
            id = #{id}
        </if>

        <if test="lastName != null and lastName != ''">
            and last_name like #{lastName}
        </if>

        <if test="gender == 0 and gender == 1">
            and gender = #{gender}
        </if>
        <!-- OGNL 会进行字符串与数字的转换判断 -->
        <if test="email != null and email.trim() != ''">
            and email = #{email}
        </if>
    </select>
```

## choose(when, otherwise)
```xml
    <select id="getEmpsByConditionChoose" resultType="org.rabbit.mybatis.bean.Employee">
        select * from tbl_employee
        <where>
            <choose>
                <when test="id != null">
                    id = #{id}
                </when>
                <when test="lastName != null">
                    last_name like #{lastName}
                </when>
                <when test="email != null">
                    email = #{email}
                </when>
                <otherwise>
                    gender = 0
                </otherwise>
            </choose>
        </where>
    </select>
```

## trim(where, set)
### where
```xml
    <select id="getEmpsByConditionIf" resultType="org.rabbit.mybatis.bean.Employee">
        select * from tbl_employee
        <where>
            <!--
                test: 判断表达式（OGNL）
            -->
            <if test="id != null">
                id = #{id}
            </if>

            <if test="lastName != null and lastName != ''">
                and last_name like #{lastName}
            </if>

            <if test="gender == 0 and gender == 1">
                and gender = #{gender}
            </if>
            <!-- OGNL 会进行字符串与数字的转换判断 -->
            <if test="email != null and email.trim() != ''">
                and email = #{email}
            </if>
        </where>
    </select>
```

### set
```xml
    <update id="updateEmp" >
        update tbl_employee
       <set>
           <if test="lastName != null">
               last_name = #{lastName},
           </if>
           <if test="gender != null">
               gender = #{gender}
           </if>
           <if test="email != null">
               email = #{email}
           </if>
       </set>
        where id = #{id}
    </update>
```

### trim
- prefix: 前缀
- prefixOverrieds: 前缀覆盖 去掉整个字符串前面多余的字符
- suffix: 后缀
- suffixOverrieds: 后缀覆盖 去掉整个字符串后面多余的字符

```xml
    <update id="updateEmp" >
        update tbl_employee
        <!-- trim -->
        <trim prefix="set" suffixOverrides=",">
            <if test="lastName != null">
                last_name = #{lastName},
            </if>
            <if test="gender != null">
                gender = #{gender}
            </if>
            <if test="email != null">
                email = #{email}
            </if>
        </trim>
        where id = #{id}
    </update>
```

### foreach
> 用于对一个集合进行遍历，通常是在构建 in 条件语句的时候
```xml
    <select id="getEmpsByConditionForeach" resultType="org.rabbit.mybatis.bean.Employee">
        select * from tbl_employee
        <!--
            collection: 指定要遍历的集合：
                list类型的参数会特殊处理封装在map中，map的key就叫list
            item: 将当前遍历出的元素赋值给指定的变量
            separator: 每个元素之间的分隔符
            open: 遍历出所有结果拼接一个开始的字符
            close: 遍历出所有结果拼接一个结束的字符
            index: 索引，遍历 list 的时候是 index 就是索引，item 就是当前值
                          遍历 map 的时候 index 表示键，item 表示值

            #{变量名}就能取出变量的值也就是当前遍历出的元素
          -->
        <foreach collection="ids" item="item_id" separator=","
                 open="where id in(" close=")">
            #{item_id}
        </foreach>
    </select>
```

#### 批量插入的应用
```xml
    <!-- 批量插入 -->
    <!--
        MySQL 下批量插入：使用 foreach 遍历 支持 values (), (), () 语法
    -->
    <insert id="addEmps">
        insert into tbl_employee (last_name, gender, email, d_id) values
        <foreach collection="emps" item="emp" separator=",">
            (#{emp.lastName}, #{emp.gender}, #{emp.email}, #{emp.dept.id})
        </foreach>
    </insert>

    <!--
        多条插入（不推荐）
        需要在数据库开启配置 allowMultiQueries = true
    -->
    <insert id="addEmps">
        <foreach collection="emps" item="emp" separator=";">
            insert into tbl_employee (last_name, gender, email, d_id) values
            (#{emp.lastName}, #{emp.gender}, #{emp.email}, #{emp.dept.id})
        </foreach>
    </insert>
```

### 内置参数
> MyBatis 内置 _parameter 和 _databaseId 两个参数
- _parameter: 代表整个参数
    - 单个参数：_parameter 就是这个参数
    - 多个参数：参数会被封装为一个 map，_parameter 代表这个 map
- _databaseId: 如果配置了 databaseIdProvider 标签
    - _databaseId 代表当前数据库的别名
```xml
    <select id="getEmpsTestInnerParameter" resultType="org.rabbit.mybatis.bean.Employee">
        <!-- 将 OGNL 表达式的值绑定到一个变量中，方便后来引用这个变量的值 -->
        <bind name="_lastName" value="'%' + lastName + '%'" />
        <if test="_databaseId=='mysql'">
            select * from tbl_employee
            <if test="_parameter != null">
                where last_name like #{lastName}
            </if>
        </if>
        <if test="_databaseId=='oracle'">
            select * from employees
        </if>
    </select>
```

### bind
> bind 元素可以从 OGNL 表达式中创建一个变量并将其绑定到上下文
1. sql 抽取：将要查询的列名或者插入用的列名抽取出来方便引用
2. include 来引用已经抽取的 sql
3. include 还可以自定义一些 property，sql 标签内部就能使用自定义的属性
    include-property: 取值的正确方式：${prop} #{不能使用这种方式}

```xml
    <sql id="insertColumn">
        employee_id, last_name, email
    </sql>
```

```xml
    <insert id="">
        <!-- 引用外部定义的 sql-->
        <include refid="insertColumn"></include>
    </insert>
```

> OGNL
> OGNL(Object Graph Navigation Language) 对象图导航语言，这是一种强大的
> 表达式语言，通过它可以非常方便的来操作对象属性。 类似于我们的 EL, SpEL 等
> | 操作 | 使用 |
> | --- | --- |
> | 访问对象属性 | person.name |
> | 调用方法 | person.getName() |
> | 调用静态属性/方法 | @java.lang.Math@PI/@java.util.UUID@randomUUID() |
> | 调用构造方法 | new org.rabbit.bean.Person('admin').name |
> | 运算符 | +, -, *, /, % |
> | 逻辑运算符 | in, not in, >, >=, <, <=, ==, != |
> 注意：xml 中特殊符号如 ", >, < 等这些都需要使用转义字符
> 
> 访问集合伪属性：
> | 类型 | 伪属性 | 伪属性对用的 Java 方法 |
> | --- | --- | --- |
> | List, Set, Map | size, isEmpty | List/Set/Map.size(), List/Set/Map.isEmpty() |
> List, Set | iterator | List.iterator(), Set.iterator() |
> Map | keys, values | Map.keySet(), Map.values() |
> Iterator | next, hasNext | Iterator.next(), Iterator.hasNext() |