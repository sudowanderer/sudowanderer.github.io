---
title: 细说Java Validation Api
categories: java
img_path: /assets/
date: 2020-12-05 21:30:57
tags: Java-Validation-Api
---

## 概述

日常开发中经常需要对接口的入参进行参数校验，使用Java Validation API来进行校验参数，我们只需要在bean的字段上加上所需要的注解即可完成校验。

这里Java Validation API指的是

[JSR]: https://jcp.org/en/jsr/detail?id=380

规范中的**Bean Validation 2.0**。该规范中定义了许多约束性注解，如@NotBlank,@Size,@Max,@Email等，以方便对bean的字段进行对应的校验<!-- more -->

## 依赖

注意，JSR 380只是定义了规范，体现在代码中就是一些注解，其并没有对应的实现。如果不使用Spring等相关框架的话，我们需要选择适合自己的第三方实现。

### javax.validation maven坐标

```java
<!-- https://mvnrepository.com/artifact/javax.validation/validation-api -->
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>2.0.1.Final</version>
</dependency>
```

这是javax.validation的官方maven坐标。如果我们使用Spring，我们并不需要额外导入上面的依赖，Spring已经内置好了。

### 使用Springboot validation starter

在Springboot项目中，使用spring-boot-starter-validation的依赖即可完成依赖的引入。

```java
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.5.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>org.example</groupId>
    <artifactId>java-validation</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
         <!-- 添加下面这个依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
    </dependencies>
</project>
```

Spring boot项目直接使用parent标签继承Spring官方的pom，并加上spring-boot-starter-validation依赖即可。

Idea中可以按ctrl查看spring-boot-starter-validation的官方pom配置，可发现Spring官方其实使用的是**hibernate-validator**实现。

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- This module was also published with a richer model, Gradle metadata,  -->
  <!-- which should be used instead. Do not delete the following line which  -->
  <!-- is to indicate to Gradle or any Gradle module metadata file consumer  -->
  <!-- that they should prefer consuming it instead. -->
  <!-- do_not_remove: published-with-gradle-metadata -->
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-validation</artifactId>
  <version>2.3.5.RELEASE</version>
  <name>spring-boot-starter-validation</name>
  <description>Starter for using Java Bean Validation with Hibernate Validator</description>
  <url>https://spring.io/projects/spring-boot</url>
  <organization>
    <name>Pivotal Software, Inc.</name>
    <url>https://spring.io</url>
  </organization>
  <licenses>
    <license>
      <name>Apache License, Version 2.0</name>
      <url>https://www.apache.org/licenses/LICENSE-2.0</url>
    </license>
  </licenses>
  <developers>
    <developer>
      <name>Pivotal</name>
      <email>info@pivotal.io</email>
      <organization>Pivotal Software, Inc.</organization>
      <organizationUrl>https://www.spring.io</organizationUrl>
    </developer>
  </developers>
  <scm>
    <connection>scm:git:git://github.com/spring-projects/spring-boot.git</connection>
    <developerConnection>scm:git:ssh://git@github.com/spring-projects/spring-boot.git</developerConnection>
    <url>https://github.com/spring-projects/spring-boot</url>
  </scm>
  <issueManagement>
    <system>GitHub</system>
    <url>https://github.com/spring-projects/spring-boot/issues</url>
  </issueManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <version>2.3.5.RELEASE</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>org.glassfish</groupId>
      <artifactId>jakarta.el</artifactId>
      <version>3.0.3</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <!-- hibernate-validator实现-->
      <groupId>org.hibernate.validator</groupId>
      <artifactId>hibernate-validator</artifactId>
      <version>6.1.6.Final</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>
</project>
```

## 使用校验注解

JSR 380标准定义了许多注解，从字面上就可以推断出意思:

* **@NotNull** 校验字段是否不为null
* **@AssertTrue** 校验字段值是否为true
* **@Size** 校验字段值是否在设置的min和max之间。可以作用于String，Collection，Map，array数组类型。 
* **@Min** 校验字段值是否不小于设置的value
* **@Max** 校验字段值是否不大于设置的value
* **@Email** 校验字段值是否是有效邮箱地址
* **@NotEmpty** 校验字段值不是否不为null或空。可以作用于String，Collection，Map或Array类型。
* **@NotBlank** 只能作用于字符串类型，校验字段是否不为空串。和StringuUtils.isNotBlank类似。
* **@Positive** and **@PositiveOrZero** 作用于数字。校验字段值是否是整数或0。
* **@Negative** and **@NegativeOrZero** 作用于数字。校验字段值是否是负数或0。
* **@Past** and **@PastOrPresent** 校验日期是否已过或包括当前日期。
* **@Future** and **@FutureOrPresent** 校验日期是否没到或包括当前日期。

**校验的注解可以作用于集合中的元素**:

```java
List<@NotBlank String> preferences;
```

这种情况下所有被加进preferences的元素都会进行校验

**@Past和@Future 可以作用于Java8新增的LocalDate类型**

```java
private LocalDate dateOfBirth;
 
public Optional<@Past LocalDate> getDateOfBirth() {
    return Optional.of(dateOfBirth);
}
```

这里校验框架会自动拿出Optional里面的值进行校验。一般日常开发中以下两种使用方式会比较频繁

- 在Controller参数上添加校验注解
- 在Controller参数的bean类上添加校验注解，比如VO，DTO类

## 编程式校验

Springboot环境下直接在方法的参数或bean的字段上添加对应的校验注解即可自动完成校验。下面来介绍一下如何手动进行校验。

```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();
```

使用ValidatorFactory工厂来生产一个Validator。

### 定义bean

```java
public class User {
 
