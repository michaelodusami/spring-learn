### Quick Overview of Dependencies in Spring  

In Spring, **dependencies** refer to the other objects (beans) that a bean needs to perform its tasks. Spring simplifies managing these dependencies through **Dependency Injection (DI)**, enabling loose coupling between components.

---

### **Key Concepts**

1. **Dependency Injection (DI)**  
   - **Definition**: A way to supply the dependencies a bean needs.  
   - **Types**:  
     - **Constructor Injection**: Dependencies are passed via a constructor.  
     - **Setter Injection**: Dependencies are set via setter methods.  
     - **Field Injection**: Dependencies are injected directly into fields (usually with `@Autowired`).  

---

2. **Dependencies and Configuration in Detail**  
   - Specify dependencies using XML, annotations, or Java-based configuration.  
   - Example with `@Autowired`:  
     ```java
     @Component
     class UserService {
         @Autowired
         private UserRepository userRepository;
     }
     ```

---

3. **Using `depends-on`**  
   - Ensures that a bean is initialized only after another bean is initialized.  
   - Useful for ordering initialization of beans with interdependencies.  
   - **XML Example**:  
     ```xml
     <bean id="beanA" class="com.example.BeanA" depends-on="beanB"/>
     ```

---

4. **Lazy-Initialized Beans**  
   - Beans are created only when they are first requested.  
   - Optimizes application startup time.  
   - **Annotation Example**:  
     ```java
     @Lazy
     @Component
     public class LazyBean {}
     ```

---

5. **Autowiring Collaborators**  
   - Spring automatically resolves and injects dependencies into beans.  
   - Annotations: `@Autowired`, `@Qualifier`, `@Primary`.  
   - **Example**:  
     ```java
     @Component
     class ServiceA {
         @Autowired
         private ServiceB serviceB;
     }
     ```

---

6. **Method Injection**  
   - A technique for injecting dependencies into methods (useful for dynamically changing dependencies).  
   - Often used with abstract methods or lookup methods.  
   - **Example**:  
     ```java
     @Component
     public class ServiceA {
         public void performTask(ServiceB serviceB) {
             // Injected dependency used here
         }
     }
     ```

---

### Summary Table

| **Feature**              | **Purpose**                                         | **Common Usage**                                    |  
|---------------------------|-----------------------------------------------------|----------------------------------------------------|  
| **Dependency Injection**  | Supplies dependencies to beans.                    | Constructor, setter, or field injection.           |  
| **depends-on**            | Controls bean initialization order.                | Used for dependent beans.                          |  
| **Lazy Initialization**   | Delays bean creation until first use.              | Annotate with `@Lazy`.                             |  
| **Autowiring**            | Automatically injects dependencies.                | Use `@Autowired` with optional `@Qualifier`.       |  
| **Method Injection**      | Injects dependencies into specific methods.        | Use abstract or non-constructor methods.           |  

Try experimenting with these features to see how dependencies are managed dynamically!
