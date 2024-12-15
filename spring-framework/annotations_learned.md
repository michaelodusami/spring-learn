### Explanation of Annotations Used in the Examples  

### **Core Annotations**

#### 1. **`@Component`**
- **Purpose**: Marks a class as a Spring-managed bean.  
- **Usage**: Automatically detects the class during component scanning and registers it as a bean in the Spring container.
- **Example**:  
   ```java
   @Component
   public class MyService {
       public void performTask() {
           System.out.println("Task performed");
       }
   }
   ```

---

#### 2. **`@Configuration`**
- **Purpose**: Marks a class as a source of bean definitions.  
- **Usage**: Defines one or more beans using `@Bean` methods. Typically used for Java-based configuration.  
- **Example**:  
   ```java
   @Configuration
   public class AppConfig {
       @Bean
       public MyService myService() {
           return new MyService();
       }
   }
   ```

---

#### 3. **`@Bean`**
- **Purpose**: Indicates that a method produces a bean to be managed by the Spring container.  
- **Usage**: Used within a `@Configuration` class to explicitly define beans.  
- **Example**:  
   ```java
   @Bean
   public MyService myService() {
       return new MyService();
   }
   ```

---

#### 4. **`@ComponentScan`**
- **Purpose**: Configures component scanning in a package or a base package.  
- **Usage**: Used in a `@Configuration` class to enable Spring to find `@Component`-annotated classes automatically.  
- **Example**:  
   ```java
   @Configuration
   @ComponentScan(basePackages = "com.example")
   public class AppConfig {}
   ```

---

#### 5. **`@Autowired`**
- **Purpose**: Injects a dependency automatically into a bean.  
- **Usage**: Can be used on constructors, fields, or setter methods.  
- **Example**:  
   ```java
   @Component
   public class UserController {
       @Autowired
       private UserService userService;
   }
   ```

---

### **Advanced Bean Annotations**

#### 6. **`@Scope`**
- **Purpose**: Specifies the scope of a bean (e.g., `singleton`, `prototype`, etc.).  
- **Usage**: Used on `@Component` or `@Bean` methods to change the default bean scope.  
- **Common Scopes**:
  - `singleton`: One shared instance (default).  
  - `prototype`: A new instance for each request.  
  - `request`: A new instance per HTTP request (web applications).  
  - `session`: A new instance per HTTP session (web applications).  
- **Example**:  
   ```java
   @Component
   @Scope("prototype")
   public class PrototypeBean {}
   ```

---

#### 7. **`@Lazy`**
- **Purpose**: Delays the initialization of a bean until it is first requested.  
- **Usage**: Useful for optimizing application startup performance.  
- **Example**:  
   ```java
   @Component
   @Lazy
   public class LazyBean {
       public LazyBean() {
           System.out.println("LazyBean created!");
       }
   }
   ```

---

#### 8. **`@Primary`**
- **Purpose**: Indicates that a bean should be preferred when multiple beans of the same type exist.  
- **Usage**: Resolves ambiguity in dependency injection when `@Autowired` is used.  
- **Example**:  
   ```java
   @Component
   @Primary
   public class PrimaryBean {}
   ```

---

#### 9. **`@Qualifier`**
- **Purpose**: Specifies which bean to inject when multiple beans of the same type exist.  
- **Usage**: Used with `@Autowired` to resolve ambiguity.  
- **Example**:  
   ```java
   @Component
   public class MyService {
       @Autowired
       @Qualifier("specificBean")
       private MyBean myBean;
   }
   ```

---

### **Property and External Configuration Annotations**

#### 10. **`@Value`**
- **Purpose**: Injects values into fields from property files or environment variables.  
- **Usage**: Used on fields or parameters to bind property values.  
- **Example**:  
   ```java
   @Component
   @PropertySource("classpath:application.properties")
   public class ConfigBean {
       @Value("${app.name}")
       private String appName;
   }
   ```

---

#### 11. **`@PropertySource`**
- **Purpose**: Specifies the location of a property file for configuration values.  
- **Usage**: Used in combination with `@Value` to inject property values.  
- **Example**:  
   ```java
   @Configuration
   @PropertySource("classpath:application.properties")
   public class AppConfig {}
   ```

---

### **Combining and Customizing Annotations**

#### 12. **Custom Component Scanning**  
You can create custom annotations by combining `@Component` with additional metadata.  
Example:  
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface MyCustomComponent {}
```

Usage:  
```java
@MyCustomComponent
public class CustomService {}
```

---

### Summary Table  

| **Annotation**        | **Purpose**                                                                                   | **Common Usage**                                      |  
|------------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------|  
| `@Component`           | Marks a class as a Spring-managed bean.                                                      | Automatically detected with `@ComponentScan`.        |  
| `@Configuration`       | Defines a class as a source of bean definitions.                                              | Used to define beans with `@Bean`.                   |  
| `@Bean`                | Declares a method as a bean producer.                                                        | Creates beans in `@Configuration` classes.           |  
| `@ComponentScan`       | Configures component scanning in a package.                                                  | Automatically registers `@Component` beans.          |  
| `@Autowired`           | Injects a dependency automatically.                                                          | Used with fields, constructors, or setters.          |  
| `@Scope`               | Sets the scope of a bean (e.g., singleton, prototype).                                        | Applied to `@Component` or `@Bean` definitions.      |  
| `@Lazy`                | Delays bean initialization until first use.                                                  | Optimizes startup performance.                       |  
| `@Primary`             | Marks a bean as the default when multiple beans exist.                                       | Used to resolve ambiguity in autowiring.             |  
| `@Qualifier`           | Specifies which bean to inject when multiple options exist.                                  | Used with `@Autowired`.                              |  
| `@Value`               | Injects property values into fields.                                                         | Requires a property file and `@PropertySource`.      |  
| `@PropertySource`      | Specifies the location of property files.                                                    | Used with `@Value` for external configurations.      |  

---

### Suggested Next Steps  

1. **Experiment with Custom Scopes**: Try using `request` or `session` scope in a web application.  
2. **Integrate External Properties**: Use `@Value` and `@PropertySource` for environment-specific configurations.  
3. **Combine Annotations**: Create custom meta-annotations for specific use cases.  

