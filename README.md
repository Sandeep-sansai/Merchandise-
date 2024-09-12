[Role] -- (1:M) -- [Credentials] -- (1:1) -- [UserInfo]
   |                                  |
   |                                  |
[City] -- (M:1) -- [Flight] -- (1:M) -- [FlightInfo] -- (M:1) -- [Booking] -- (1:1) -- [Canceled]
                                            |                                |
                                            |                                |
                                          [Seat]                         [Payment]




To establish relationships (mappings) between the tables in your airline management system using Spring JPA, we will use JPA annotations such as `@OneToOne`, `@OneToMany`, `@ManyToOne`, and `@ManyToMany` to define the associations between the entities. This approach will reflect the relationships defined in your database schema, like primary and foreign keys.

### **Relationships Overview:**

Based on your schema:

1. **`UserInfo` and `Booking`:** One user can have multiple bookings (`OneToMany`).
2. **`Flight` and `FlightInfo`:** One flight can have multiple flight instances (`OneToMany`).
3. **`FlightInfo` and `City`:** A flight info has one source city and one destination city (`ManyToOne`).
4. **`Booking` and `FlightInfo`:** A booking is associated with one flight info (`ManyToOne`).
5. **`Booking` and `Seat`:** A booking can have multiple seats (`OneToMany`).
6. **`Booking` and `Status`:** A booking has one status (`ManyToOne`).
7. **`Seat` and `FlightInfo`:** One flight info can have multiple seats (`OneToMany`).
8. **`UserInfo` and `Role`:** A user can have one role (`ManyToOne`).
9. **`Credentials` and `UserInfo`:** One-to-one relationship (`OneToOne`).

Let's define these relationships in your entity classes.

### **Entity Mappings:**

#### 1. **UserInfo Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
@Table(name = "UserInfo")
public class UserInfo {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long userId;

    @Column(name = "name", nullable = false)
    private String name;

    @Column(name = "age", nullable = false)
    private int age;

    @ManyToOne
    @JoinColumn(name = "role_id")
    private Role role;

    @OneToOne(mappedBy = "userInfo", cascade = CascadeType.ALL)
    private Credentials credentials;

    @OneToMany(mappedBy = "userInfo", cascade = CascadeType.ALL)
    private Set<Booking> bookings;

    // Getters and Setters
}
```

#### 2. **Role Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
@Table(name = "Role")
public class Role {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long roleId;

    @Column(name = "role_name", nullable = false)
    private String roleName;

    @OneToMany(mappedBy = "role", cascade = CascadeType.ALL)
    private Set<UserInfo> users;

    // Getters and Setters
}
```

#### 3. **Credentials Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;

@Entity
@Table(name = "Credentials")
public class Credentials {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long credentialsId;

    @Column(name = "username", nullable = false, unique = true)
    private String username;

    @Column(name = "password", nullable = false)
    private String password;

    @OneToOne
    @JoinColumn(name = "user_id")
    private UserInfo userInfo;

    // Getters and Setters
}
```

#### 4. **Flight Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
@Table(name = "Flight")
public class Flight {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long flightId;

    @OneToMany(mappedBy = "flight", cascade = CascadeType.ALL)
    private Set<FlightInfo> flightInfos;

    // Other fields (source, destination, etc.)

    // Getters and Setters
}
```

#### 5. **FlightInfo Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
@Table(name = "FlightInfo")
public class FlightInfo {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long flightInfoId;

    @ManyToOne
    @JoinColumn(name = "flight_id")
    private Flight flight;

    @ManyToOne
    @JoinColumn(name = "source_city_id")
    private City sourceCity;

    @ManyToOne
    @JoinColumn(name = "destination_city_id")
    private City destinationCity;

    @OneToMany(mappedBy = "flightInfo", cascade = CascadeType.ALL)
    private Set<Booking> bookings;

    @OneToMany(mappedBy = "flightInfo", cascade = CascadeType.ALL)
    private Set<Seat> seats;

    // Other fields (date, time, etc.)

    // Getters and Setters
}
```

#### 6. **City Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
@Table(name = "City")
public class City {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long cityId;

    @Column(name = "city_name", nullable = false)
    private String cityName;

    @OneToMany(mappedBy = "sourceCity", cascade = CascadeType.ALL)
    private Set<FlightInfo> sourceFlights;

    @OneToMany(mappedBy = "destinationCity", cascade = CascadeType.ALL)
    private Set<FlightInfo> destinationFlights;

    // Getters and Setters
}
```

