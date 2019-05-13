# Spring workshop

### Generate project
Use https://start.spring.io/ to generate an empty Spring project

Complete the following project metadata:
* Group: com.cegeka.academy
* Artifact: spring

### IoC and DI example
Create the following package: com.cegeka.academy.spring.beans<br />
Inside of this package, create the following classes:<br />

```java
@Component
public class Master {
    private Dog dog;

    @Autowired
    public void setDog(Dog dog) {
        this.dog = dog;
        System.out.println("My dog's name is: " + dog.getName());
    }
}
```
```java
@Component
public class Dog {

    private String name;

    public Dog() {
        this.name = "Rex";
    }

    public String getName() {
        return name;
    }
}
```

### XML based configuration
Create applicationContext.xml in resources folder:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean class="com.cegeka.academy.spring.beans.AnotherMaster" name="anotherMaster1">
        <constructor-arg name="prototypeDog" ref="prototypeDog"/>
    </bean>

    <bean class="com.cegeka.academy.spring.beans.AnotherMaster" name="anotherMaster2">
        <constructor-arg name="prototypeDog" ref="prototypeDog"/>
    </bean>

    <bean class="com.cegeka.academy.spring.beans.PrototypeDog" name="prototypeDog"/>
</beans>
```

In Application class, add the following annotation: *@ImportResource({"classpath*:applicationContext.xml"})

Create the PrototypeDog and AnotherMaster classes:
```java
public class PrototypeDog {
    private String name;

    public PrototypeDog() {
        this.name = "Rex" + new Date().getTime();
    }

    public String getName() {
        return name;
    }
}
```
```java
public class AnotherMaster {

    PrototypeDog prototypeDog;

    public AnotherMaster(PrototypeDog prototypeDog) {
        this.prototypeDog = prototypeDog;
        System.out.println(prototypeDog.getName());
    }
}
```

### Prototype scope vs Singleton scope
Which will be the effect of:
```xml
<bean class="com.cegeka.academy.spring.beans.PrototypeDog" name="prototypeDog" scope="prototype"/>
```

### Lazy loading
Which will be the effect of:
```java
@Component
@Lazy
public class Master {
    ...
}
```

### Handle multiple beans of the same type
Create a new interface called *Cat* and two implementations of it, called *MyFirstCat* and *MySecondCat*.<br />
Add a new method in Cat interface, called *meow()* and add the implementations of this method in the concrete classes (MyFirstCat and MySecondCat).

Use the appropiate annotations to inject one of the cats defined above, in the *Master* bean.

Is it possible to inject all the cats in the *Master* bean?

### Using @Value annotation
In application.properties, add: *cat1.name=Tom*<br />
In MyFirstCat class, you can use the property defined above:
```java
    @Value("${cat1.name}")
    private String name;
```

### Using starters
The next exercise involves connecting our application to a database. We will create a new table where we will insert 10 random numbers. Finally, we will read and display the numbers inserted in the database.<br />
Further on, we will simulate how to work with DB, using JDBC. <br />
For this, we will use an in memory database(H2):
```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>
```
Spring Boot supports H2, an in-memory relational database engine, and automatically creates a connection. Because we are using spring-jdbc, Spring Boot automatically creates a JdbcTemplate. The @Autowired JdbcTemplate field automatically loads it and makes it available.<br />
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```
If the Application class implements Spring Boot’s CommandLineRunner, it will execute the run() method after the application context is loaded up.
```java
    public class Application implements CommandLineRunner
```

In run() method, we can add our business specific implementation.
```java
    @Override
    public void run(String... strings) throws Exception {
        //create table
        jdbcTemplate.execute("CREATE TABLE NUMBERS(ID SERIAL, NR VARCHAR(255))");
    
        //generate random numbers
        ...
    
        //save values in the database
        ...
    
        //read and list all values from database
        ...
    }
```