    @Positive
    @Min(value = 1, message = "年龄不能小于{value}")
    private int age;

    @NotBlank()
    private String name;

    @Email(message = "Email ${validatedValue} 不合法")
    private String email;
 
    // standard setters and getters 
}
```

创建一个User

```java
User user = new User();
user.setAge(0);
user.setName("");
user.setEmail("demoemail.com");
```

### message属性占位符

在定义message错误消息时，可以使用**{注解属性}**来获得注解的属性值。通过**${validatedValue}**来获得被注解字段的值。

### 校验bean

```java
Set<ConstraintViolation<User>> validate = validator.validate(user);
validate.forEach(userConstraintViolation -> System.out.println(userConstraintViolation.getMessage()));
```

validate方法返回一个set，其中包含了所有的校验错误信息。遍历打印结果：

```java
Email demoemail.com 不合法
不能为空
年龄不能小于1
必须是正数
```

## 自定义校验注解

JSR380标准中的注解并不能完全满足自己的需求。可以根据需求写自己的校验注解，并需要实现一个校验器搭配食用。下面以校验**ip地址**为例介绍。

### 注解

定义一个名为IpAddress的注解，代码如下

```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = IpAddressValidator.class)
public @interface IpAddress {
    String message() default "ip地址无效";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

@Constraint需要传入使用的校验器，下文会继续介绍。message()的default值有两种写法

- 像上面那样写死，缺点是不支持国际化
- 定义一个key，这样会从properties文件中读取该key的value来替换。比较优雅，支持国际化。下文会详细介绍

采用第二种比较优雅，扩展性强。默认的message，在使用该注解时也可以再传入mesage进行覆盖。

### 校验器

自定义的校验器实现ConstraintValidator<A extends Annotation, T>接口即可。两个泛型A是自己写的注解名，T是该注解支持的校验字段类型。IpAddressValidator的代码如下：

```java
public class IpAddressValidator implements ConstraintValidator<IpAddress, String> {

    private static final Pattern PATTERN = Pattern.compile("^(?:[0-9]{1,3}\\.){3}[0-9]{1,3}$");

    @Override
    public void initialize(IpAddress constraintAnnotation) {

    }

    @Override
    public boolean isValid(String ip, ConstraintValidatorContext constraintValidatorContext) {
        return PATTERN.matcher(ip).matches();
    }
}
```

isValid方法可以定义自己的校验逻辑。这里用正则表达式来校验ip地址的格式。

现在在user上添加ip属性，并添加上ipaddress注解

```java
    @IpAddress()
    private String ip;
```

程序运行结果

> ip地址无效
> 必须是正数
> 年龄不能小于1
> 不是一个合法的电子邮件地址
> 不能为空

### message扩展与国际化

#### 文件位置

message中定义的key，都在ValidationMessages.properties文件中。使用hibernate validator的实现的话，文件位置为

```java
D:\maven\repository\org\hibernate\validator\hibernate-validator\6.1.6.Final\hibernate-validator-6.1.6.Final.jar!\org\hibernate\validator\ValidationMessages.properties
```

可以看到，不同语言的文件用下划线和国家isocode区分分别存储


![](细说Java-Validation-Api.assets/image-20201111135907742.png)


#### 扩展message properties文件

可以自行编写messages.properties文件来扩展内置的消息。

```java
ipaddress.invalid=ip address invalid
```

#### 国际化i18n

不同语言的message文件用国家的isocode前面加上下划线来区分。例如messages_zh.properties的内容如下

```java
ipaddress.invalid=ip地址无效
```

文件结构如下

![](细说Java-Validation-Api.assets/image-20201112100701925.png)



### SpringBoot环境集成

properties文件写好了，Springboot环境下需要配置一下方可读取。

#### bean配置

```java
public class ValidationConfiguration {

    @Bean
    public MessageSource messageSource() {
        ReloadableResourceBundleMessageSource messageSource
                = new ReloadableResourceBundleMessageSource();

        messageSource.setBasename("classpath:messages");
        messageSource.setDefaultEncoding("UTF-8");
        return messageSource;
    }

    @Bean
    public LocalValidatorFactoryBean getValidator(MessageSource messageSource) {
        LocalValidatorFactoryBean bean = new LocalValidatorFactoryBean();
        bean.setValidationMessageSource(messageSource);
        return bean;
    }
}
```

主要配置两个bean，配置mesageSource以可以读取咱们自己的messages.properties文件。配置LocalValidatorFactoryBean以使用咱们自己的messageSource

#### 创建容器并获取validator

这里为了方便，使用手动创建ApplicationContext的方式来创建Spring容器。

```java
 ApplicationContext context = new AnnotationConfigApplicationContext(ValidationConfiguration.class);
        LocalValidatorFactoryBean bean = context.getAutowireCapableBeanFactory().getBean(LocalValidatorFactoryBean.class);
        Validator validator = bean.getValidator();
```

接着就可以和之前一样愉快得校验User对象了。

```java
Set<ConstraintViolation<User>> validate = validator.validate(user);
        validate.forEach(userConstraintViolation -> System.out.println(userConstraintViolation.getMessage()));
```

运行代码，可以发现messages_zh.properties文件已被正常读取。

> 不能为空
> 不是一个合法的电子邮件地址
> 必须是正数
> 年龄不能小于1
> **ip地址无效**