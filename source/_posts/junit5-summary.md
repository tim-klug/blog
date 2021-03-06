---
title: junit5-summary
date: 2019-02-17 13:00:00
tags:
  - Junit5
  - Testing
  - Java
---

## Dependencies

Junit5 is constructed by 3 modules: Junit-Platform, Junit-Vintage, Junit-Jupiter.

### Jupiter

The core package the provide all the unit test functionalities.

### Platform

This packages is the engine that provides the functionality to execute tests on a platform.

### Vintage

Support for Junit4.

### Maven

Maven Dependencies are:

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.3.0-M1</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.3.0-M1</version>
    <scope>test</scope>
</dependency>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.22.1</version>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-failsafe-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
    </plugins>
</build>
```

## Arrange Act Assert Triple A Pattern

### Arrange

- set up the data for the test
- different modes
  - Transient Fresh --> new for each
  - Persistence Fresh --> same, but reset
  - Persistence Share --> keep/collect

#### Once per method

- `@BeforeEach`
- `@AfterEach`

#### Once per class

- `@BeforeAll`
- `@AfterAll`

#### Default behavior

Default instances are created per test cycle/method call. This can be changed at class level.

```java
@TestInstance(TestInstance.Lifecycle.PER_METHOD) // default
@TestInstance(TestInstance.Lifecycle.PER_CLASS) // @AfterAll, @BeforeAll picked up
class Demo {

}
```

#### Naming Conventions

`setUp()` and `teatDown()` methods with annotations.

#### Nested

Nested classes can be used to structure tests. This can improve cohesion and readability.

#### Naming

With `@DisplayName("This method ...")` a custom name for the method is added to the test report. the annotation can be used on `@Test`, `@BeforeEach` and `@Nested`.

## BDD Naming

- **Arrange** --> **Given**
- **Act** --> **When**
- **Assert --> **Then**

## Assertions

- test only one logical unit at a time
- when having multiple depending assertions, they should be coupled
- be as concrete as possible inside an assertion

### combining assertions

```java
assertAll("Message", action1(), action2(), ...);
```

## disabling tests

`@Disabled("Info why")`

## Assumptions

There are 3 different types of assumptions.

```java
assumeTrue();
assumeFalse();
assumeThat(Assumption boolean, String message, Executable execute);
```

## Generic method compared Java with C#

`consumer<T>` --> `action<T>`
`supplier<T>` --> `func<T>`
`function<T, T1>` --> `func<T, T1>`

## Interfaces

`@Test` can also be placed on interfaces.

## Test Repetition

Repetition for tests can be done with a fixed number of times. Templates are provided to name these tests and add predefined placeholder.

Placeholder:

- `{displayName}`
- `{currentRepetition}`
- `{totalRepetition}`

```java
@RepeatedTest(value=5, name="{displayName} run {currentIteration}")
```

It is possible to inject the a RepetitionInfo into the test function:

```java
void test(RepetitionInfo repInfo) {
}
```

## Dynamic Tests

Dynamic tests are provided through the test `TestFactory`. The factory can not be private or static.

```java
@TestFactory
Stream<DynamicTest> test() {
    return StreamOrigin.stream()
        .map(input -> dynamicTest("name", () -> {
            // test ...
            assertThat(...);
        }));
}

// or

DynamicTest.stream(
    someOtherStream().iterator(),
    iteratorType -> someString, // <- Name
    iteratorType -> assertThat(...)
);
```

Dynamic container allows to build a tree of nested test functions.

## Parameterized Tests

Annotation: `@ParameterizedTest`

Naming for parameterized tests can be done with placeholders.

- `{index}`
- `{arguments}`
- `{0}` till `{n}`

These placeholders can be used inside the naming:

```java
@ParameterizedTest(name="Test #{index}: id{0}")
```

To use parameterized tests a maven package must be added:

`junit.jupiter-params`

Example:

```java
@ParameterizedTest
@ValueSource(longs={...})
void test(long id) {}
```

It is also possible to inject a `TestInfo` object and a `TestReporter` into the methods parameter.

```java
@ParameterizedTest
@ValueSource(longs={...})
void test(long id, TestInfo info, TestReporter reporter) {
    reporter.publishEntry("id", id);
}