#### 7. **Booking Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
@Table(name = "Booking")
public class Booking {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long bookingId;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private UserInfo userInfo;

    @ManyToOne
    @JoinColumn(name = "flight_info_id")
    private FlightInfo flightInfo;

    @ManyToOne
    @JoinColumn(name = "status_id")
    private Status status;

    @OneToMany(mappedBy = "booking", cascade = CascadeType.ALL)
    private Set<Seat> seats;

    // Other fields (booking date, time, total passengers, etc.)

    // Getters and Setters
}
```

#### 8. **Seat Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;

@Entity
@Table(name = "Seat")
public class Seat {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long seatId;

    @ManyToOne
    @JoinColumn(name = "booking_id")
    private Booking booking;

    @ManyToOne
    @JoinColumn(name = "flight_info_id")
    private FlightInfo flightInfo;

    // Other fields (seat number, class, etc.)

    // Getters and Setters
}
```

#### 9. **Status Entity**

```java
package com.example.airline.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
@Table(name = "Status")
public class Status {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long statusId;

    @Column(name = "status_name", nullable = false)
    private String statusName;

    @OneToMany(mappedBy = "status", cascade = CascadeType.ALL)
    private Set<Booking> bookings;

    // Getters and Setters
}
```

### **Explanation of Mappings:**

- **`@OneToOne`:** Used for a one-to-one relationship (e.g., `UserInfo` and `Credentials`).
- **`@OneToMany` and `@ManyToOne`:** Used for one-to-many and many-to-one relationships (e.g., `UserInfo` and `Booking`).
- **`@ManyToMany`:** If any many-to-many relationships are needed, use a join table to manage the mapping.

### **Circular Mapping Considerations:**

To handle circular references in bidirectional mappings, use the `mappedBy` attribute in one of the entities to indicate the owning side. Also, be cautious of recursive serialization or fetching issues by using appropriate fetch types (`LAZY` vs. `EAGER`) and handling circular references in your data transfer objects (DTOs).

### **Conclusion:**

These mappings ensure that all entities in your airline management system are properly connected based on their relationships, supporting complex queries, cascading operations, and maintaining data integrity.




Let's explore how to define mappings between the tables using foreign keys and composite keys in the context of your airline management system with Spring JPA. 

### **Understanding Foreign Key and Composite Key Mappings:**

1. **Foreign Key**: A foreign key is a column or a group of columns in one table that uniquely identifies a row in another table. It's used to establish and enforce a link between the data in the two tables.

2. **Composite Key**: A composite key is a primary key composed of two or more columns. It is used when a single column is not sufficient to uniquely identify a row.

### **Mappings Using Foreign Keys and Composite Keys:**

Let's define how these mappings can be done in the entities using JPA annotations.

#### **1. Foreign Key Mapping:**

Foreign key relationships are established using the following JPA annotations:

- **`@ManyToOne`**: This annotation defines a many-to-one relationship, where a foreign key is stored in the source table to reference a record in the target table.
- **`@OneToMany`**: This annotation establishes the one-to-many side of the relationship but does not directly define the foreign key in the table.

Let's look at the entities and see how foreign keys are defined:

##### **Example: `Booking` and `UserInfo` (Foreign Key Mapping)**

- **`Booking` table** has a foreign key reference to the **`UserInfo` table**.
- This is a **Many-to-One** relationship because each booking is linked to one user, but one user can have multiple bookings.

Here's how to define the relationship:

```java
// Booking entity
@Entity
@Table(name = "Booking")
public class Booking {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long bookingId;

    @ManyToOne // Many bookings for one user
    @JoinColumn(name = "user_id", referencedColumnName = "userId") // Foreign key in Booking table referencing UserInfo table
    private UserInfo userInfo;

    // Other fields, getters, and setters
}
```

- The **`@JoinColumn`** annotation defines the foreign key column (`user_id`) in the **`Booking`** table, which references the **`userId`** column in the **`UserInfo`** table.

#### **2. Composite Key Mapping:**

Composite keys are defined when you want to use multiple columns as a primary key. In JPA, you typically define a composite key in a separate class using the **`@Embeddable`** annotation and then use the **`@EmbeddedId`** annotation in the entity to represent the composite key.

##### **Example: `Seat` Entity with a Composite Key**

