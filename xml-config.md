# xml配置
完整xml参考：
```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE ssssssss PUBLIC "-//ssssssss.org//DTD ssssssss 0.1//EN" "http://ssssssss.org/dtd/0.0.x/ssssssss.dtd">
<!-- 定义访问路径 -->
<ssssssss request-mapping="/user">
    <!-- 定义sql片段 -->
    <sql id="customSql">
        username,password
    </sql>
    <!-- 定义一组验证规则 -->
    <!-- 自定义code、message的优先级为3>2>1 -->
    <validate id="rule1" code="50" message="自定义错误提示(1)">
        <param name="username" code="50" message="自定义错误提示(2)">
            <not-null>username不能为空</not-null>
            <max-len value="8" code="100" message="自定义错误提示(3)">username最大长度为8</max-len>
            <min-len value="6">username最小长度为6</min-len>
            <regx value="\d+">username必须为数字</regx>
        </param>
        <param name="roleId">
            <not-null>roleId不能为空</not-null>
        </param>
    </validate>
    <!-- 插入成功返回true,否则返回false -->
    <insert request-mapping="/add" request-method="post" return-type="boolean" validate="rule1,rule2">
        insert into sys_user(username,password,role_id) values(#{username},#{password},#{roleId})
    </insert>
    <!-- 映射路径为/user/list,并开启分页 返回List<Map<String,Object>> -->
    <select-list request-mapping="/list" request-method="get" page="true">
        select
        <!-- 引用自定义的sql片段 -->
        <include refid="customSql"/>
        from sys_user
        where del_flag = 0
        <if test="username != null and username != ''">
            and username like concat('%',#{username},'%')
        </if>
        order by create_date desc
    </select-list>
    <!-- 返回String -->
    <select-one request-mapping="/one" request-method="get" return-type="string">
        select username from sys_user where id = #{id}
    </select-one>
    <!-- 返回String -->
    <select-one request-mapping="/get/{id}" request-method="get" validate="rule3" return-type="string" id="getOne">
        select username from sys_user where id = #{id}
    </select-one>

    <function request-mapping="/test">
        <!-- 调用java方法 -->
        <java class="org.ssssssss.samples.SSSSSSSSApplication" method="testFunction" return="val">
            <value>id</value>
        </java>
        <!-- 执行sqlId为getOne的sql语句 -->
        <execute-sql id="getOne">
            <param name="id" value="val"/><!-- 覆盖id变量，值采用上面调用java方法的结果 -->
        </execute-sql>
    </function>
</ssssssss>
```
## sql
- `id`： 唯一ID，必填项
与`Mybatis`的用法一致，用于定义SQL片段
```xml
<sql id="common-columns">
    username,password
</sql>
```
## include
- `refid`: 被引入的sqlId，必填项
与`Mybatis`的用法一致，用来引入SQL片段
```xml
<include refid="common-columns"/>
```

## foreach
- `collection` : 集合，必填项
- `item` : item，必填项
- `open` : 拼接起始SQL，选填
- `close` : 拼接结束SQL，选填
- `separator` : 分隔符，选填
与`Mybatis`的用法一致，用来循环拼接SQL
```xml
<foreach collection="body.userIds" item="userId" open="(" close=")" separator=",">
    #{userId}
</foreach>
```
## if
- `test` : 判断条件，必填项
与`Mybatis`一致,用来判断，当条件成立时拼接if标签里的内容
```xml
<if test="userId != null and userId != ''">
 and user_id = #{userId}
</if>
```


## select-list
- `id`: `String`(定义Id)
- `request-mapping` : `String`(映射请求路径)
- `request-method` : `String`(限制请求方法)
- `page` : `boolean`(是否开启分页,默认不开启)
- `return-type` : `string`(返回值类型，可选值(int,double,long,string,map) 默认map)
- `validate` : `string`(定义验证规则，多个用`,`分隔)
```xml
<select-list request-mapping="/user/list" page="true" validate="rule1">
    select * from sys_user
</select-list>
```

## select-one
- `id`: `String`(定义Id)
- `request-mapping` : `String`(映射请求路径)
- `request-method` : `String`(限制请求方法)
- `return-type` : `string`(返回值类型，可选值(int,double,long,string,map) 默认map)
- `validate` : `string`(定义验证规则，多个用`,`分隔)
```xml
<select-one id="user-name" request-mapping="/user/name" return-type="string">
    select username from sys_user where id = #{id}
</select-one>
```
## update/isnert/delete
- `id`: `String`(定义Id)
- `request-mapping` : `String`(映射请求路径)
- `request-method` : `String`(限制请求方法)
- `return-type` : `string`(返回值类型，可选值(int,long,string,boolean) 默认int)
- `validate` : `string`(定义验证规则，多个用`,`分隔)
```xml
<update request-mapping="/user/update/password" return-type="boolean">
    update sys_user set password = #{password} where id = #{session.userId}
</update>
```
## validate
- `id`: `string` 定义节点ID
- `code`: `int` 定义验证失败时返回的错误码
- `message`: `int` 定义验证失败时返回的错误说明
### param
- `name` : `string` 参数名
- `code`: `int` 定义验证失败时返回的错误码
- `message`: `int` 定义验证失败时返回的错误说明
```xml
<validate id="rule1" code="50" message="自定义错误提示(1)">
    <param name="username" code="50" message="自定义错误提示(2)">
        <not-null>username不能为空</not-null>
        <max-len value="8" code="100" message="自定义错误提示(3)">username最大长度为8</max-len>
        <min-len value="6">username最小长度为6</min-len>
        <regx value="\d+">username必须为数字</regx>
    </param>
    <param name="roleId">
        <not-null>roleId不能为空</not-null>
    </param>
</validate>
```
## function
- `id`: `String`(定义Id)
- `request-mapping` : `String`(映射请求路径)
- `request-method` : `String`(限制请求方法)
- `validate` : `string`(定义验证规则，多个用`,`分隔)
- `return` : `string`(调用方法返回值的变量名，供后续使用)
### java
- `class`: `String`(调用目标类名)
- `method`: `String`(调用目标方法名)
### execute-sql
- `id` : `select-list/select-one/update/insert/delete`的`id`

```xml
<function request-mapping="/test">
    <!-- 调用java方法 -->
    <java class="org.ssssssss.samples.SSSSSSSSApplication" method="testFunction" return="val">
        <value>id</value>
    </java>
    <!-- 执行sqlId为getOne的sql语句 -->
    <execute-sql id="getOne">
        <param name="id" value="val"/><!-- 覆盖id变量，值采用上面调用java方法的结果 -->
    </execute-sql>
</function>
```
