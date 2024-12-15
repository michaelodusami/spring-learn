### **1. @Getter**
- **What it does**: Automatically generates getter methods for all non-static fields of the class.
- **Why it is useful**: Eliminates boilerplate code for writing getters manually.
- **When and where to use it**: 
  - Use at the class level to generate getters for all fields.
  - Use at the field level to generate a getter for a specific field.

---

### **2. @Setter**
- **What it does**: Automatically generates setter methods for all non-final fields of the class.
- **Why it is useful**: Reduces boilerplate code for writing setters.
- **When and where to use it**:
  - Use at the class level to generate setters for all fields.
  - Use at the field level to generate a setter for a specific field.
- **Caution**: Use selectively, as allowing setters on all fields can break immutability or encapsulation.

---

### **3. @ToString**
- **What it does**: Generates a `toString()` method that includes all fields in the class by default.
- **Attributes**:
  - `exclude`: Exclude specific fields from the generated method.
  - `callSuper`: Include the `toString()` output of the superclass.
  - `onlyExplicitlyIncluded`: Includes only fields explicitly marked with `@ToString.Include`.
- **Why it is useful**: Saves time when debugging or logging by automatically creating a readable string representation.
- **When and where to use it**: Use on data classes where a custom `toString()` method would otherwise need to be written.

---

### **4. @EqualsAndHashCode**
- **What it does**: Generates `equals()` and `hashCode()` methods for the class, based on its fields.
- **Attributes**:
  - `exclude`: Exclude specific fields from the methods.
  - `callSuper`: Include the fields of the superclass in the methods.
  - `onlyExplicitlyIncluded`: Includes only fields marked with `@EqualsAndHashCode.Include`.
- **Why it is useful**: Ensures proper equality checks and compatibility with hashed collections like `HashMap` and `HashSet`.
- **When and where to use it**: Use on entities or data classes where equality depends on field values.

---

### **5. @Data**
- **What it does**: A shorthand for `@Getter`, `@Setter`, `@RequiredArgsConstructor`, `@ToString`, and `@EqualsAndHashCode`.
- **Why it is useful**: Convenient for simple classes like DTOs or POJOs that require basic functionality without custom logic.
- **When and where to use it**: Use for data classes where all fields are accessible and equality, hash code, and string representations are straightforward.

---

### **6. @NoArgsConstructor**
- **What it does**: Generates a no-argument constructor.
- **Attributes**:
  - `force`: Generates a constructor even if final fields without default values exist (sets them to default values like `0`, `false`, or `null`).
- **Why it is useful**: Required for frameworks like JPA, which often need a no-argument constructor.
- **When and where to use it**: Use on entity or model classes when a no-argument constructor is needed.

---

### **7. @AllArgsConstructor**
- **What it does**: Generates a constructor that accepts one argument for each field in the class.
- **Why it is useful**: Reduces boilerplate when creating classes with all fields as constructor parameters.
- **When and where to use it**: Use when you want a full constructor for dependency injection or value initialization.

---

### **8. @RequiredArgsConstructor**
- **What it does**: Generates a constructor that accepts arguments for all `final` fields or fields annotated with `@NonNull`.
- **Why it is useful**: Ensures proper initialization of immutable fields without boilerplate.
- **When and where to use it**: Use in classes with a mix of required (`final` or `@NonNull`) and optional fields.

---

### **9. @Value**
- **What it does**: Marks the class as immutable. Combines `@Getter`, `@AllArgsConstructor`, `@ToString`, `@EqualsAndHashCode`, and marks all fields as `private final`.
- **Why it is useful**: Enforces immutability for objects like configuration data or constants.
- **When and where to use it**: Use for classes that represent immutable data (e.g., DTOs or value objects).

---

### **10. @Builder**
- **What it does**: Implements the builder pattern for the class.
- **Why it is useful**: Provides a clean and readable way to construct complex objects with multiple optional or required fields.
- **When and where to use it**: Use for classes with many fields where constructors would be unwieldy.

---

### **11. @SneakyThrows**
- **What it does**: Allows methods to throw checked exceptions without explicitly declaring them.
- **Why it is useful**: Simplifies exception handling by bypassing the need for `try-catch` or `throws` declarations.
- **When and where to use it**: Use sparingly, as it can hide potential exceptions and make debugging harder.

---

### **12. @NonNull**
- **What it does**: Generates a null check for the annotated field or parameter.
- **Why it is useful**: Ensures that critical fields or method arguments are not null at runtime.
- **When and where to use it**: Use on fields, method parameters, or constructor arguments that must not be null.

---

### **13. @Log, @Slf4j, etc.**
- **What it does**: Generates a static logger instance for the class.
- **Variants**:
  - `@Log` (Java Util Logging)
  - `@Slf4j` (SLF4J)
  - `@Log4j2` (Log4j2)
  - `@CommonsLog` (Apache Commons Logging)
- **Why it is useful**: Simplifies the creation of logger instances for logging purposes.
- **When and where to use it**: Use at the class level to add a logging mechanism.

---

### **14. @Accessors**
- **What it does**: Allows customization of getter and setter behavior.
- **Attributes**:
  - `fluent`: Enables fluent accessors (e.g., `user.username("newName")`).
  - `chain`: Enables chaining setters (e.g., `user.setName("newName").setEmail("newEmail")`).
  - `prefix`: Strips prefixes like "m" or "is" from field names to generate method names.
- **Why it is useful**: Improves readability and flexibility of accessor methods.
- **When and where to use it**: Use when you want custom behavior for getters and setters.

---

### **15. @Cleanup**
- **What it does**: Automatically calls the `close()` method on a resource after it goes out of scope.
- **Why it is useful**: Reduces boilerplate for managing resources like streams or database connections.
- **When and where to use it**: Use in methods where resources must be closed explicitly.

---

### **16. @With**
- **What it does**: Generates "with" methods for creating copies of objects with one field changed.
- **Why it is useful**: Facilitates immutability by providing a way to create modified copies.
- **When and where to use it**: Use on immutable data classes to simplify creating variations of an object.

---

Lombok annotations drastically reduce boilerplate code and simplify Java development. However, always use them thoughtfully to maintain code readability and avoid over-reliance. Let me know if you’d like to dive deeper into any of these!
