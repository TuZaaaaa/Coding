# mapping file
> 映射文件指导着MyBatis如何进行数据库增删改查

## insert, update, delete 标签
| 属性 | 描述 |
| --- | --- |
| **id** | **命名空间中的唯一标识符** |
| parameterType | 将要传入语句的参数的完全限定类型或别名。这个属性是可选的，因为 **MyBatis** 可以通过 **TypeHandler** 推断出具体传入语句的参数类型，默认值为 unset |
| flushCache | 将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句）
| **timeout** | 这个设置是再抛出异常之前，驱动程序**等待数据库返回请求结果的秒数** ，默认值为 unset（依赖驱动） |
| statementType | STATEMENT, PREPARED, CALLABLE 的一个，这会让 MyBatis 分别使用 Statement, **PreparedStatement** 或 CallableStatement，**默认值：PREPARED**
| **userGeneratedKeys** | （仅对 insert, update 有效）**这会令 MyBatis 使用 JDBC 的 getGeneratedKeys() 方法来取出由数据库内部生成的主键**（比如：像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段），默认值：false |
| **keyProperty** | （仅对 insert, update 有效）唯一标记一个属性，**MyBatis 会通过 getGeneratedKeys() 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值**，默认：unset |
| keyColun | （仅对 insert, update 有效）通过生成的键值表中的列名，这个设置仅在某些数据库（像 PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置，如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表 |
| databaseId | **如果配置了 databaseIdProvider, MyBatis 会加载所有的不带 databaseId 或匹配当前 databaseId 的语句，如果带或不带的语句都有，则不带的会被忽略**

```xml
    <insert id="addEmp" parameterType="org.rabbit.mybatis.bean.Employee"
            useGeneratedKeys="true" keyProperty="id">
        insert into tbl_employee (last_name, gender, email)
        values (#{lastName}, #{gender}, #{email})
    </insert>

    <delete id="deleteEmpById">
        delete from tbl_employee where id = #{id}
    </delete>

    <update id="updateEmp">
        update tbl_employee set last_name = #{lastName}, gender = #{gender}, email = #{email} where id = #{id}
    </update>

```
## 主键生成方式
- 若数据库支持自动生成主键的字段（比如 MySQL 和 SQL Server），则可以设置 useGeneratedKeys=”true”，然后再把 keyProperty 设置到目标属性上
```xml
    <insert id="addEmp" parameterType="org.rabbit.mybatis.bean.Employee"
            useGeneratedKeys="true" keyProperty="id">
        insert into tbl_employee (last_name, gender, email)
        values (#{lastName}, #{gender}, #{email})
    </insert>
```

- 而对于不支持自增型主键的数据库（例如 Oracle），则可以使用 selectKey 子元素：selectKey 元素将会首先运行，id 会被设置，然后插入语句会被调用
```xml

    <insert id="insertCustomer" databaseId="oracle"
    parameterType="customer">
        <selectKey order="BEFORE" keyProperty="id" resultType="_int">
            select crm_seq.nextval
            from dual
        </selectKey>
        insert into customers2 (id, last_name, email, age)
        values (#{id}, #{lastName}, #{email}, #{age})
    </insert>
```

### selectKey
| 属性 | 描述 |
| --- | --- |
| **keyProperty** | **selectKey 语句结果应该被设置的目标属性** |
| keyColumn | 匹配属性的返回结果集中的列名称 |
| **resultType** | **结果的类型**。MyBatis 通常可以推断出来，但是为了更加确定，写上也不会有什么问题，MyBatis 允许任何简单类型用作主键的类型，包括字符串 |
| **order** | **可以被设置为 BEFORE 或 AFTER。如果设置为 BEFORE，那么它会首先选择主键，设置 keyProperty 然后执行插入语句**，如果设置为 AFTER，那么先执行插入语句，然后是 selectKey 元素 |

## 参数处理
- 单个参数：mybatis不会做特殊处理，#{参数名/任意名}：取出参数值。
- 多个参数：mybatis会做特殊处理。多个参数会被封装成 一个map
    - key：param1...paramN,或者参数的索引也可以
    - value：传入的参数值
        - #{}就是从map中获取指定的key的值
	异常：
        - org.apache.ibatis.binding.BindingException: 
        - Parameter 'id' not found. 
        - Available parameters are [1, 0, param1, param2]
	操作：
		- 方法：public Employee getEmpByIdAndLastName(Integer id,String lastName);
		- 取值：#{id},#{lastName}