Let's say the **`Seat`** entity has a composite key made up of `seatNumber` and `flightInfoId`. This means each seat is uniquely identified by a combination of these two columns.

1. **Define the Composite Key Class:**

```java
// Composite key class for Seat entity
@Embeddable
public class SeatId implements Serializable {

    @Column(name = "seat_number")
    private String seatNumber;

    @Column(name = "flight_info_id")
    private Long flightInfoId;

    // Constructors, Getters, Setters, equals() and hashCode()
}
```

- **`@Embeddable`**: Marks this class as a composite key class.
- **`Serializable`**: Implements `Serializable` to ensure the class can be serialized.

2. **Use the Composite Key in the Entity:**

```java
@Entity
@Table(name = "Seat")
public class Seat {

    @EmbeddedId // Composite key
    private SeatId seatId;

    @ManyToOne // Many seats belong to one booking
    @JoinColumn(name = "booking_id") // Foreign key in Seat table referencing Booking table
    private Booking booking;

    @ManyToOne // Many seats for one flight info
    @JoinColumn(name = "flight_info_id", insertable = false, updatable = false) // Part of the composite key
    private FlightInfo flightInfo;

    // Other fields, getters, and setters
}
```

- **`@EmbeddedId`**: Specifies that `SeatId` is the composite key for this entity.
- The **`@JoinColumn`** annotations for `flightInfo` include `insertable = false, updatable = false` because `flight_info_id` is already part of the composite key, preventing JPA from trying to manage it twice.

#### **3. Mapping Between Other Entities Using Foreign Keys:**

##### **Example: `FlightInfo` and `City` (Foreign Key Mapping)**

- The **`FlightInfo`** entity has foreign key references to the **`City`** entity for both the source and destination cities.

```java
@Entity
@Table(name = "FlightInfo")
public class FlightInfo {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long flightInfoId;

    @ManyToOne
    @JoinColumn(name = "source_city_id", referencedColumnName = "cityId")
    private City sourceCity;

    @ManyToOne
    @JoinColumn(name = "destination_city_id", referencedColumnName = "cityId")
    private City destinationCity;

    // Other fields, getters, and setters
}
```

- **`@JoinColumn`**: Defines the foreign keys `source_city_id` and `destination_city_id` in the `FlightInfo` table, both referencing `cityId` in the `City` table.

#### **4. Circular Mapping and Avoiding Recursion:**

To handle circular mappings, especially in bi-directional relationships:

- **Use `mappedBy`**: To indicate which entity is the owner of the relationship.
- **Control Fetch Type**: Use `FetchType.LAZY` to avoid fetching related entities unnecessarily.

##### **Example: Circular Mapping Between `Booking` and `Seat`**

- In the `Booking` entity:

```java
@OneToMany(mappedBy = "booking", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
private Set<Seat> seats;
```

- In the `Seat` entity:

```java
@ManyToOne
@JoinColumn(name = "booking_id")
private Booking booking;
```

- `mappedBy = "booking"`: Indicates that the **`Booking`** entity is not the owner of the relationship (it is the inverse side).

### **Conclusion:**

- **Foreign Keys**: Defined using `@ManyToOne` and `@JoinColumn` to establish relationships between tables.
- **Composite Keys**: Created using a separate class annotated with `@Embeddable` and referenced in the entity using `@EmbeddedId`.
- **Circular Mappings**: Managed using `mappedBy` to specify the relationship owner and control data fetching strategies to avoid recursion or infinite loops. 

By setting up these mappings properly, you ensure data integrity, simplify data retrieval, and maintain relationships between your tables effectively in your airline management system.




The `mappedBy` attribute in JPA (Java Persistence API) is used to define the inverse side of a bi-directional relationship between two entities. It helps specify which entity is the owner of the relationship and which entity is the inverse (non-owning) side.

### **Purpose of Using `mappedBy`**

1. **Defining Ownership of the Relationship**:
   In a bi-directional relationship, there are always two sides:
   - **Owning side**: This side controls the association and contains the foreign key column.
   - **Inverse side (non-owning side)**: This side maps to the owning side and does not contain the foreign key column.
   
   The `mappedBy` attribute is used on the inverse side to specify the field in the owning entity that maps the relationship. This avoids redundancy and prevents both entities from trying to control the relationship, which could lead to inconsistent database states.

2. **Avoiding Redundant Foreign Key Columns**:
   By specifying `mappedBy`, we prevent the JPA provider from creating an additional foreign key column in the inverse entity's table. Instead, the inverse side will rely on the foreign key defined in the owning side.

