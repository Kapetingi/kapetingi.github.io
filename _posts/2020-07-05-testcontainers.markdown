---
layout: post
title:  "How to use docker in JUnit tests"
date:   2020-07-05 22:21:53 +0100
categories: jekyll update
---

Recently I was wonder what is the best approach to provide a comprehensive tests for my application.
It always better to cover with test as much logic as possible but what to do if most of application logic 
is depends on infrastructure as search engine or database. After a couple hours of googling i find out an amazing 
solution a [test containers][testcontainers] project.

In next couple of paragraphs I want to provide an example how you can write a good integration test without
a pain to manage environments for it

First of all you need to add a maven dependency. We are going to use a `PostgreSQL` in our project so that let's
add it as well as `jupiter`

{% highlight xml %}

    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>testcontainers</artifactId>
        <version>${testcontainers.version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>postgresql</artifactId>
        <version>${testcontainers.version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>${testcontainers.version}</version>
        <scope>test</scope>
    </dependency>
    
{% endhighlight %}

Ok nice! Almost there! Than lets add container

{% highlight java %}

@ExtendWith(value = {SpringExtension.class})
@SpringBootTest
@ActiveProfiles("dev")
@ContextConfiguration(initializers = {DbTest.Initializer.class}) //1
@Testcontainers  //2
public class DbTest {

    @Container //4
    public static PostgreSQLContainer postgreSQLContainer = 
            (PostgreSQLContainer) new PostgreSQLContainer("postgres:12.2")
                    .withStartupTimeout(Duration.ofSeconds(600))
                    .withClasspathResourceMapping("docker-entrypoint-initdb.d/schema.sql", "docker-entrypoint-initdb.d/1-schema.sql", BindMode.READ_ONLY)
                    .withClasspathResourceMapping("docker-entrypoint-initdb.d/data.sql", "docker-entrypoint-initdb.d/2-data.sql", BindMode.READ_ONLY);

    static class Initializer //3
            implements ApplicationContextInitializer<ConfigurableApplicationContext> {
        public void initialize(ConfigurableApplicationContext configurableApplicationContext) {
            TestPropertyValues.of(
                    "spring.datasource.url=" + postgreSQLContainer.getJdbcUrl(),
                    "spring.datasource.username=" + postgreSQLContainer.getUsername(),
                    "spring.datasource.password=" + postgreSQLContainer.getPassword()
            ).applyTo(configurableApplicationContext.getEnvironment());
        }
    }
    
{% endhighlight %}

Ok let's look on this. On line 1 we define our context initializer. If you look on line 3 you will see that
this is just class which set up our test property file with actual values. It's highly important because testcontainers
avoid collision setting up a random mapping port, as well as password and user for postgre. 

On line 4 we are setting up an actual container. Because testcontainers already has an library for postgre 
we can use a postgres container. As well we also use a `GenericContainer` but in our case is more comfartable to
use predefined one. Here we also set up a init scripts for postgre to create schema and fill data. Also we specify a
version of postgre and timeout for init.

That all! We done! After that containers will be pulled by testcontainer library and up each time when you run test. After the test 
it will clean up automatically. Enjoy!

[testcontainers]: https://www.testcontainers.org/