- 命名参数：明确指定封装参数时map的key；@Param("id") 多个参数会被封装成一个 map
    - key：使用@Param注解指定的值
    - value：参数值
        - #{指定的key}取出对应的参数值


### 应用选择
- POJO:
> 如果多个参数正好是我们业务逻辑的数据模型，我们就可以直接传入pojo
> - #{属性名}：取出传入的pojo的属性值	

- Map:
> 如果多个参数不是业务模型中的数据，没有对应的pojo，不经常使用，为了方便，我们也可以传入map
> - #{key}：取出map中对应的值

- TO:
> 如果多个参数不是业务模型中的数据，但是经常要使用，推荐来编写一个TO（Transfer Object）数据传输对象
> - Page{
>       int index;
>       int size;
>    }

### 思考
`public Employee getEmp(@Param("id")Integer id,String lastName);`
	取值：id==>#{id/param1}   lastName==>#{param2}

`public Employee getEmp(Integer id,@Param("e")Employee emp);`
	取值：id==>#{param1}    lastName===>#{param2.lastName/e.lastName}

> 特别注意：如果是Collection（List、Set）类型或者是数组，
		 也会特殊处理。也是把传入的list或者数组封装在map中。
			key：Collection（collection），如果是List还可以使用这个key(list)
				数组(array)
`public Employee getEmpById(List<Integer> ids);`
	取值：取出第一个id的值：   #{list[0]}

### 参数处理-源码
> 总结：参数多时会封装map，为了不混乱，我们可以使用@Param来指定封装时使用的key
>   - #{key}就可以取出map中的值；

```java
(@Param("id")Integer id,@Param("lastName")String lastName);
```
- ParamNameResolver 解析参数封装 map
1、names：{0=id, 1=lastName}；构造器的时候就确定好了
	- 确定流程：
        1. 获取每个标了param注解的参数的@Param的值：id，lastName；  赋值给name;
        2. 每次解析一个参数给map中保存信息：（key：参数索引，value：name的值）
            - name的值：
                - 标注了param注解：注解的值
                - 没有标注：
                    - 全局配置：useActualParamName（jdk1.8）：name=参数名
                    - name=map.size()；相当于当前元素的索引
                        {0=id, 1=lastName,2=2}


`args【1，"Tom",'hello'】:`

源码：
```java
public Object getNamedParams(Object[] args) {
    final int paramCount = names.size();
    //1、参数为null直接返回
    if (args == null || paramCount == 0) {
      return null;
     
    //2、如果只有一个元素，并且没有Param注解；args[0]：单个参数直接返回
    } else if (!hasParamAnnotation && paramCount == 1) {
      return args[names.firstKey()];
      
    //3、多个元素或者有Param标注
    } else {
      final Map<String, Object> param = new ParamMap<Object>();
      int i = 0;
      
      //4、遍历names集合；{0=id, 1=lastName,2=2}
      for (Map.Entry<Integer, String> entry : names.entrySet()) {
      
      	//names集合的value作为key;  names集合的key又作为取值的参考args[0]:args【1，"Tom"】:
      	//eg:{id=args[0]:1,lastName=args[1]:Tom,2=args[2]}
        param.put(entry.getValue(), args[entry.getKey()]);
        
        
        // add generic param names (param1, param2, ...)param
        //额外的将每一个参数也保存到map中，使用新的key：param1...paramN
        //效果：有Param注解可以#{指定的key}，或者#{param1}
        final String genericParamName = GENERIC_NAME_PREFIX + String.valueOf(i + 1);
        // ensure not to overwrite parameter named with @Param
        if (!names.containsValue(genericParamName)) {
          param.put(genericParamName, args[entry.getKey()]);
        }
        i++;
      }
      return param;
    }
  }
}
```

### 参数值的获取
- #{}：可以获取map中的值或者pojo对象属性的值
- ${}：可以获取map中的值或者pojo对象属性的值

```xml
select * from tbl_employee where id = ${id} and last_name = #{lastName}
Preparing: select * from tbl_employee where id = 2 and last_name = ?
```
> 区别：
>    - #{}:是以预编译的形式，将参数设置到sql语句中；PreparedStatement；防止sql注入
>    - ${}:取出的值直接拼装在sql语句中；会有安全问题；
>    大多情况下，我们去参数的值都应该去使用#{}
>    
>    原生jdbc不支持占位符的地方我们就可以使用${}进行取值
>    - 比如分表、排序，按照年份分表拆分
>        - `select * from ${year}_salary where xxx;`
>        - `select * from tbl_employee order by ${f_name} ${order}`