3. **Bidirectional Navigation**:
   The `mappedBy` attribute allows bidirectional navigation between entities. For example, if you have entities `Booking` and `Seat`, you can navigate from a `Booking` to its `Seats` and vice versa without duplicating the foreign key.

### **Example of `mappedBy` in Bi-directional Relationships**

Let's consider the example from the airline management system where we have a `Booking` entity and a `Seat` entity.

- **Relationship**: 
  - One `Booking` can have multiple `Seat` entries (one-to-many relationship).
  - Each `Seat` is associated with a single `Booking` (many-to-one relationship).

#### **Booking Entity (Owning Side)**

```java
@Entity
@Table(name = "Booking")
public class Booking {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long bookingId;

    @OneToMany(mappedBy = "booking", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private Set<Seat> seats;

    // Other fields, getters, and setters
}
```

#### **Seat Entity (Inverse Side)**

```java
@Entity
@Table(name = "Seat")
public class Seat {

    @EmbeddedId // Composite key
    private SeatId seatId;

    @ManyToOne // Many seats belong to one booking
    @JoinColumn(name = "booking_id") // Foreign key in Seat table referencing Booking table
    private Booking booking;

    // Other fields, getters, and setters
}
```

### **Explanation of `mappedBy` Usage:**

- **Owning Side (Seat):**
  - The `Seat` entity is the owning side of the relationship because it contains the foreign key column `booking_id` in its table.
  - The foreign key is defined using `@JoinColumn(name = "booking_id")`, linking the `Seat` table to the `Booking` table.

- **Inverse Side (Booking):**
  - The `Booking` entity is the inverse side of the relationship.
  - It uses the `@OneToMany` annotation with `mappedBy = "booking"`, which indicates that the relationship is managed by the `booking` field in the `Seat` entity.
  - `mappedBy` tells JPA that the `Booking` entity should not create a foreign key column of its own; instead, it will rely on the foreign key column managed by the `Seat` entity.

### **Why Use `mappedBy`:**

1. **Prevents Redundant Foreign Keys:**
   Without `mappedBy`, JPA might try to create an extra foreign key column in the `Booking` table, leading to redundant or conflicting data.

2. **Improves Data Consistency:**
   It ensures that only one side (the owning side) manages the foreign key, reducing the chance of inconsistent data and avoiding potential issues with cascades and deletions.

3. **Reduces Complexity and Simplifies Queries:**
   By defining which entity owns the relationship, JPA can optimize database operations, making the codebase cleaner and easier to maintain.

4. **Supports Bidirectional Navigation:**
   You can navigate from a `Booking` to its associated `Seat` objects and vice versa without duplicating relationship data or creating additional join conditions.

### **Summary:**

`mappedBy` is essential in JPA for defining the inverse side of a bi-directional relationship, avoiding redundant foreign key columns, and ensuring data consistency. It helps establish clear ownership of relationships, reducing complexity and potential errors in the database schema.




Sure, let's go through each line of the entity classes step-by-step to clarify why certain decisions were made, such as declaring variables of entity types and using `Set` instead of other collections.

### **Understanding JPA Entity Classes:**

An entity class in JPA represents a table in the database, and each instance of the class corresponds to a row in that table. The fields in the entity class represent the columns in the table.

### **Example Entity Classes: `Booking` and `Seat`**

Let's start with the `Booking` and `Seat` entity classes to explain their structure and purpose.

#### **`Booking` Entity Class Explanation:**

```java
@Entity
@Table(name = "Booking")
public class Booking {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long bookingId;
    
    @OneToMany(mappedBy = "booking", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private Set<Seat> seats;

    // Other fields, constructors, getters, and setters
}
```

##### **Explanation:**

1. **`@Entity` Annotation:**
   - Marks the class as a JPA entity, meaning it is mapped to a database table. 
   - JPA will treat this class as an entity to perform CRUD (Create, Read, Update, Delete) operations.

2. **`@Table(name = "Booking")` Annotation:**
   - Specifies the name of the table (`Booking`) to which this entity is mapped. If not provided, JPA will assume the table name matches the class name (`Booking` in this case).

