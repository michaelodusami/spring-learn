### **Integration Test vs Unit Test**

Both **integration tests** and **unit tests** are critical components of software testing, but they differ in focus, scope, and purpose.

---

### **Unit Test**
**Definition**:  
Unit tests validate the smallest units of an application, typically individual methods or classes, in isolation from the rest of the system.

**Focus**:  
- Tests **one unit of code** at a time (e.g., a single function or method).  
- Ensures that the logic within the unit behaves as expected.

**Scope**:  
- Very narrow, testing only the internal logic of the unit.
- Dependencies (like databases, external services) are mocked or stubbed.

**Characteristics**:
- Fast to execute since they don’t interact with external systems.
- Written and executed by developers during the development phase.
- High in quantity because every function or method should ideally have at least one unit test.

**Tools**:  
- JUnit (Java), NUnit (.NET), Pytest (Python), Jest (JavaScript).

**Example**:
Testing the `save()` method in `UserService`:
```java
@Test
void testSaveUser() {
    // Arrange
    RegisterRequest registerRequest = new RegisterRequest("John", "john@example.com", "password123");
    User user = new User();
    user.setEmail("john@example.com");
    
    when(userRepository.save(any(User.class))).thenReturn(user);

    // Act
    Optional<User> savedUser = userService.save(registerRequest);

    // Assert
    assertTrue(savedUser.isPresent());
    assertEquals("john@example.com", savedUser.get().getEmail());
}
```

---

### **Integration Test**
**Definition**:  
Integration tests validate the interactions between multiple components or modules of the system to ensure they work together as expected.

**Focus**:  
- Tests the **integration of multiple units**, including their dependencies (e.g., database, APIs, services).
- Verifies that data flows correctly across modules or layers.

**Scope**:  
- Broader than unit tests, focusing on how units interact.
- May require the actual system environment or external systems.

**Characteristics**:
- Slower than unit tests due to real interactions with external systems.
- More complex to set up because of dependencies.
- Fewer in number compared to unit tests.

**Tools**:  
- SpringBootTest, RestAssured, TestContainers (Java); Postman for APIs; Selenium for UI.

**Example**:
Testing `/register` endpoint from `UserAuthController` with database interaction:
```java
@Test
void testUserRegistrationIntegration() {
    // Arrange
    RegisterRequest registerRequest = new RegisterRequest("Alice", "alice@example.com", "password123");

    // Act & Assert
    webTestClient.post()
        .uri("/v1/auth/register")
        .contentType(MediaType.APPLICATION_JSON)
        .bodyValue(registerRequest)
        .exchange()
        .expectStatus().isCreated()
        .expectBody()
        .jsonPath("$.email").isEqualTo("alice@example.com");
}
```

---

### **Comparison Table**

| **Aspect**         | **Unit Test**                       | **Integration Test**             |
|---------------------|-------------------------------------|-----------------------------------|
| **Focus**           | Single method or function          | Multiple components interacting  |
| **Dependencies**    | Mocked or stubbed                  | Real or simulated                |
| **Execution Speed** | Fast                               | Slower                           |
| **Purpose**         | Validate correctness of a unit     | Validate integration of units    |
| **Setup Complexity**| Simple                             | Complex                          |
| **Test Coverage**   | High                               | Lower than unit tests            |
| **Examples**        | Testing a `Service` or `Repository` method | Testing REST APIs or database interaction |

---

### **Usage in Practice**
1. **Unit Tests**: Focus on testing isolated logic to ensure individual components work as intended.
2. **Integration Tests**: Ensure the system’s modules work together correctly, such as API calls interacting with a database.

Both types are complementary and essential for a robust testing strategy.