#### #{}:更丰富的用法：
- 规定参数的一些规则：
    javaType、 jdbcType、 mode（存储过程）、 numericScale、
    resultMap、 typeHandler、 jdbcTypeName、 expression（未来准备支持的功能）

- jdbcType 通常需要在某种特定的条件下被设置：
    - 在我们数据为null的时候，有些数据库可能不能识别mybatis对null的默认处理。比如Oracle（报错）
    
    - JdbcType OTHER：无效的类型，因为mybatis对所有的null都映射的是原生Jdbc的OTHER类型，oracle不能正确处理
    由于全局配置中：jdbcTypeForNull=OTHER；oracle不支持；两种办法
        1. #{email,jdbcType=OTHER};
        2. jdbcTypeForNull=NULL
            - `<setting name="jdbcTypeForNull" value="NULL" />`

## select 标签
> 用来定义查询操作
- Id：唯一标识符。
    - 用来引用这条语句，需要和接口的方法名一致
- parameterType：参数类型。
    - 可以不传，MyBatis会根据TypeHandler自动推断
- resultType：返回值类型。
    - 别名或者全类名，如果返回的是集合，定义集合中元素的类型。不能和resultMap同时使用

### 自动映射
#### 全局setting设置
- autoMappingBehavior默认是PARTIAL，开启自动映射的功能。唯一的要求是列名和javaBean属性名一致
- 如果autoMappingBehavior设置为null则会取消自动映射
- 数据库字段命名规范，POJO属性符合驼峰命名法，如 A_COLUMNaColumn，我们可以开启自动驼峰命名规则映射功能，mapUnderscoreToCamelCase=true

#### 自定义resultMap，实现高级结果集映射

##### resultMap
> - constructor: 类在实例化时, 用来注入结果到构造方法中
>   - idArg: ID 参数; 标记结果作为 ID 可以帮助提高整体效能
>   - arg: 注入到构造方法的一个普通结果
> - **id**: 一个 ID 结果; 标记结果作为 ID 可以帮助提高整体效能
> - **result**: 注入到字段或 JavaBean 属性的普通结果
> - **association**: 一个复杂的类型关联;许多结果将包成这种类型
> - 嵌入结果映射: 结果映射自身的关联,或者参考一个
> - collection**: 复杂类型的集
> - 嵌入结果映射: 结果映射自身的集,或者参考一个
> - discriminator: 使用结果值来决定使用哪个结果映射
>   - case: 基于某些值的结果映射
>       - 嵌入结果映射: 这种情形结果也映射它本身，因此可以包含很多相同的元素，或者它可以参照一个外部的结果映射

###### id & result
> id 和 result 映射一个单独列的值到简单数据类型(字符串,整型,双精度浮点数,日期等)的属性或字段

| 属性 | 描述 |
| --- | --- |
| **property** | 映射到列结果的字段或属性。例如："username" 或 "address.street.number" |
| **column** | 数据表的列名。通常和 resultSet.getString(columnName) 的返回值一致 |
| javaType | 一个 Java 类的完全限定名，或一个类型别名。如果映射到一个 JavaBean, MyBatis 通常可以断定类型 |
| jdbcType | JDBC 类型是仅仅需要对插入，更新和删除操作可能为空的列进行处理 |
| typeHandler | 类型处理器，使用这个属性，可以覆盖默认的类型处理器，这个属性值是类的完全限定名或者是一个类型处理器的实现或者是类型别名 |

## association
> 复杂对象映射
> 当 POJO 中的属性为一个对象时，我们可以使用联合查询，
> 并以级联属性的方式封装对象

```xml
    <resultMap id="MyDifEmp" type="org.rabbit.mybatis.bean.Employee">
        <id column="id" property="id" />
        <result column="last_name" property="lastName" />
        <result column="gender" property="gender"/>
        <result column="did" property="dept.id"/>
        <result column="dept_name" property="dept.departmentName"/>
    </resultMap>
```
> 或者使用 association 标签定义对象的封装规则
- 嵌套结果集
```xml
    <resultMap id="MyDifEmp2" type="org.rabbit.mybatis.bean.Employee">
        <id column="id" property="id" />
        <result column="last_name" property="lastName" />
        <result column="gender" property="gender"/>
        <association property="dept" javaType="org.rabbit.mybatis.bean.Department">
            <id column="did" property="id" />
            <result column="dept_name" property="departmentName" />
        </association>
    </resultMap>
```

- 分步查询
    - 流程
        1. 先按照员工 id 查询员工信息
        2. 根据查询员工信息中的 d_id 值去部门表查出部门信息
        3. 部门设置到员工中
    - select: 调用目标的方法查询当前属性的值
    - column: 将指定列的值传入目标方法