3. **`private Long bookingId;` with `@Id` and `@GeneratedValue`:**
   - **`@Id`**: Marks `bookingId` as the primary key of the table.
   - **`@GeneratedValue(strategy = GenerationType.IDENTITY)`**: Specifies the strategy for generating primary key values. `GenerationType.IDENTITY` means the database will automatically generate and assign the primary key value (commonly using auto-increment).

4. **`private Set<Seat> seats;` with `@OneToMany` Annotation:**
   - Declares a collection (in this case, a `Set`) of `Seat` entities.
   - **Why Declare Variables of Entity Type (`Set<Seat>`)**:
     - **`Seat` is an entity**: Declaring a variable of type `Set<Seat>` establishes a relationship between the `Booking` entity and the `Seat` entity.
     - This allows JPA to manage and understand the relationship between `Booking` and `Seat`, meaning a `Booking` can have multiple `Seat` entries. 
   - **Why Use `Set` Instead of `List`**:
     - **`Set` ensures uniqueness**: In many cases, you want to ensure that the collection of related entities is unique. A `Set` is chosen over a `List` when you want to prevent duplicate entries.
     - **Performance reasons**: Operations like `add` in a `Set` can be faster when uniqueness is required because they typically rely on hash-based data structures.

5. **`mappedBy = "booking"` in `@OneToMany`:**
   - Specifies that this is the inverse side of the relationship, and the `booking` field in the `Seat` entity is the owner of the relationship.
   - JPA will not create a foreign key column in the `Booking` table. Instead, it relies on the foreign key column defined in the `Seat` entity.

6. **`cascade = CascadeType.ALL`:**
   - Defines how operations (like persist, merge, remove, etc.) are cascaded from the `Booking` entity to the `Seat` entities.
   - `CascadeType.ALL` means that all operations on a `Booking` entity (e.g., saving or deleting) will be cascaded to the associated `Seat` entities.

7. **`fetch = FetchType.LAZY`:**
   - Specifies the loading strategy for associated entities. `FetchType.LAZY` means the associated `Seat` entities will be loaded on-demand (when accessed for the first time).
   - This is useful for performance optimization, as it avoids unnecessary loading of data.

#### **`Seat` Entity Class Explanation:**

```java
@Entity
@Table(name = "Seat")
public class Seat {

    @EmbeddedId // Composite key
    private SeatId seatId;

    @ManyToOne // Many seats belong to one booking
    @JoinColumn(name = "booking_id") // Foreign key in Seat table referencing Booking table
    private Booking booking;

    // Other fields, constructors, getters, and setters
}
```

##### **Explanation:**

1. **`@Entity` Annotation:**
   - Marks the class as a JPA entity, indicating that it is mapped to a database table.

2. **`@Table(name = "Seat")` Annotation:**
   - Specifies the name of the table (`Seat`) to which this entity is mapped.

3. **`private SeatId seatId;` with `@EmbeddedId`:**
   - **`@EmbeddedId`**: Specifies that `seatId` is a composite primary key.
   - **`SeatId` Class**: An `@Embeddable` class that contains multiple columns to form a composite key. The `SeatId` class typically contains fields like `seatNumber` and `flightInfoId`.
   - Using `@EmbeddedId` allows the `Seat` entity to have a composite primary key (a key formed by combining multiple columns).

4. **`private Booking booking;` with `@ManyToOne` Annotation:**
   - **`Booking booking`**: Declares a reference to another entity (`Booking`). This is a relationship field.
   - **`@ManyToOne`**: Indicates a many-to-one relationship, meaning multiple `Seat` entries can be associated with a single `Booking`.
   - JPA will create a foreign key column (`booking_id`) in the `Seat` table referencing the primary key of the `Booking` table.

5. **`@JoinColumn(name = "booking_id")` Annotation:**
   - Specifies the foreign key column name (`booking_id`) in the `Seat` table that references the primary key column (`bookingId`) in the `Booking` table.
   - Establishes a link between the `Seat` and `Booking` tables, allowing JPA to handle the relationship correctly in the database.

### **Key Takeaways:**

1. **Why Declare Variables of Entity Types (`Booking` in `Seat` and `Set<Seat>` in `Booking`):**
   - **To establish relationships**: Declaring variables of entity types allows JPA to understand and manage the relationships between entities. This is necessary for bidirectional navigation and cascading operations.
   - **To define foreign keys**: Entity variables indicate where foreign key relationships exist, enabling JPA to create and manage these constraints in the database.

