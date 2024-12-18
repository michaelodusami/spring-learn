### **What is `UserDetailsService` in Spring Security?**

`UserDetailsService` is an interface in Spring Security used for retrieving user-related data. It provides a single method, `loadUserByUsername`, that is called during the authentication process to fetch a user’s details (like username, password, and roles) from a database or any other source.

Spring Security uses this service to validate user credentials and build an **`Authentication` object**.

---

### **Core Method of `UserDetailsService`:**
```java
UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
```

- **`username`:** The username of the user trying to authenticate.
- **Returns:** A `UserDetails` object containing user information (username, password, roles, etc.).
- **Throws:** `UsernameNotFoundException` if the username is not found.

---

### **Why is it Important?**
- Central to authentication in Spring Security.
- Allows you to integrate your own user database or data source.
- Provides flexibility to include custom logic (e.g., account locking, expiration).

---

### **In Layman's Terms**
Imagine a login system:
1. A user submits their username and password.
2. The application needs to look up the user details (like their password and roles) from a database.
3. The `UserDetailsService` acts like a **lookup service**:
   - It fetches the user’s record (username, hashed password, roles).
   - Returns the record so Spring Security can validate the credentials.

---

### **How `UserDetailsService` Fits in the Authentication Flow**

1. **User Authentication Begins:**
   - A login form sends the username and password to the application.

2. **Delegation to `AuthenticationProvider`:**
   - The `AuthenticationProvider` (e.g., `DaoAuthenticationProvider`) uses the `UserDetailsService` to fetch the user’s details.

3. **Validate Credentials:**
   - Spring Security compares the password from the login form with the password in the `UserDetails` object.
   - If they match, the user is authenticated.

---

### **Implementing `UserDetailsService`:**

#### **1. Example Implementation:**

Here’s an example of a custom `UserDetailsService` that fetches user details from an in-memory database:

```java
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.core.userdetails.User;

import java.util.HashMap;
import java.util.Map;

public class CustomUserDetailsService implements UserDetailsService {

    private static final Map<String, UserDetails> users = new HashMap<>();

    static {
        // Create some sample users
        users.put("user", User.withDefaultPasswordEncoder()
                .username("user")
                .password("password")
                .roles("USER")
                .build());
        users.put("admin", User.withDefaultPasswordEncoder()
                .username("admin")
                .password("admin")
                .roles("ADMIN")
                .build());
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        UserDetails user = users.get(username);

        if (user == null) {
            throw new UsernameNotFoundException("User not found: " + username);
        }

        return user;
    }
}
```

---

#### **2. Using the Custom `UserDetailsService`:**

Configure the custom `UserDetailsService` in your Spring Security configuration.

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    @Override
    protected UserDetailsService userDetailsService() {
        return new CustomUserDetailsService();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/login", "/register").permitAll()
                .antMatchers("/admin").hasRole("ADMIN")
                .anyRequest().authenticated()
                .and()
            .formLogin()
                .defaultSuccessUrl("/home", true)
                .and()
            .logout()
                .logoutSuccessUrl("/login?logout");
    }
}
```

---

### **Customizing `UserDetails`**

Sometimes you need to store more user information than just username, password, and roles. For this, you can create a custom implementation of the `UserDetails` interface.

#### **Example: Custom `UserDetails` Implementation**

```java
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.Collection;

public class CustomUserDetails implements UserDetails {
    private String username;
    private String password;
    private boolean isActive;
    private Collection<? extends GrantedAuthority> authorities;

    public CustomUserDetails(String username, String password, boolean isActive,
                             Collection<? extends GrantedAuthority> authorities) {
        this.username = username;
        this.password = password;
        this.isActive = isActive;
        this.authorities = authorities;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    @Override
    public String getPassword() {
        return password;
    }

    @Override
    public String getUsername() {
        return username;
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return isActive;
    }
}
```

#### **Custom `UserDetailsService` Using Custom `UserDetails`:**
```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        UserEntity userEntity = userRepository.findByUsername(username);
        if (userEntity == null) {
            throw new UsernameNotFoundException("User not found: " + username);
        }

        return new CustomUserDetails(
                userEntity.getUsername(),
                userEntity.getPassword(),
                userEntity.isActive(),
                userEntity.getRoles()
        );
    }
}
```

---

### **Database Integration Example**

1. **User Entity:**
   ```java
   @Entity
   public class UserEntity {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
       private String username;
       private String password;
       private boolean active;
       @ElementCollection(fetch = FetchType.EAGER)
       private List<String> roles;
       // Getters and Setters
   }
   ```

2. **Repository:**
   ```java
   public interface UserRepository extends JpaRepository<UserEntity, Long> {
       UserEntity findByUsername(String username);
   }
   ```

3. **Fetch User from Database in `UserDetailsService`:**
   ```java
   @Service
   public class DatabaseUserDetailsService implements UserDetailsService {

       @Autowired
       private UserRepository userRepository;

       @Override
       public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
           UserEntity userEntity = userRepository.findByUsername(username);

           if (userEntity == null) {
               throw new UsernameNotFoundException("User not found: " + username);
           }

           return User.withUsername(userEntity.getUsername())
                   .password(userEntity.getPassword())
                   .roles(userEntity.getRoles().toArray(new String[0]))
                   .build();
       }
   }
   ```

---

### **Summary:**
| Feature                  | Description                                  |
|--------------------------|----------------------------------------------|
| **What is it?**          | Interface for fetching user details.         |
| **Method**               | `loadUserByUsername(String username)`.       |
| **Purpose**              | Retrieves user credentials and roles.        |
| **Default Usage**        | Works with `DaoAuthenticationProvider`.      |
| **Customization**        | Allows fetching users from custom sources.   |
| **Real-World Example**   | Load users from a database or external API.  |

