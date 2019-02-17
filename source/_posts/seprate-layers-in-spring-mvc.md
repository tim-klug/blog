---
title: seprate-layers-in-spring-mvc
date: 2019-02-17 12:33:45
tags: 
  - Spring MVC
  - separation
  - Optional
---

While moving data between a controller and a service layer or even further, Java Optional comes very handy.

## How to apply

Every method in a controller or service that is at least `package private` should return an `Optional<>`. By doing so use of these functions can be done in a very fluent way. Because a lot of the time these classes are dealing with technical concerns and resources the result can be `null`. So every time when walking through these calls a new null check is added to the code base. By applying the Optional pattern, these guard clauses are removed and a lambda expression might only be applied when the result is present. In the end the code becomes a more fluent style and readability is highly improved.

## Example

In this example we will have a controller and a service both powered by Spring.

```java

@Controller
public class UserController {

    public Optional<User> getUserById(UUID userId) {
        // ...
        return userLoadFromSomeWhere != null ? Optional.of(userLoadFromSomeWhere) : Optional.empty();
    }
}

@Service
class UserService {

    private UserController userController;

    UserService(UserController userController) {
        this.userController = userController;
    }

    Optional<User> loadUser(UUID userId) {
        return userController.getUserById(userId);
    }
}

@Component
class BusinessProblem {

    private UserService userService;

    BusinessProblem(UserService userService) {
        this.userService = userService;
    }

    void someOperationWithAUser(UUID userId) {
        userService.loadUser(userId).ifPresent(u -> u.applyMagic());
    }
}