2. **Why Use `Set` Instead of `List`:**
   - **Ensures Uniqueness**: `Set` guarantees that no duplicate entities exist in the collection.
   - **Performance**: Certain operations are optimized for sets, especially when uniqueness is a concern.

3. **`mappedBy` in `@OneToMany`:**
   - Indicates which side of the relationship is the inverse side, avoiding redundant foreign keys and ensuring that only one side manages the relationship.

4. **Cascading and Fetching Strategies (`CascadeType` and `FetchType`):**
   - **`CascadeType`** determines what operations should cascade from parent to child entities.
   - **`FetchType`** controls when the data is loaded, allowing for performance optimization.

### **Conclusion:**

In JPA, entity relationships are managed through annotations and types that define how entities are related in the database. Declaring entity variables and collections helps JPA map the object model to the relational model effectively, maintaining data consistency and integrity while allowing efficient data operations.








------/;/(-;-:-;-(;-;-;-;())))




To implement JWT authentication with a security filter chain in your Spring Boot application, where the `usercreds` table contains email, hashed password, and role ID (and the role ID is used to retrieve the role name), you can follow these steps:

### 1. **Setup Dependencies**
Ensure that you have the necessary dependencies in your `pom.xml`:

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>
```

### 2. **JWT Utility Class**
This class handles JWT creation and validation.

```java
import io.jsonwebtoken.*;
import org.springframework.stereotype.Component;
import java.util.Date;
import java.util.Map;

@Component
public class JwtUtil {

    private final String SECRET_KEY = "your_secret_key";

    public String generateToken(String email, String roleName, Map<String, Object> claims) {
        return Jwts.builder()
                .setClaims(claims)
                .setSubject(email)
                .claim("role", roleName) // Add role to JWT payload
                .setIssuedAt(new Date())
                .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60 * 10)) // Token validity 10 hours
                .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
                .compact();
    }

    public Claims extractClaims(String token) {
        return Jwts.parser()
                .setSigningKey(SECRET_KEY)
                .parseClaimsJws(token)
                .getBody();
    }

    public String extractUsername(String token) {
        return extractClaims(token).getSubject();
    }

    public String extractRole(String token) {
        return extractClaims(token).get("role", String.class);
    }

    public Boolean validateToken(String token, String userDetails) {
        final String username = extractUsername(token);
        return (username.equals(userDetails) && !isTokenExpired(token));
    }

    private Boolean isTokenExpired(String token) {
        return extractClaims(token).getExpiration().before(new Date());
    }
}
```

### 3. **Custom UserDetailsService Implementation**
Implement the `UserDetailsService` to load user details from your `usercreds` and `roles` tables.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.stereotype.Service;

@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserCredsRepository userCredsRepository;

    @Autowired
    private RoleRepository roleRepository;

    @Override
    public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
        UserCreds user = userCredsRepository.findByEmail(email)
                .orElseThrow(() -> new UsernameNotFoundException("User not found"));

        String roleName = roleRepository.findById(user.getRoleId())
                .orElseThrow(() -> new UsernameNotFoundException("Role not found"))
                .getName();

        return new CustomUserDetails(user.getEmail(), user.getPassword(), roleName);
    }
}
```

### 4. **CustomUserDetails Class**
This class wraps the user's credentials and authorities (roles).

```java
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.Collection;
import java.util.Collections;

public class CustomUserDetails implements UserDetails {

    private String email;
    private String password;
    private String roleName;

    public CustomUserDetails(String email, String password, String roleName) {
        this.email = email;
        this.password = password;
        this.roleName = roleName;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return Collections.singletonList(new SimpleGrantedAuthority(roleName));
    }

    @Override
    public String getPassword() {
        return password;
    }

    @Override
    public String getUsername() {
        return email;
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
        return true;
    }
}
```

