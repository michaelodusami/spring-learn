### When to Use Different Types of Injection

Spring provides several injection mechanisms, each suited for specific use cases. Here’s a summary of when to use each:

---

#### 1. **Constructor-Based Injection**
- **Use When**:  
  - Dependencies are **mandatory**.
  - You want **immutability** for your bean (dependencies are set once and cannot be changed).
  - You need to validate dependencies at instantiation.
- **Example Use Case**: A service that requires a mandatory data repository.

---

#### 2. **Setter-Based Injection**
- **Use When**:  
  - Dependencies are **optional**.
  - You need to **reconfigure** or inject dependencies after bean creation.
- **Example Use Case**: A bean with optional properties that may or may not be set.

---

#### 3. **Field-Based Injection (using `@Autowired`)**
- **Use When**:  
  - You prefer concise code without setter or constructor methods.
  - Dependencies are straightforward and directly assigned to fields.
- **Caution**: Can make testing and debugging harder since the dependencies are not explicitly visible in constructors or setters.

---

#### 4. **Method Injection (`@Lookup`)**
- **Use When**:  
  - A **singleton** bean requires a **prototype** bean for each method call.
  - You need a fresh instance of the dependency on every use.
- **Example Use Case**: A task manager that uses a new command object for every task execution.

---

#### 5. **Arbitrary Method Replacement**
- **Use When**:  
  - You need to replace a specific method in a bean with a custom implementation.
  - Rarely used—prefer to refactor or use lookup methods instead.
- **Example Use Case**: Overriding specific logic dynamically for legacy code.

---

### Comprehensive Example: Combining Injection Types

#### **Scenario**:  
A **TaskManager** (singleton) is responsible for processing user requests. Each task requires a new instance of `TaskCommand` (prototype), and optional settings like logging can be applied.

---

#### **Implementation**

##### **Step 1: Define the Prototype Bean**
Create a `TaskCommand` bean that represents a new command for each task.

```java
public class TaskCommand {
    private String taskName;

    public void setTaskName(String taskName) {
        this.taskName = taskName;
    }

    public String execute() {
        return "Executing task: " + taskName;
    }
}
```

---

##### **Step 2: Define the Singleton Bean**
The `TaskManager` bean uses `@Lookup` for prototype injection and optional `Logger` using setter injection.

```java
import org.springframework.beans.factory.annotation.Lookup;
import org.springframework.beans.factory.annotation.Autowired;

public abstract class TaskManager {
    private Logger logger;

    // Optional dependency injected via setter
    @Autowired(required = false)
    public void setLogger(Logger logger) {
        this.logger = logger;
    }

    public void processTask(String taskName) {
        TaskCommand taskCommand = createTaskCommand();
        taskCommand.setTaskName(taskName);

        if (logger != null) {
            logger.log("Processing task: " + taskName);
        }

        System.out.println(taskCommand.execute());
    }

    // Abstract method for dynamic prototype injection
    @Lookup
    protected abstract TaskCommand createTaskCommand();
}
```

---

##### **Step 3: Optional Logger for Setter Injection**
Define a logger bean for optional logging functionality.

```java
public class Logger {
    public void log(String message) {
        System.out.println("[LOG]: " + message);
    }
}
```

---

##### **Step 4: Configuration**
Use Java-based configuration to define the beans and their scopes.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Scope;

@Configuration
public class AppConfig {

    // Prototype Bean
    @Bean
    @Scope("prototype")
    public TaskCommand taskCommand() {
        return new TaskCommand();
    }

    // Singleton Bean
    @Bean
    public TaskManager taskManager() {
        return new TaskManager() {};
    }

    // Optional Bean
    @Bean
    public Logger logger() {
        return new Logger();
    }
}
```

---

##### **Step 5: Test the Configuration**
Run the application and observe the behavior.

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        TaskManager taskManager = context.getBean(TaskManager.class);

        taskManager.processTask("Task1");
        taskManager.processTask("Task2");
    }
}
```

---

### **Output**

```plaintext
[LOG]: Processing task: Task1
Executing task: Task1
[LOG]: Processing task: Task2
Executing task: Task2
```

---

### Key Takeaways from the Example

1. **Constructor Injection**:  
   Use it when dependencies (like `TaskManager`) are mandatory.

2. **Setter Injection**:  
   Use it for optional dependencies like the `Logger`.

3. **Lookup Method Injection**:  
   Use it for prototype beans like `TaskCommand` that must have a fresh instance for each method invocation.

This example demonstrates when and how to combine injection types effectively in a real-world scenario. Let me know if you'd like to expand further!
