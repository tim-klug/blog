---
title: spring_kafka_json_serde
date: 2019-02-02 21:56:05
tags: 
  - serialization
  - deserialization
  - Java Money
---

# Deserialization problems with Java money

The Spring frameworks helps with a lot of Java's overhead configuration. Most of the time things just magically work. But sometimes not.

## Problem

Spring uses Jackson for the serialization and deserialization of objects to and from json. It is very powerful and extendable. The serialized output can be placed as a value or payload in a Kafka message or used to send a response from an api. But here comes the problem. To be deserialized from a json string back to an object, the object itself must contain a parameterless constructor. And guess what, the java money class doesn't have one.

## Solution

A quick search will lead you to a repo from Zalando [jackson-datatype-money](https://github.com/zalando/jackson-datatype-money). This package provides a fix for the problem.

All you need to do is:

### 1. add Maven package

Add the maven package to your pom file.

```xml
<dependency>
    <groupId>org.zalando</groupId>
    <artifactId>jackson-datatype-money</artifactId>
    <version>${jackson-datatype-money.version}</version>
</dependency>
```

### 2. register the module

With Spring Boot this is extremely simple. All ypu need is a Bean defined in a `@Configuration` and Jackson will automatically add the module.

```java
@Configuration
class ServiceConfiguration {

    @Bean
    Module moneyModule() {
        return new MoneyModule();
    }
}
```

## Conclusion

Using Sprung Boot makes development a lot easier. Most of the configuration is done but can be extended and overridden to match a special need.