### 5. **JWT Filter**
This filter will validate the JWT token and set the authenticated user in the SecurityContext.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.web.authentication.WebAuthenticationDetailsSource;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class JwtRequestFilter extends OncePerRequestFilter {

    @Autowired
    private JwtUtil jwtUtil;

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
            throws ServletException, IOException {

        final String authorizationHeader = request.getHeader("Authorization");

        String username = null;
        String jwt = null;

        if (authorizationHeader != null && authorizationHeader.startsWith("Bearer ")) {
            jwt = authorizationHeader.substring(7);
            username = jwtUtil.extractUsername(jwt);
        }

        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {

            UserDetails userDetails = this.userDetailsService.loadUserByUsername(username);

            if (jwtUtil.validateToken(jwt, userDetails.getUsername())) {
                UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());
                authenticationToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authenticationToken);
            }
        }
        chain.doFilter(request, response);
    }
}
```

### 6. **Security Configuration**
Configure the security filter chain to add the JWT filter.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Autowired
    private JwtRequestFilter jwtRequestFilter;

    @Autowired
    private CustomUserDetailsService customUserDetailsService;

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public AuthenticationManager authenticationManager(HttpSecurity http, PasswordEncoder passwordEncoder) throws Exception {
        return http.getSharedObject(AuthenticationManagerBuilder.class)
                .userDetailsService(customUserDetailsService)
                .passwordEncoder(passwordEncoder)
                .and()
                .build();
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .authorizeHttpRequests(auth -> auth
                        .antMatchers("/authenticate").permitAll()
                        .anyRequest().




Apologies for that! Let's continue from where we left off, starting with the security filter chain configuration.

### 6. **Security Configuration (continued)**

In the `SecurityConfig` class, we will configure the security filter chain to add the JWT filter and handle the authentication mechanism.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Autowired
    private JwtRequestFilter jwtRequestFilter;

    @Autowired
    private CustomUserDetailsService customUserDetailsService;

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public AuthenticationManager authenticationManager(HttpSecurity http, PasswordEncoder passwordEncoder) throws Exception {
        return http.getSharedObject(AuthenticationManagerBuilder.class)
                .userDetailsService(customUserDetailsService)
                .passwordEncoder(passwordEncoder)
                .and()
                .build();
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .authorizeHttpRequests(auth -> auth
                        .antMatchers("/authenticate").permitAll() // Allow access to the authentication endpoint
                        .anyRequest().authenticated() // All other requests require authentication
                )
                .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS) // Use stateless sessions (no sessions)
                .and()
                .addFilterBefore(jwtRequestFilter, UsernamePasswordAuthenticationFilter.class); // Add JWT filter

        return http.build();
    }
}
```

### 7. **Authentication Controller**
This controller will handle user authentication, verify credentials, and issue a JWT upon successful login.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.web.bind.annotation.*;

import java.util.HashMap;
import java.util.Map;

@RestController
public class AuthController {

    @Autowired
    private AuthenticationManager authenticationManager;

    @Autowired
    private CustomUserDetailsService userDetailsService;

    @Autowired
    private JwtUtil jwtUtil;

    @PostMapping("/authenticate")
    public Map<String, String> createAuthenticationToken(@RequestBody AuthRequest authRequest) throws Exception {
        try {
            authenticationManager.authenticate(
                    new UsernamePasswordAuthenticationToken(authRequest.getEmail(), authRequest.getPassword()));
        } catch (Exception e) {
            throw new Exception("Incorrect email or password", e);
        }

        final UserDetails userDetails = userDetailsService.loadUserByUsername(authRequest.getEmail());
        final String roleName = ((CustomUserDetails) userDetails).getAuthorities().iterator().next().getAuthority();
        final String jwt = jwtUtil.generateToken(userDetails.getUsername(), roleName, new HashMap<>());

        Map<String, String> response = new HashMap<>();
        response.put("token", jwt);
        return response;
    }
}
```

### 8. **Authentication Request DTO**
This is a simple DTO (Data Transfer Object) for passing the login credentials (email and password) from the client.

```java
public class AuthRequest {
    private String email;
    private String password;

    // Getters and Setters
}
```

### 9. **Testing the Implementation**

#### 1. **Authentication Flow**:
   - The user sends a POST request to `/authenticate` with the email and password.
   - The server validates the credentials and issues a JWT token in the response.
   
#### 2. **Token Usage**:
   - The client stores the JWT token (e.g., in local storage or cookies).
   - For each subsequent request to a secured endpoint, the token is passed in the `Authorization` header as `Bearer <JWT_TOKEN>`.
   
#### 3. **JWT Validation**:
   - The `JwtRequestFilter` checks if the token is valid and extracts the user details and role from it.
   - If valid, it authenticates the user and sets their authorities (role) in the security context.

---

Now you have a working JWT authentication with role-based authorization in your Spring Boot app! You can secure endpoints based on user roles by using the `@PreAuthorize` annotation (e.g., `@PreAuthorize("hasRole('ROLE_ADMIN')")`) on your controller methods for role-specific access control.

Let me know if you need further adjustments or explanations!