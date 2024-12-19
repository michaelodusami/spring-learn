Let’s dive into the **`when`** method from Mockito.

---

### **What it is**
The `when` method is a static method provided by Mockito to set up mock behavior. It specifies what a mock object should return (or do) when a specific method is called with certain arguments.

---

### **What it does**
- It sets expectations on mocked methods.
- Allows you to simulate specific behaviors of dependencies without executing their actual implementation.
- You chain it with methods like `thenReturn` or `thenThrow` to define the output or exception for the specified behavior.

---

### **Where to use it**
1. **In Unit Tests:** When you want to test a class that depends on another class (the dependency).
2. **Mocking External Calls:** For methods making database calls, API calls, or other I/O operations.
3. **Simulating Exceptions:** To test how code handles unexpected scenarios, like exceptions.

---

### **What it does in Layman Terms**
Imagine you're playing a game and ask your friend (mocked dependency), "What happens if I press this button?" Your friend responds with a specific answer you’ve pre-decided. The `when` method is how you tell your friend to give you that answer.

---

### **Code Example**

Here’s how to use `when` in a typical test case:

```java
import org.junit.jupiter.api.*;
import org.mockito.*;
import static org.mockito.Mockito.*;

class Calculator {
    private final DataFetcher dataFetcher;

    public Calculator(DataFetcher dataFetcher) {
        this.dataFetcher = dataFetcher;
    }

    public int add(int a, int b) {
        return a + b;
    }

    public int fetchAndAdd(int a) {
        return a + dataFetcher.getNumber();
    }
}

interface DataFetcher {
    int getNumber();
}

public class MockitoWhenTest {

    @Mock
    private DataFetcher dataFetcher;

    private Calculator calculator;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        calculator = new Calculator(dataFetcher);
    }

    @Test
    void testFetchAndAdd() {
        // Arrange
        when(dataFetcher.getNumber()).thenReturn(10);

        // Act
        int result = calculator.fetchAndAdd(5);

        // Assert
        Assertions.assertEquals(15, result);
    }
}
```

---

### **Table Summary**

| **Aspect**            | **Details**                                                                                       |
|-----------------------|---------------------------------------------------------------------------------------------------|
| **Purpose**           | Defines behavior of mocked methods when called with specific arguments.                          |
| **Class**             | `org.mockito.Mockito`                                                                            |
| **Return Type**       | Returns a **stubber**, which can be chained with methods like `thenReturn` or `thenThrow`.        |
| **Use Case**          | Mocking method calls in tests to simulate responses or exceptions.                                |
| **Chained With**      | `thenReturn`, `thenThrow`, `thenAnswer`, etc.                                                     |
| **Best Practices**    | Use with specific arguments or matchers like `anyInt()` to avoid overly generic or fragile mocks. |

---

### **How It Works in the Example**
1. `when(dataFetcher.getNumber()).thenReturn(10);`  
   - Configures the mock `dataFetcher` to return `10` whenever the `getNumber` method is called.
   
2. The `fetchAndAdd` method in the `Calculator` calls `dataFetcher.getNumber()` and adds the result to the input (`5` + `10 = 15`).

3. The `Assertions.assertEquals(15, result);` verifies that the mocked behavior worked as expected.

---

### **Next Steps**
To explore further:
1. Use argument matchers with `when`, such as `anyInt()` or `eq(value)`.
2. Understand **stubbing void methods** with `doAnswer` or `doThrow`.
3. Investigate **`thenThrow`** to simulate exceptions for negative test cases.

Would you like an example with argument matchers or more advanced usage?
