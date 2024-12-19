
### **Using Mockito**
#### Purpose:
Mockito will mock dependencies to test the behavior of methods interacting with the `User` entity without relying on a database or actual implementations.

#### Example:

```java
import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import com.github.michaelodusami.fakeazon.modules.user.entity.User;
import org.junit.jupiter.api.Test;
import java.util.Optional;

public class UserServiceTest {

    @Test
    void testFindUserById() {
        // Mock repository
        UserRepository userRepositoryMock = mock(UserRepository.class);

        // Stub response
        User mockUser = new User(1L, "John Doe", "john@example.com", "password", Set.of("USER"), null, null);
        when(userRepositoryMock.findById(1L)).thenReturn(Optional.of(mockUser));

        // Service using the mocked repository
        UserService userService = new UserService(userRepositoryMock);
        Optional<User> result = userService.findUserById(1L);

        // Assertions
        assertTrue(result.isPresent());
        assertEquals("John Doe", result.get().getName());

        // Verify interaction
        verify(userRepositoryMock, times(1)).findById(1L);
    }
}
```

#### Key Points:
- **Mocks the UserRepository.**
- **Focus:** Isolated testing of service logic.
- **Dependency**: No need for a real database.

---

### **Using MockMvc**
#### Purpose:
MockMvc will simulate HTTP requests to a REST controller that handles operations on the `User` entity.

#### Example:

```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

import com.github.michaelodusami.fakeazon.modules.user.entity.User;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

@WebMvcTest(UserController.class)
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testGetUserById() throws Exception {
        mockMvc.perform(get("/users/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name").value("John Doe"))
                .andExpect(jsonPath("$.email").value("john@example.com"));
    }
}
```

#### Assumptions:
The `UserController` responds to `GET /users/1` with user details.

#### Key Points:
- **Mocks HTTP requests to the controller.**
- **Focus:** Testing the controller layer and API responses.
- **Dependency**: No actual service or repository logic is involved (can be mocked).

---

### **Using TestContainers**
#### Purpose:
TestContainers provides a real database in a Docker container to validate `User`-related operations end-to-end.

#### Example:

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.DynamicPropertyRegistry;
import org.springframework.test.context.DynamicPropertySource;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

import java.util.Optional;

import static org.junit.jupiter.api.Assertions.assertTrue;

@SpringBootTest
@Testcontainers
public class UserRepositoryTest {

    @Container
    private static PostgreSQLContainer<?> postgresContainer = new PostgreSQLContainer<>("postgres:15.3")
            .withDatabaseName("testdb")
            .withUsername("testuser")
            .withPassword("testpass");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgresContainer::getJdbcUrl);
        registry.add("spring.datasource.username", postgresContainer::getUsername);
        registry.add("spring.datasource.password", postgresContainer::getPassword);
    }

    @Autowired
    private UserRepository userRepository;

    @Test
    void testSaveUser() {
        User user = User.builder()
                .name("Jane Doe")
                .email("jane@example.com")
                .password("securepassword")
                .roles(Set.of("ADMIN"))
                .build();

        userRepository.save(user);
        Optional<User> fetchedUser = userRepository.findById(user.getId());

        assertTrue(fetchedUser.isPresent());
        assertTrue(fetchedUser.get().getRoles().contains("ADMIN"));
    }
}
```

#### Key Points:
- **Uses a real PostgreSQL database in a Docker container.**
- **Focus:** Integration testing of repository and database interaction.
- **Dependency**: Requires Docker installed and running.

---

### **Comparison Table**

| **Aspect**             | **Mockito**                               | **MockMvc**                              | **TestContainers**                        |
|-------------------------|-------------------------------------------|------------------------------------------|-------------------------------------------|
| **Purpose**            | Unit testing with mocked dependencies     | Testing the web layer (controllers)      | Integration testing with real dependencies|
| **Dependencies**       | No external systems needed                | Simulates HTTP requests                  | Requires Docker for containerized testing |
| **Testing Scope**      | Isolated logic testing                    | Web layer (controllers, APIs)            | End-to-end with a real database           |
| **Execution Speed**    | Fast                                      | Fast                                     | Slower (due to container startup)         |
| **Complexity**         | Low                                       | Moderate                                 | High                                      |
| **When to Use**        | Service or utility class testing          | API/Controller testing                   | Full integration testing                  |

---

### **Next Steps**
- Use **Mockito** for unit testing services with `UserRepository`.
- Use **MockMvc** to test REST APIs in `UserController`.
- Use **TestContainers** to validate repository operations in a real database setup.
