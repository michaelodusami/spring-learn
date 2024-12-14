# Spring Key Features

### **Microservices**
Spring Boot simplifies the creation of microservices by providing embedded servers, configuration management, and production-ready features:
- **Example**: Create a RESTful microservice using Spring Boot:
  ```java
  @RestController
  @RequestMapping("/api")
  public class ProductController {
      @GetMapping("/products")
      public List<Product> getAllProducts() {
          return Arrays.asList(new Product(1, "Laptop"), new Product(2, "Phone"));
      }
  }
  ```
- Spring Cloud enables service discovery with Eureka, API Gateway using Zuul or Spring Cloud Gateway, and distributed tracing with Sleuth.

---

### **Reactive**
Spring WebFlux enables reactive programming for non-blocking, asynchronous request handling:
- **Example**: A reactive endpoint using Spring WebFlux:
  ```java
  @RestController
  public class ReactiveController {
      @GetMapping("/flux")
      public Flux<String> getFlux() {
          return Flux.just("A", "B", "C").delayElements(Duration.ofSeconds(1));
      }
  }
  ```

---

### **Cloud**
Spring Cloud provides tools to build cloud-native applications:
- **Example**: Configuring services with Spring Cloud Config:
  ```properties
  # application.yml in config server
  database.url=jdbc:mysql://localhost:3306/mydb
  ```
  Services can retrieve this configuration dynamically.

- **Example**: Load balancing with Spring Cloud LoadBalancer:
  ```java
  @RestController
  public class GreetingController {
      @Autowired
      private RestTemplate restTemplate;

      @GetMapping("/greeting")
      public String greeting() {
          return restTemplate.getForObject("http://user-service/hello", String.class);
      }
  }
  ```

---

### **Web Apps**
Spring MVC provides a framework for building secure, scalable web applications:
- **Example**: Building a simple web application:
  ```java
  @Controller
  public class HomeController {
      @GetMapping("/")
      public String home(Model model) {
          model.addAttribute("message", "Welcome to Spring!");
          return "home";
      }
  }
  ```

---

### **Serverless**
Spring Cloud Function allows you to write serverless functions that can run on AWS Lambda, Azure Functions, or Google Cloud:
- **Example**: A simple serverless function:
  ```java
  @Component
  public class UpperCaseFunction implements Function<String, String> {
      @Override
      public String apply(String input) {
          return input.toUpperCase();
      }
  }
  ```

---

### **Event-Driven**
Spring Kafka or Spring AMQP enables event-driven architectures:
- **Example**: Consuming messages from a Kafka topic:
  ```java
  @KafkaListener(topics = "events", groupId = "group_id")
  public void listen(String message) {
      System.out.println("Received: " + message);
  }
  ```

---

### **Batch**
Spring Batch simplifies batch processing for large-scale tasks like ETL or data migration:
- **Example**: A batch job to read, process, and write data:
  ```java
  @Configuration
  public class BatchConfig {
      @Bean
      public Job job(JobBuilderFactory jobBuilderFactory, StepBuilderFactory stepBuilderFactory) {
          Step step = stepBuilderFactory.get("step")
              .<String, String>chunk(10)
              .reader(itemReader())
              .processor(itemProcessor())
              .writer(itemWriter())
              .build();

          return jobBuilderFactory.get("job").start(step).build();
      }
  }
  ```

Each of these examples shows how Spring's tools and frameworks cater to the respective functionalities, making application development efficient and production-ready.