```xml
    <resultMap id="MyEmpByStep" type="org.rabbit.mybatis.bean.Employee">
        <id column="id" property="id" />
        <result column="last_name" property="lastName" />
        <result column="gender" property="gender" />
        <result column="email" property="email" />
        <!--
            association 定义关联对象的封装规则
                select: 表明当前属性是调用 select 指定的方法查出的结果
                column: 指定将哪一列的值传给这个方法
            流程：使用 select 指定的方法（传入 column 指定的这列参数的值）查出对象，并封装给 property 指定的属性
        -->
        <association property="dept"
                     select="org.rabbit.mybatis.dao.DepartmentMapper.getDeptById"
                     column="d_id">
        </association>
    </resultMap>
```

- 分步查询 & 延迟加载
  - 开启延迟加载和属性按需加载
```xml
    <setting name="lazyLoadingEnabled" value="true" />
    <setting name="aggressiveLazyLoading" value="false" />
```
用处：
分步查询时，每次查询 Employee 对象时，也查询了对应的 Department 对象，部门信息需要在我们使用的时候才去查询

### Collection-集合类型&嵌套结果集
```xml
    <select id="getDeptByIdPlus" resultMap="MyDept">
        SELECT d.id did, d.dept_name dept_name,
        e.id eid, e.last_name last_name, e.email email, e.gender gender
        FROM tbl_dept d LEFT JOIN tbl_employee e
        ON d.id = e.d_id
        WHERE d.id = #{id}
    </select>
```

```xml
    <resultMap id="MyDept" type="org.rabbit.mybatis.bean.Department">
        <id column="did" property="id" />
        <result column="dept_name" property="departmentName" />
        <!--
            collection: 定义关联集合类型的属性的封装规则
            ofType: 指定集合里面元素的类型
        -->
        <collection property="emps" ofType="org.rabbit.mybatis.bean.Employee">
            <!-- 定义这个集合中元素的封装规则 -->
            <id column="eid" property="id" />
            <result column="last_name" property="lastName" />
            <result column="gender" property="gender" />
            <result column="email" property="email" />
        </collection>
    </resultMap>
```

### Collection-分步查询&延迟加载
```xml
    <resultMap id="MyDeptStep" type="org.rabbit.mybatis.bean.Department">
        <id column="id" property="id" />
        <result column="dept_name" property="departmentName" />
        <collection property="emps"
                    select="org.rabbit.mybatis.dao.EmployeeMapperPlus.getEmpsByDeptId"
                    column="{deptId=id}" fetchType="lazy">
        </collection>
    </resultMap>
    <select id="getDeptByIdPlusStep" resultMap="MyDeptStep">
        select id, dept_name from tbl_dept where id = #{id}
    </select>
```

### 扩展-多列值封装map传递
- 分步查询的时候通过column指定，将对应的列的数据传递过去，我们有时需要传递多列数据
- 使用{key1=column1,key2=column2…}的形式
- association或者collection标签的 fetchType = eager/lazy 可以覆盖全局的延迟加载策略，指定立即加载（eager）或者延迟加载（lazy）

### discriminator 鉴别器
> 使用鉴别器判断某列的值，然后根据某列的值改变封装行为

- 封装 Employee:
    - 如果查出的是女生：把部门信息查询出来，否则不查询
    - 如果是男生，把 last_name 这一列的值赋值给 email

```xml
    <resultMap id="MyEmpDis" type="org.rabbit.mybatis.bean.Employee">
        <id column="id" property="id" />
        <result column="last_name" property="lastName" />
        <result column="gender" property="gender" />
        <result column="email" property="email" />
        <!--
            column: 指定判定的列名
            javaType: 列值对应的 java 类型
        -->
        <discriminator javaType="string" column="gender">
            <!-- 女生 resultType: 指定封装的结果类型 不能省略-->
            <case value="0" resultType="org.rabbit.mybatis.bean.Employee">
                <association property="dept"
                             select="org.rabbit.mybatis.dao.DepartmentMapper.getDeptById"
                             column="d_id">
                </association>
            </case>
            <!-- 男生 将 last_name 这一列的值赋值给 email-->
            <case value="1" resultType="org.rabbit.mybatis.bean.Employee">
                <id column="id" property="id" />
                <result column="last_name" property="lastName" />
                <result column="gender" property="gender" />
                <result column="last_name" property="email" />
            </case>
        </discriminator>
    </resultMap>
```