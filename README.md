# Create-Framework-for-software-engineering

# CampusBoot

A tiny annotation-driven mini-framework built with **Java Reflection** and **custom annotations**, showing how Spring Boot works under the hood. No external libraries are used.

| | |
|---|---|
| **Author** | Meherun Nesa Mila |
| **Student ID** | IT24002 |
| **Department** | ICT, Mawlana Bhasani Science and Technology University (MBSTU) |
| **Language** | Java 11+ |

---

## 1. Overview

CampusBoot removes common boilerplate (creating objects, reading config, validating data, wiring URLs to methods) using annotations that are read at runtime through reflection.

| CampusBoot | Applies to | Purpose | Spring equivalent |
|---|---|---|---|
| `@Component` | Class | Marks a class to be created and managed by the container | `@Component` / `@Service` |
| `@Value` | Field | Injects a configuration value into a field | `@Value("${...}")` |
| `@Range` | Field | Validates that an `int` field is within `min`..`max` | `@Min` / `@Max` (Bean Validation) |
| `@Route` | Method | Maps a URL path to a method | `@GetMapping` / `@RequestMapping` |

`@Route` supports both `path()` (as in the specification) and `value()` (needed because the starter code writes `@Route("/hello")`); `path()` takes priority.

All four annotations use `@Retention(RetentionPolicy.RUNTIME)` so they are visible to reflection.

---

## 2. Project Structure

```
IT24002_CampusBoot/
├── Component.java        # Part A - annotation
├── Value.java            # Part A - annotation
├── Range.java            # Part A - annotation
├── Route.java            # Part A - annotation
├── Container.java        # Part B - IoC container + value injection
├── Validator.java        # Part C - @Range validator
├── Router.java           # Part D - @Route dispatcher
├── GradeController.java  # Starter code (unmodified)
├── ReportPrinter.java    # Starter code (unmodified, no annotation)
├── Student.java          # Starter code (unmodified)
├── Main.java             # Starter code (unmodified)
├── answers.txt           # Part E - reflection answers
└── README.md
```

---

## 3. How It Works

### Container (IoC + Value Injection)
- `scan(Class<?>... classes)` checks each class for `@Component`. If present, it creates an instance through the no-arg constructor and stores it in a `Map<String,Object>`. The bean name is `value()`, or the simple class name with a lowercase first letter if `value()` is empty. Classes without `@Component` are skipped.
- `injectValues(Map<String,String> config)` finds fields annotated with `@Value`, looks up the key in the config, converts to `int` when needed, and sets the field using `setAccessible(true)`.
- `getBean(String name)` returns a bean by name.

### Validator
`Validator.validate(Object)` reads every `@Range` field. If an `int` value is outside `[min, max]`, it adds a message in this format:

```
fieldName - message (actual: value)
```

An empty list means the object is valid.

### Router
`Router.dispatch(Container, String path)` searches all beans for a method annotated with `@Route` whose `path()` equals the request path, invokes it, and returns the result. If nothing matches, it returns `404 Not Found: <path>`.

---

## 4. Build and Run

Requires JDK 11 or newer.

```bash
javac *.java
java Main
```

---

## 5. Expected Output

```
[Container] Registered: gradeController (GradeController)
[Container] Skipped: ReportPrinter (no @Component)
[Container] Injected: app.name -> GradeController.appName
[Container] Injected: app.passMark -> GradeController.passMark
--- Validation ---
VIOLATION: score - Score must be between 0 and 100 (actual: 120)
OK
VIOLATION: semester - Value out of range (actual: 15)
--- Routing ---
[Router] /hello -> Welcome to CampusBoot
[Router] /status -> Pass mark is 40
[Router] /unknown -> 404 Not Found: /unknown
```

---

## 6. Reflection Questions

The answers to Part E are in [`answers.txt`](answers.txt).

---

## 7. Submission

Package all `.java` files and `answers.txt` into one archive named:

```
IT24002_CampusBoot.zip
```

---

## 8. Academic Integrity

This is an individual assignment. The code was written and understood by the author.
