

### **What is a JPA Repository?**
A **JPA Repository** in Spring Data JPA is a part of the framework that provides methods to interact with the database without requiring custom SQL or boilerplate code. It’s built on top of the JPA (Java Persistence API) specification.

The `JpaRepository` interface is one of the core Spring Data interfaces that extend `CrudRepository` and `PagingAndSortingRepository`, adding several additional methods for data access and management.

---

### **Creating a Repository**

#### Example:
```java
package com.github.michaelodusami.rantms_backend.user;

import java.util.UUID;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, UUID> {
    // Additional query methods can be added here
}
```

---

### **Breaking Down the Example**

#### 1. **Extending `JpaRepository`**
```java
public interface UserRepository extends JpaRepository<User, UUID>
```

- **`JpaRepository<T, ID>`**:
  - `T`: The entity type (in this case, `User`).
  - `ID`: The type of the primary key (in this case, `UUID`).
  - By extending `JpaRepository`, this repository automatically inherits CRUD (Create, Read, Update, Delete) methods and additional functionality for pagination and sorting.

#### 2. **Annotation `@Repository`**
```java
@Repository
```
- Marks this interface as a Spring-managed **repository bean**.
- Spring will automatically create an implementation of the repository at runtime, so you don’t need to write boilerplate code.

---

### **Methods Provided by `JpaRepository`**
When you extend `JpaRepository`, you get many methods for free:

#### **CRUD Operations**
- `save(S entity)`: Save or update an entity.
- `findById(ID id)`: Find an entity by its ID.
- `existsById(ID id)`: Check if an entity exists by ID.
- `findAll()`: Fetch all entities from the database.
- `deleteById(ID id)`: Delete an entity by ID.
- `deleteAll()`: Delete all entities.

#### **Pagination and Sorting**
- `findAll(Sort sort)`: Fetch all entities sorted by a specific property.
- `findAll(Pageable pageable)`: Fetch entities with pagination.

#### **Query by Example**
- `findOne(Example<S> example)`: Fetch a single entity matching an example.
- `findAll(Example<S> example)`: Fetch all entities matching an example.

---

### **Custom Query Methods**
You can define your own query methods using the **method naming convention**. Spring Data JPA will automatically implement these methods by analyzing their names.

#### Example Custom Query Methods:
```java
public interface UserRepository extends JpaRepository<User, UUID> {

    // Find a user by username
    User findByUsername(String username);

    // Check if a user exists by email
    boolean existsByEmail(String email);

    // Find all users with a specific email domain
    List<User> findByEmailEndingWith(String domain);
}
```

- **Method Naming Convention**:
  - `findBy`: Indicates a query.
  - `Username`: The entity field being queried (must match the `User` entity field).
  - `EndingWith`: Indicates the type of query (e.g., matching, containing, greater than, less than).

Spring automatically generates the appropriate SQL based on these method names.

#### Equivalent SQL:
For the method `findByUsername(String username)`, the equivalent SQL would be:
```sql
SELECT * FROM users WHERE username = ?;
```

---

### **Advanced Queries**

If method naming conventions are not sufficient, you can write custom JPQL or native SQL queries using the `@Query` annotation.

#### Example:
```java
public interface UserRepository extends JpaRepository<User, UUID> {

    @Query("SELECT u FROM User u WHERE u.email = :email")
    User findUserByEmail(@Param("email") String email);

    @Query(value = "SELECT * FROM users WHERE username LIKE %:keyword%", nativeQuery = true)
    List<User> searchByUsername(@Param("keyword") String keyword);
}
```

- **JPQL (Java Persistence Query Language)**:
  - Entity-focused query language.
  - The first query uses `User` as the entity name and its properties (like `email`).

- **Native Query**:
  - Directly write raw SQL queries.
  - Use `nativeQuery = true` to execute queries directly against the database.

---

### **Using the Repository**

Here’s how you would use the `UserRepository` in your service or controller:

#### Example Service:
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;
import java.util.UUID;

@Service
public class UserService {

    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // Create a new user
    public User createUser(User user) {
        return userRepository.save(user);
    }

    // Find user by ID
    public Optional<User> getUserById(UUID id) {
        return userRepository.findById(id);
    }

    // Find user by username
    public User getUserByUsername(String username) {
        return userRepository.findByUsername(username);
    }

    // Get all users
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    // Delete user by ID
    public void deleteUser(UUID id) {
        userRepository.deleteById(id);
    }
}
```

---

### **When to Use Custom Repositories?**

If you need highly complex queries or operations that don't fit well in `JpaRepository`, you can define a **custom repository** by extending your `UserRepository`.

#### Example:
```java
public interface UserCustomRepository {
    List<User> findUsersWithCustomLogic();
}
```

Then implement this in a separate class:
```java
@Repository
public class UserCustomRepositoryImpl implements UserCustomRepository {
    @PersistenceContext
    private EntityManager entityManager;

    @Override
    public List<User> findUsersWithCustomLogic() {
        // Use JPQL or Criteria API
        return entityManager.createQuery("SELECT u FROM User u WHERE u.username IS NOT NULL", User.class)
                            .getResultList();
    }
}
```

---

### **Key Takeaways**
- `JpaRepository` simplifies database operations by providing built-in CRUD, pagination, and sorting methods.
- You can create custom query methods by following naming conventions or using the `@Query` annotation.
- For more advanced use cases, you can define custom repositories or use the `EntityManager`.

This combination of flexibility and ease of use makes Spring Data JPA repositories a powerful tool for working with relational databases.
