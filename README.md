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



