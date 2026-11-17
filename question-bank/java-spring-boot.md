# Java & Spring Boot — Question Bank with Answers

## Core Java and OOP

### 1. What are the four pillars of OOP?
**Answer:** Encapsulation groups state and behaviour and controls access; abstraction exposes essential behaviour while hiding implementation; inheritance reuses or extends behaviour; polymorphism allows a common contract to have different implementations.

### 2. Interface vs abstract class?
**Answer:** An interface defines a contract and supports multiple implementation. Modern Java interfaces can contain abstract, default, static and private methods. An abstract class can hold instance state, constructors and shared implementation, but a class can extend only one class. Prefer interfaces for capabilities/contracts and abstract classes for shared base state.

### 3. Overloading vs overriding?
**Answer:** Overloading uses the same method name with different parameter lists and is resolved at compile time. Overriding replaces an inherited instance method with a compatible implementation and uses runtime dispatch. Static methods are hidden, not overridden.

### 4. == vs equals()?
**Answer:** For primitives, == compares values; for references it compares identity. equals() compares logical equality if overridden. If equals is overridden, hashCode must be consistent: equal objects must have equal hash codes.

### 5. ArrayList vs LinkedList?
**Answer:** ArrayList offers O(1) indexed access and good cache locality; middle insertion/removal shifts elements. LinkedList has O(n) indexed access and O(1) insertion/removal only when the node or iterator position is already known. ArrayList is usually the default choice.

### 6. HashMap vs ConcurrentHashMap?
**Answer:** HashMap is not thread-safe and permits one null key and null values. ConcurrentHashMap supports concurrent access and disallows null keys/values. Compound logic should use atomic methods such as compute or merge.

### 7. Checked vs unchecked exceptions?
**Answer:** Checked exceptions must be caught or declared; unchecked exceptions extend RuntimeException and need not be declared. Preserve causes and do not silently swallow errors.

### 8. What is immutability?
**Answer:** An immutable object cannot change after construction. Use private final fields, avoid exposing mutable internals, validate constructor input and use defensive copies. Immutability simplifies concurrency and reasoning.

### 9. What is a Java Stream?
**Answer:** A Stream is a pipeline for declarative processing: source, intermediate operations such as filter/map, and terminal operations such as collect/count/reduce. Streams are generally lazy until a terminal operation. Avoid side effects.

### 10. map() vs flatMap()?
**Answer:** map transforms each element into one result. flatMap transforms each element into a stream-like container and flattens nested results into one pipeline.

### 11. What is Optional?
**Answer:** Optional represents a value that may be absent and makes absence explicit in APIs. Use map, orElseGet and orElseThrow appropriately. Avoid calling get() without checking.

### 12. What is a race condition?
**Answer:** A race condition occurs when a result depends on unpredictable interleaving of concurrent operations. Prevent it with immutability, synchronization, locks, atomic types, thread-safe collections or database constraints/transactions.

### 13. synchronized vs volatile?
**Answer:** synchronized provides mutual exclusion and happens-before visibility for monitor-protected operations. volatile provides visibility and ordering for reads/writes but does not make compound operations such as count++ atomic.

### 14. Explain SOLID.
**Answer:** Single Responsibility: one reason to change. Open/Closed: extend without modifying stable code. Liskov Substitution: subtype preserves base contract. Interface Segregation: small client-specific interfaces. Dependency Inversion: depend on abstractions, not concrete details.

## Spring and Spring Boot

### 15. What are IoC and Dependency Injection?
**Answer:** Inversion of Control means the framework/container manages object creation and lifecycle. Dependency Injection supplies an object's collaborators from outside. Constructor injection makes required dependencies explicit and simplifies testing.

### 16. @Component, @Service, @Repository?
**Answer:** All are component-scanning stereotypes. @Component is generic; @Service communicates business-service intent; @Repository communicates persistence intent and participates in persistence exception translation.

### 17. @Controller vs @RestController?
**Answer:** @Controller is commonly used for MVC view handling. @RestController combines @Controller and @ResponseBody, so handler return values are written to the response body, commonly as JSON.

### 18. What does Spring Boot auto-configuration do?
**Answer:** It conditionally configures beans based on classpath, properties and existing beans. Explicit configuration can override it. Inspect the condition evaluation report when configuration is unexpected.

### 19. Explain the Spring request flow.
**Answer:** Request passes servlet/security filters, DispatcherServlet, handler mapping and controller. Controller delegates to service; service applies business rules and transactions and calls repository; result is serialized into an HTTP response. @ControllerAdvice can map exceptions consistently.

### 20. Why use DTOs?
**Answer:** DTOs decouple API contracts from persistence entities, prevent accidental exposure of internal fields, support validation and allow versioned request/response shapes. Do not bind untrusted JSON directly to privileged entity fields.

### 21. What is Bean Validation?
**Answer:** Use constraints such as @NotNull, @NotBlank, @Size and @Email on request DTOs, then trigger validation with @Valid. Return structured 400 responses with field-level errors. Validate business rules in the service/domain layer too.

### 22. How do you handle exceptions globally?
**Answer:** Use @RestControllerAdvice with @ExceptionHandler methods. Map validation/not-found/conflict errors to suitable status codes, return a stable error schema and correlation ID, and never expose stack traces or secrets.

### 23. What is Spring transaction management?
**Answer:** @Transactional defines a transaction boundary. By default, Spring rolls back on unchecked exceptions and Errors, not all checked exceptions. Understand proxy/self-invocation, propagation, isolation and long-running transactions.

### 24. Lazy vs eager loading in JPA?
**Answer:** Lazy loads an association when accessed; eager loads it as part of the fetch plan. Neither is universally best. Define fetch plans per use case and avoid N+1 queries.

### 25. What is the N+1 query problem?
**Answer:** One query loads a list, then one extra query per row loads related data. Detect via SQL logs/APM; fix with fetch joins, entity graphs, batch fetching or projections.

### 26. How should passwords be stored?
**Answer:** Never store plaintext or reversible encryption. Use a dedicated password hashing algorithm such as Argon2id, bcrypt or scrypt with unique salts and suitable work factors. Apply rate limiting and secure reset flows.

### 27. Unit test vs integration test?
**Answer:** Unit tests isolate a class and mock external dependencies. Integration tests verify collaboration with real infrastructure or realistic substitutes such as a database or HTTP service. Test business behaviour, edge cases and authorization.

### 28. How would you secure a Spring REST API?
**Answer:** Use a vetted identity provider/OIDC, validate token issuer/audience/signature/expiry, enforce authorization at endpoint and service/data layer, validate input, use parameterized persistence, HTTPS, rate limits, secret management, audit logs and dependency updates.

## Quick revision
- Constructor injection is explicit, testable and supports final dependencies.
- Singleton is the default Spring bean scope per application context.
- @Transactional(readOnly=true) is a hint, not an authorization control.
- Optimistic locking uses a version column to detect conflicting updates; pessimistic locking obtains database locks.
