---
layout: post
title:  "MyBatis example"
date:   2020-05-10 22:21:53 +0100
categories: jekyll update
---

Whenever you want to create a application ypu have to persist data somewhere. Most serious
functions need a proper DBMS for resolve this problem, but how we can handel a OOP entities
with relation table together. There is no silver bullet to do this. The already existing solutions
is ORM frameworks as Hibernate or persistence frameworks as MyBatis. What is the difference between of
them?

* OMR - it is complex solution which provide a way to map and manage persistent entity. It's means 
it not just map tables to objects but also mange a objects life cycle. The `EntityManager` play a 
key role in that solution. It decided when to gt object from cache or put it there, how
to flush cache entities to DB and build a queries for that.

* persistent mapper - it more simple solution that just provide a easiest way to map you query result set 
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
    CONSTRAINT pk_employee PRIMARY KEY(id)
);

INSERT INTO employee
VALUES
    ('700d698d-6fa6-42d9-a5cc-a85c8c4ccbe6', 'Nick', 'Carter', '3000', null),
    ('794d22e8-e5ff-46e0-8c50-94e363bf1249', 'Bruce', 'Kane', '1000', '700d698d-6fa6-42d9-a5cc-a85c8c4ccbe6');

{% endhighlight %}

Than to fetch all employees we can use next code

{% highlight java %}

@Mapper
public interface EmployeeMapper {

    @Select("Select " +
            "first_name as firstName, " +
            "last_name as lastName " +
            "from employee")
    List<Employee> findAllEmployees();
}

{% endhighlight %}



To map entity correctly we should to assert an alias like a name for mapped entity. 

Check out the [demo repo][demo] for more info on how to set up MyBatis on spring boot 

[demo]: https://github.com/Kapetingi/mybatis-demo

