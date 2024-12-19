Let’s explore the **`verify(Object mock, VerificationMode mode)`** method from Mockito.

---

### **What it is**
The `verify` method is used to confirm interactions with mocked objects. It checks whether a specific method on a mock was called, how many times it was called, and with what arguments.

---

### **What it does**
- Ensures that expected methods are invoked on a mock object.
- Confirms the number of times a method is called (e.g., once, twice, or never).
- Validates that no unintended interactions occurred.
- Allows fine-grained verification of method calls, improving test accuracy.

---

### **Where to use it**
1. **Interaction Verification:** When you want to ensure a method is called as expected.
2. **Behavior-Driven Development (BDD):** Validating system behavior by testing how components interact.
3. **Avoid Redundant Calls:** To verify no unnecessary or duplicate calls are made to dependencies.
4. **Testing Side Effects:** For methods that perform actions like logging, writing to a database, or sending an email.

---

### **What it does in Layman Terms**
Imagine you’re directing a play and want to ensure an actor (mock object) performs their line exactly three times. You use `verify` to check if they followed the script or went off-track.

---

### **Code Example**

```java
import org.junit.jupiter.api.*;
import org.mockito.*;
import static org.mockito.Mockito.*;

class NotificationService {
    private final EmailSender emailSender;

    public NotificationService(EmailSender emailSender) {
        this.emailSender = emailSender;
    }

    public void notifyUser(String userEmail) {
        if (userEmail != null) {
            emailSender.sendEmail(userEmail);
        }
    }
}

interface EmailSender {
    void sendEmail(String email);
}

public class MockitoVerifyTest {

    @Mock
    private EmailSender emailSender;

    private NotificationService notificationService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        notificationService = new NotificationService(emailSender);
    }

    @Test
    void testNotifyUser() {
        // Act
        notificationService.notifyUser("user@example.com");
        notificationService.notifyUser("user@example.com");

        // Assert
        verify(emailSender, times(2)).sendEmail("user@example.com");
    }

    @Test
    void testNotifyUserNeverCalled() {
        // Act
        notificationService.notifyUser(null);

        // Assert
        verify(emailSender, never()).sendEmail(anyString());
    }
}
```

---

### **Table Summary**

| **Aspect**                | **Details**                                                                                 |
|---------------------------|---------------------------------------------------------------------------------------------|
| **Purpose**               | Validates interactions with mocked objects, ensuring correct method calls and argument use. |
| **Class**                 | `org.mockito.Mockito`                                                                      |
| **Parameters**            | - **`mock`**: The mocked object.                                                           |
|                           | - **`mode`**: Specifies the number of times (e.g., `times(1)`, `never()`, `atLeastOnce()`). |
| **Use Case**              | To confirm expected method calls and prevent unintended interactions.                      |
| **Best Practices**        | Verify interactions only when meaningful to test behavior; avoid overusing in logic tests. |

---

### **How It Works in the Example**

1. **`verify(emailSender, times(2)).sendEmail("user@example.com");`**  
   - Confirms that the `sendEmail` method was called exactly twice with the argument `"user@example.com"`.
   
2. **`verify(emailSender, never()).sendEmail(anyString());`**  
   - Ensures that the `sendEmail` method was never called with any string argument.

---

### **Verification Modes in Mockito**

| **Mode**            | **Description**                                                                                      | **Example**                   |
|---------------------|------------------------------------------------------------------------------------------------------|-------------------------------|
| `times(n)`          | Verifies the method was called exactly `n` times.                                                    | `verify(mock, times(3))`      |
| `never()`           | Ensures the method was not called at all.                                                            | `verify(mock, never())`       |
| `atLeast(n)`        | Verifies the method was called at least `n` times.                                                   | `verify(mock, atLeast(2))`    |
| `atLeastOnce()`     | Confirms the method was called at least once.                                                        | `verify(mock, atLeastOnce())` |
| `atMost(n)`         | Checks the method was called no more than `n` times.                                                | `verify(mock, atMost(5))`     |
| `only()`            | Validates that no other method was called on the mock apart from the specified ones.                 | `verify(mock).method();`      |

---

### **Next Steps**
1. Explore advanced `verify` options like argument matchers (`eq(value)`, `any()`) to validate parameters.
2. Combine `verify` with `inOrder()` to verify call sequences on multiple mocks.
3. Investigate BDDMockito's `then()` for behavior-driven test assertions.

Let me know if you'd like more examples or further explanation!
