---
layout: post
title:  "MyBatis example"
date:   2020-05-10 22:21:53 +0100
categories: jekyll update
---

Whenever you want to create a application you have to save data somewhere. If your application 
need to perform a more complex operation on data it will need a proper DBMS for resolve this problem. 
However the question is shows up. How we can handel a OOP entities with relation table together. 
There is no silver bullet to do this. The already existing solutions
is ORM frameworks as Hibernate or persistence mapping frameworks as MyBatis. What is the difference between of
them?

* OMR - it is complex solution which provide a way to map and manage persistent entity. It's means 
it not just map tables to objects but also manage a objects life cycle. The `EntityManager` play a 
key role in that solution. It decided when to gt object from cache or put it there, how
to flush cache entities to DB and build a queries for that.

* Persistent mapper - it more simple solution that just provide a easiest way to map you query result set 
to a entity. 

As you see `persitent mapper` is more simple solution. There are not a lot of magic under the hood in it. 
You write you own query and than just map it in OP entity. And MyBatis is a persistent mapper. What is
benefits of that approach?

* you can write queries with a lot of complexity and it will be transparent for you
* there is no complex life cycle behind this solution so it easy to maintain
* mapping of result set data is implemented in one way an it much easy that do it with `jdbcTemplate` solution

So now let's move a little bit dipper and write some code

To use MyBatis you have to add a maven

{% highlight xml %}

<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.2</version>
</dependency>

{% endhighlight %}

Than you will be able to use MyBatis in your application. 

MyBatis provide a two approach how to describe your mapping via XML or annotation. We start with annotation and 
then describe how to do it with XML. Let assume that we have next DB init script

{% highlight sql %}

CREATE TABLE employee(
    id UUID,
    first_name VARCHAR,
    last_name VARCHAR,
    salary SMALLINT,
    manager_id UUID,
    birth_date DATE,
    skills VARCHAR[],
    CONSTRAINT pk_employee PRIMARY KEY(id)
);

INSERT INTO employee
VALUES
    ('700d698d-6fa6-42d9-a5cc-a85c8c4ccbe6', 'Nick', 'Carter', '3000', null, '1990-12-08', ARRAY['excel', 'java']),
    ('794d22e8-e5ff-46e0-8c50-94e363bf1249', 'Bruce', 'Kane', '1000', '700d698d-6fa6-42d9-a5cc-a85c8c4ccbe6', '1986-02-24', ARRAY['MBA deegre']);
    
{% endhighlight %}

Than to fetch all employees we can use next code

{% highlight java %}

@Mapper
public interface EmployeeMapper {

    @Select("Select " +
            "first_name as firstName, " +
            "last_name as lastName, " +
            "birth_date as date, " +
            "skills as skills " +
            "from employee")
    @Results(value = {
            @Result(property = "skills", column = "skills", typeHandler = StringArrayTypeHandler.class)
    })
    List<Employee> findAllEmployees();
}

{% endhighlight %}


To map entity correctly we should to assert an alias like a name for mapped entity. But what we should to do 
with skills. It's a array so how exactly we should map it to list of skills? for that propose we create class
`StringArrayTypeHandler.class` that's extend a BaseTypeHandler

{% highlight java %}


public class StringArrayTypeHandler extends BaseTypeHandler<List<String>> {

    @Override
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, List<String> strings, JdbcType jdbcType) throws SQLException {

    }

    @Override
    public List<String> getNullableResult(ResultSet resultSet, String s) throws SQLException {
        return convertToList(resultSet.getArray(s));
    }

    @Override
    public List<String> getNullableResult(ResultSet resultSet, int i) throws SQLException {
        return convertToList(resultSet.getArray(i));
    }

    @Override
    public List<String> getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        return convertToList(callableStatement.getArray(i));
    }

    List<String> convertToList(Array array) throws SQLException {
        String[] strings = (String[]) array.getArray();
        return Arrays.asList(strings);
    }
}

{% endhighlight %}

Ok it simple, but what if we need a more complex query? For example we want to select department with all
employees. Then let's do it. First prepare DB

{% highlight sql %}

INSERT INTO department
VALUES
    ('cb3326b5-28c6-4f09-90a3-ae55d600f67b', 'hr department');

CREATE TABLE department_employee(
    id UUID,
    id_department UUID NOT NULL
        CONSTRAINT department_to_department_employee_fkey REFERENCES department(id),
    id_employee UUID NOT NULL
        CONSTRAINT employee_to_department_employee_fkey REFERENCES employee(id)
);

INSERT INTO department_employee
    VALUES
    ('9b628bbc-740b-46a7-adb3-76be4c7ac489','cb3326b5-28c6-4f09-90a3-ae55d600f67b','700d698d-6fa6-42d9-a5cc-a85c8c4ccbe6'),
    ('4e1419ee-d9ec-48de-9792-40a7197706d9','cb3326b5-28c6-4f09-90a3-ae55d600f67b','794d22e8-e5ff-46e0-8c50-94e363bf1249');

{% endhighlight %}

Next write the mapper


{% highlight java %}

@Mapper
public interface DepartmentMapper {

    @Select("Select " +
            "id as id, " +
            "name as name, " +
            "id as dp_id " +
            "from department")
    @Results(value = {
            @Result(property = "employeeList", column = "dp_id", javaType = List.class, many = @Many(select = "getByDepartmentId")) // [1]
    })
    Department findDepartmentById(@Param("id") String id);

    @Select("Select " +
            "first_name as firstName, " +
            "last_name as lastName, " +
            "birth_date as date, " +
            "skills as skills " +
            "from employee emp " +
            "join department_employee dp on dp.id_employee = emp.id " +
            "where dp.id_department = uuid(#{id})")
    @Results(value = {
            @Result(property = "skills", column = "skills", typeHandler = StringArrayTypeHandler.class)
    })
    List<Employee> getByDepartmentId(@Param("id") String id);

}
{% endhighlight %}

And now it's done! just pay attention on [1] we add type as list and point a mapper query. For more
convenient way to handle sql you can try a XML mapping version, it will be in next post 


Check out the [demo repo][demo] for more info on how to set up MyBatis on spring boot 

[demo]: https://github.com/Kapetingi/mybatis-demo

