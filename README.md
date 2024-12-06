Below is the mapping structure for all the mentioned entities with their fields, considering this is a Spring Boot project using JPA annotations. Relationships like @OneToMany, @ManyToOne, etc., are included as per standard practices. Feel free to adjust based on your exact requirements.

1. BookingEntity

@Entity
@Table(name = "booking")
public class BookingEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "user_id", nullable = false)
    private UserEntity user;

    @ManyToOne
    @JoinColumn(name = "flight_id", nullable = false)
    private FlightEntity flight;

    @OneToMany(mappedBy = "booking", cascade = CascadeType.ALL)
    private List<SeatEntity> seats;

    @Column(name = "booking_date", nullable = false)
    private LocalDate bookingDate;

    @ManyToOne
    @JoinColumn(name = "status_id", nullable = false)
    private StatusEntity status;

    // Getters and Setters
}

2. CityEntity

@Entity
@Table(name = "city")
public class CityEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name", nullable = false, unique = true)
    private String name;

    @Column(name = "state", nullable = false)
    private String state;

    @Column(name = "country", nullable = false)
    private String country;

    @OneToMany(mappedBy = "sourceCity", cascade = CascadeType.ALL)
    private List<FlightEntity> outgoingFlights;

    @OneToMany(mappedBy = "destinationCity", cascade = CascadeType.ALL)
    private List<FlightEntity> incomingFlights;

    // Getters and Setters
}

3. CredentialsEntity

@Entity
@Table(name = "credentials")
public class CredentialsEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username", nullable = false, unique = true)
    private String username;

    @Column(name = "password", nullable = false)
    private String password;

    @ManyToOne
    @JoinColumn(name = "role_id", nullable = false)
    private RoleEntity role;

    @OneToOne(mappedBy = "credentials")
    private UserEntity user;

    // Getters and Setters
}

4. FlightEntity

@Entity
@Table(name = "flight")
public class FlightEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "flight_number", nullable = false, unique = true)
    private String flightNumber;

    @ManyToOne
    @JoinColumn(name = "source_city_id", nullable = false)
    private CityEntity sourceCity;

    @ManyToOne
    @JoinColumn(name = "destination_city_id", nullable = false)
    private CityEntity destinationCity;

    @OneToMany(mappedBy = "flight", cascade = CascadeType.ALL)
    private List<SeatEntity> seats;

    @OneToMany(mappedBy = "flight", cascade = CascadeType.ALL)
    private List<BookingEntity> bookings;

    @OneToMany(mappedBy = "flight", cascade = CascadeType.ALL)
    private List<FlightInfoEntity> flightInfos;

    // Getters and Setters
}

5. FlightInfoEntity

@Entity
@Table(name = "flight_info")
public class FlightInfoEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "flight_id", nullable = false)
    private FlightEntity flight;

    @Column(name = "departure_time", nullable = false)
    private LocalDateTime departureTime;

    @Column(name = "arrival_time", nullable = false)
    private LocalDateTime arrivalTime;

    @Column(name = "duration", nullable = false)
    private String duration;

    @Column(name = "price", nullable = false)
    private BigDecimal price;

    // Getters and Setters
}

6. RoleEntity

@Entity
@Table(name = "role")
public class RoleEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name", nullable = false, unique = true)
    private String name;

    @OneToMany(mappedBy = "role", cascade = CascadeType.ALL)
    private List<CredentialsEntity> credentials;

    // Getters and Setters
}

7. SeatEntity

@Entity
@Table(name = "seat")
public class SeatEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "seat_number", nullable = false)
    private String seatNumber;

    @ManyToOne
    @JoinColumn(name = "flight_id", nullable = false)
    private FlightEntity flight;

    @ManyToOne
    @JoinColumn(name = "booking_id")
    private BookingEntity booking;

    @ManyToOne
    @JoinColumn(name = "status_id", nullable = false)
    private StatusEntity status;

    // Getters and Setters
}

8. StatusEntity

@Entity
@Table(name = "status")
public class StatusEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "status_name", nullable = false, unique = true)
    private String statusName;

    @OneToMany(mappedBy = "status", cascade = CascadeType.ALL)
    private List<BookingEntity> bookings;

    @OneToMany(mappedBy = "status", cascade = CascadeType.ALL)
    private List<SeatEntity> seats;

    // Getters and Setters
}

9. UserEntity

@Entity
@Table(name = "user")
public class UserEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "first_name", nullable = false)
    private String firstName;

    @Column(name = "last_name", nullable = false)
    private String lastName;

    @Column(name = "email", nullable = false, unique = true)
    private String email;

    @Column(name = "phone_number", nullable = false, unique = true)
    private String phoneNumber;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<BookingEntity> bookings;

    @OneToOne
    @JoinColumn(name = "credentials_id", nullable = false)
    private CredentialsEntity credentials;

    // Getters and Setters
}

These mappings provide a comprehensive structure for your entities. Let me know if you need additional changes or more details about specific relationships.









&&&&&&&&&&&&&&&&&&-&-&&-&&&&-&-&&-&-&-&-&-&-&-&-&&


DAO

Below is the implementation of the DAO layer for the entities you mentioned using Spring Data JPA. These repositories will allow for basic CRUD operations and querying through Spring JPA without needing to write boilerplate code.

1. BookingEntity Repository

@Repository
public interface BookingRepository extends JpaRepository<BookingEntity, Long> {
    List<BookingEntity> findByUserId(Long userId); // Custom query to find bookings by user ID
}

2. CityEntity Repository

@Repository
public interface CityRepository extends JpaRepository<CityEntity, Long> {
    Optional<CityEntity> findByCityName(String cityName); // Custom query to find city by name
}

3. CredentialsEntity Repository

@Repository
public interface CredentialsRepository extends JpaRepository<CredentialsEntity, Long> {
    Optional<CredentialsEntity> findByUsername(String username); // Custom query to find by username
}

4. FlightEntity Repository

@Repository
public interface FlightRepository extends JpaRepository<FlightEntity, Long> {
    List<FlightEntity> findBySourceAndDestination(String source, String destination); // Find flights by source and destination
    List<FlightEntity> findByDepartureDate(LocalDate departureDate); // Find flights by departure date
}

5. FlightInfoEntity Repository

@Repository
public interface FlightInfoRepository extends JpaRepository<FlightInfoEntity, Long> {
    Optional<FlightInfoEntity> findByFlightNumber(String flightNumber); // Find flight info by flight number
}

6. RoleEntity Repository

@Repository
public interface RoleRepository extends JpaRepository<RoleEntity, Long> {
    Optional<RoleEntity> findByRoleName(String roleName); // Find role by name
}

7. SeatEntity Repository

@Repository
public interface SeatRepository extends JpaRepository<SeatEntity, Long> {
    List<SeatEntity> findByFlightId(Long flightId); // Find seats by flight ID
}

8. StatusEntity Repository

@Repository
public interface StatusRepository extends JpaRepository<StatusEntity, Long> {
    Optional<StatusEntity> findByStatusName(String statusName); // Find status by name
}

9. UserEntity Repository

@Repository
public interface UserRepository extends JpaRepository<UserEntity, Long> {
    Optional<UserEntity> findByEmail(String email); // Find user by email
    Optional<UserEntity> findByPhoneNumber(String phoneNumber); // Find user by phone number
}

Key Points:

	1.	Base Interface: All repositories extend JpaRepository<T, ID>, where:
	•	T is the entity type.
	•	ID is the type of the entity’s primary key.
	2.	Custom Methods: Use Spring Data JPA’s method query generation for custom queries like findByEmail or findBySourceAndDestination.
	3.	Annotations:
	•	@Repository: Indicates that the interface is a Spring Data repository.

Why These DAOs Are Useful:

	•	These DAOs provide built-in CRUD methods like save, findById, findAll, deleteById, etc.
	•	You can add custom methods (e.g., findBySourceAndDestination) based on naming conventions.
	•	These DAOs will be used in the Service Layer to interact with the database seamlessly.

Let me know if you’d like to proceed with the Service Layer implementation or have further questions!





&&&&&-&-&-&-&-&-&-&-&&-&-&-&-&-&-&&-&-&-&-&-&



To create a complete POJO Layer and Service Layer for all the entities, here is the implementation:

1. POJO Layer

Each POJO will represent a simplified, plain Java object (DTO-like structure) that is used to transfer data between the Service Layer and the Controller.

Example: Booking POJO

public class Booking {
    private Long id;
    private Long userId;
    private Long flightId;
    private String status;
    private LocalDate bookingDate;

    // Getters and Setters
}

POJO Classes for All Entities

Below are the POJOs for all the entities.

City POJO

public class City {
    private Long id;
    private String cityName;

    // Getters and Setters
}

Credentials POJO

public class Credentials {
    private Long id;
    private String username;
    private String password;

    // Getters and Setters
}

Flight POJO

public class Flight {
    private Long id;
    private String source;
    private String destination;
    private LocalDate departureDate;
    private LocalTime departureTime;

    // Getters and Setters
}

FlightInfo POJO

public class FlightInfo {
    private Long id;
    private String flightNumber;
    private String airlineName;

    // Getters and Setters
}

Role POJO

public class Role {
    private Long id;
    private String roleName;

    // Getters and Setters
}

Seat POJO

public class Seat {
    private Long id;
    private Long flightId;
    private String seatNumber;
    private boolean isBooked;

    // Getters and Setters
}

Status POJO

public class Status {
    private Long id;
    private String statusName;

    // Getters and Setters
}

User POJO

public class User {
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
    private String phoneNumber;

    // Getters and Setters
}

2. Service Layer

The Service Layer contains business logic and ensures data transformation between Entities and POJOs.

Example: BookingService

@Service
public class BookingService {
    private final BookingRepository bookingRepository;

    @Autowired
    public BookingService(BookingRepository bookingRepository) {
        this.bookingRepository = bookingRepository;
    }

    // Fetch All Bookings
    public List<Booking> fetchAllBookings() {
        List<BookingEntity> bookingEntities = bookingRepository.findAll();
        return bookingEntities.stream().map(this::mapToPojo).collect(Collectors.toList());
    }

    // Fetch a Booking by ID
    public Booking fetchBookingById(Long id) {
        BookingEntity bookingEntity = bookingRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Booking not found with ID: " + id));
        return mapToPojo(bookingEntity);
    }

    // Add a Booking
    public Booking addBooking(Booking booking) {
        BookingEntity bookingEntity = mapToEntity(booking);
        BookingEntity savedEntity = bookingRepository.save(bookingEntity);
        return mapToPojo(savedEntity);
    }

    // Update a Booking
    public Booking updateBooking(Long id, Booking booking) {
        BookingEntity bookingEntity = bookingRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Booking not found with ID: " + id));

        bookingEntity.setStatus(booking.getStatus());
        bookingEntity.setBookingDate(booking.getBookingDate());
        BookingEntity updatedEntity = bookingRepository.save(bookingEntity);

        return mapToPojo(updatedEntity);
    }

    // Delete a Booking
    public void deleteBooking(Long id) {
        bookingRepository.deleteById(id);
    }

    // Mapping Methods
    private Booking mapToPojo(BookingEntity entity) {
        Booking pojo = new Booking();
        pojo.setId(entity.getId());
        pojo.setUserId(entity.getUser().getId());
        pojo.setFlightId(entity.getFlight().getId());
        pojo.setStatus(entity.getStatus().getStatusName());
        pojo.setBookingDate(entity.getBookingDate());
        return pojo;
    }

    private BookingEntity mapToEntity(Booking pojo) {
        BookingEntity entity = new BookingEntity();
        entity.setId(pojo.getId());
        // Assume UserEntity and FlightEntity are fetched separately in Service Layer logic
        entity.setBookingDate(pojo.getBookingDate());
        return entity;
    }
}

Service Classes for Other Entities

Below is an outline for the other services. These follow the same logic as the BookingService.

CityService
	•	Fetch all cities.
	•	Fetch a city by ID or name.
	•	Add, update, and delete city records.
	•	Convert between City and CityEntity.

CredentialsService
	•	Manage user credentials, fetch by username, and update passwords.

FlightService
	•	Fetch all flights.
	•	Fetch flights by source, destination, or date.
	•	Add, update, and delete flights.
	•	Convert between Flight and FlightEntity.

UserService
	•	Fetch all users.
	•	Fetch user by ID, email, or phone number.
	•	Add, update, and delete users.
	•	Convert between User and UserEntity.

3. Key Concepts
	•	Mapping: Each service uses helper methods (mapToPojo and mapToEntity) to transform between POJOs and Entities.
	•	CRUD Operations: All service classes include basic CRUD methods: fetchAll, fetchById, add, update, and delete.
	•	Error Handling: Throw exceptions like RuntimeException or use custom exceptions for missing data.

Let me know if you want fully implemented service classes for all entities or detailed controllers using these services!



?????#?%~#,#,%?#?#,#,#,^#~£?$,%~$?$>.$,>,$?$




Here are the Service Layer classes for all entities with the required CRUD methods:

Each service class will implement the logic to handle operations and transform data between entities and POJOs.

1. BookingService

@Service
public class BookingService {
    private final BookingRepository bookingRepository;

    @Autowired
    public BookingService(BookingRepository bookingRepository) {
        this.bookingRepository = bookingRepository;
    }

    // Fetch All Bookings
    public List<Booking> fetchAllBookings() {
        return bookingRepository.findAll()
                .stream()
                .map(this::mapToPojo)
                .collect(Collectors.toList());
    }

    // Fetch Booking by ID
    public Booking fetchBookingById(Long id) {
        return bookingRepository.findById(id)
                .map(this::mapToPojo)
                .orElseThrow(() -> new RuntimeException("Booking not found with ID: " + id));
    }

    // Add a Booking
    public Booking addBooking(Booking booking) {
        BookingEntity savedEntity = bookingRepository.save(mapToEntity(booking));
        return mapToPojo(savedEntity);
    }

    // Update a Booking
    public Booking updateBooking(Long id, Booking booking) {
        BookingEntity existingEntity = bookingRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Booking not found with ID: " + id));

        existingEntity.setStatus(booking.getStatus());
        existingEntity.setBookingDate(booking.getBookingDate());
        BookingEntity updatedEntity = bookingRepository.save(existingEntity);

        return mapToPojo(updatedEntity);
    }

    // Delete a Booking
    public void deleteBooking(Long id) {
        bookingRepository.deleteById(id);
    }

    // Mapping Methods
    private Booking mapToPojo(BookingEntity entity) {
        Booking booking = new Booking();
        booking.setId(entity.getId());
        booking.setUserId(entity.getUser().getId());
        booking.setFlightId(entity.getFlight().getId());
        booking.setStatus(entity.getStatus().getStatusName());
        booking.setBookingDate(entity.getBookingDate());
        return booking;
    }

    private BookingEntity mapToEntity(Booking booking) {
        BookingEntity entity = new BookingEntity();
        entity.setId(booking.getId());
        // Use service logic to fetch and set UserEntity, FlightEntity, and StatusEntity.
        entity.setBookingDate(booking.getBookingDate());
        return entity;
    }
}

2. CityService

@Service
public class CityService {
    private final CityRepository cityRepository;

    @Autowired
    public CityService(CityRepository cityRepository) {
        this.cityRepository = cityRepository;
    }

    // Fetch All Cities
    public List<City> fetchAllCities() {
        return cityRepository.findAll()
                .stream()
                .map(this::mapToPojo)
                .collect(Collectors.toList());
    }

    // Fetch City by ID
    public City fetchCityById(Long id) {
        return cityRepository.findById(id)
                .map(this::mapToPojo)
                .orElseThrow(() -> new RuntimeException("City not found with ID: " + id));
    }

    // Add a City
    public City addCity(City city) {
        CityEntity savedEntity = cityRepository.save(mapToEntity(city));
        return mapToPojo(savedEntity);
    }

    // Update a City
    public City updateCity(Long id, City city) {
        CityEntity existingEntity = cityRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("City not found with ID: " + id));

        existingEntity.setCityName(city.getCityName());
        CityEntity updatedEntity = cityRepository.save(existingEntity);

        return mapToPojo(updatedEntity);
    }

    // Delete a City
    public void deleteCity(Long id) {
        cityRepository.deleteById(id);
    }

    // Mapping Methods
    private City mapToPojo(CityEntity entity) {
        City city = new City();
        city.setId(entity.getId());
        city.setCityName(entity.getCityName());
        return city;
    }

    private CityEntity mapToEntity(City city) {
        CityEntity entity = new CityEntity();
        entity.setId(city.getId());
        entity.setCityName(city.getCityName());
        return entity;
    }
}

3. CredentialsService

@Service
public class CredentialsService {
    private final CredentialsRepository credentialsRepository;

    @Autowired
    public CredentialsService(CredentialsRepository credentialsRepository) {
        this.credentialsRepository = credentialsRepository;
    }

    // Fetch All Credentials
    public List<Credentials> fetchAllCredentials() {
        return credentialsRepository.findAll()
                .stream()
                .map(this::mapToPojo)
                .collect(Collectors.toList());
    }

    // Fetch Credentials by ID
    public Credentials fetchCredentialsById(Long id) {
        return credentialsRepository.findById(id)
                .map(this::mapToPojo)
                .orElseThrow(() -> new RuntimeException("Credentials not found with ID: " + id));
    }

    // Add Credentials
    public Credentials addCredentials(Credentials credentials) {
        CredentialsEntity savedEntity = credentialsRepository.save(mapToEntity(credentials));
        return mapToPojo(savedEntity);
    }

    // Update Credentials
    public Credentials updateCredentials(Long id, Credentials credentials) {
        CredentialsEntity existingEntity = credentialsRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Credentials not found with ID: " + id));

        existingEntity.setUsername(credentials.getUsername());
        existingEntity.setPassword(credentials.getPassword());
        CredentialsEntity updatedEntity = credentialsRepository.save(existingEntity);

        return mapToPojo(updatedEntity);
    }

    // Delete Credentials
    public void deleteCredentials(Long id) {
        credentialsRepository.deleteById(id);
    }

    // Mapping Methods
    private Credentials mapToPojo(CredentialsEntity entity) {
        Credentials credentials = new Credentials();
        credentials.setId(entity.getId());
        credentials.setUsername(entity.getUsername());
        credentials.setPassword(entity.getPassword());
        return credentials;
    }

    private CredentialsEntity mapToEntity(Credentials credentials) {
        CredentialsEntity entity = new CredentialsEntity();
        entity.setId(credentials.getId());
        entity.setUsername(credentials.getUsername());
        entity.setPassword(credentials.getPassword());
        return entity;
    }
}

Repeat similar patterns for all other entities, such as FlightService, RoleService, SeatService, StatusService, and UserService.

Let me know if you’d like me to generate the remaining classes or clarify anything specific!















m














Here are the remaining Service Layer classes for all entities:

4. FlightService

@Service
public class FlightService {
    private final FlightRepository flightRepository;

    @Autowired
    public FlightService(FlightRepository flightRepository) {
        this.flightRepository = flightRepository;
    }

    // Fetch All Flights
    public List<Flight> fetchAllFlights() {
        return flightRepository.findAll()
                .stream()
                .map(this::mapToPojo)
                .collect(Collectors.toList());
    }

    // Fetch Flight by ID
    public Flight fetchFlightById(Long id) {
        return flightRepository.findById(id)
                .map(this::mapToPojo)
                .orElseThrow(() -> new RuntimeException("Flight not found with ID: " + id));
    }

    // Add a Flight
    public Flight addFlight(Flight flight) {
        FlightEntity savedEntity = flightRepository.save(mapToEntity(flight));
        return mapToPojo(savedEntity);
    }

    // Update a Flight
    public Flight updateFlight(Long id, Flight flight) {
        FlightEntity existingEntity = flightRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Flight not found with ID: " + id));

        existingEntity.setFlightNumber(flight.getFlightNumber());
        existingEntity.setSourceCity(flight.getSourceCity());
        existingEntity.setDestinationCity(flight.getDestinationCity());
        existingEntity.setDepartureTime(flight.getDepartureTime());
        existingEntity.setArrivalTime(flight.getArrivalTime());

        FlightEntity updatedEntity = flightRepository.save(existingEntity);
        return mapToPojo(updatedEntity);
    }

    // Delete a Flight
    public void deleteFlight(Long id) {
        flightRepository.deleteById(id);
    }

    // Mapping Methods
    private Flight mapToPojo(FlightEntity entity) {
        Flight flight = new Flight();
        flight.setId(entity.getId());
        flight.setFlightNumber(entity.getFlightNumber());
        flight.setSourceCity(entity.getSourceCity().getCityName());
        flight.setDestinationCity(entity.getDestinationCity().getCityName());
        flight.setDepartureTime(entity.getDepartureTime());
        flight.setArrivalTime(entity.getArrivalTime());
        return flight;
    }

    private FlightEntity mapToEntity(Flight flight) {
        FlightEntity entity = new FlightEntity();
        entity.setId(flight.getId());
        // Use service logic to fetch CityEntity references for source and destination cities.
        entity.setDepartureTime(flight.getDepartureTime());
        entity.setArrivalTime(flight.getArrivalTime());
        return entity;
    }
}

5. FlightInfoService

@Service
public class FlightInfoService {
    private final FlightInfoRepository flightInfoRepository;

    @Autowired
    public FlightInfoService(FlightInfoRepository flightInfoRepository) {
        this.flightInfoRepository = flightInfoRepository;
    }

    // Fetch All Flight Info
    public List<FlightInfo> fetchAllFlightInfo() {
        return flightInfoRepository.findAll()
                .stream()
                .map(this::mapToPojo)
                .collect(Collectors.toList());
    }

    // Fetch Flight Info by ID
    public FlightInfo fetchFlightInfoById(Long id) {
        return flightInfoRepository.findById(id)
                .map(this::mapToPojo)
                .orElseThrow(() -> new RuntimeException("Flight Info not found with ID: " + id));
    }

    // Add Flight Info
    public FlightInfo addFlightInfo(FlightInfo flightInfo) {
        FlightInfoEntity savedEntity = flightInfoRepository.save(mapToEntity(flightInfo));
        return mapToPojo(savedEntity);
    }

    // Update Flight Info
    public FlightInfo updateFlightInfo(Long id, FlightInfo flightInfo) {
        FlightInfoEntity existingEntity = flightInfoRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Flight Info not found with ID: " + id));

        existingEntity.setFlightDetails(flightInfo.getFlightDetails());
        existingEntity.setPrice(flightInfo.getPrice());
        FlightInfoEntity updatedEntity = flightInfoRepository.save(existingEntity);

        return mapToPojo(updatedEntity);
    }

    // Delete Flight Info
    public void deleteFlightInfo(Long id) {
        flightInfoRepository.deleteById(id);
    }

    // Mapping Methods
    private FlightInfo mapToPojo(FlightInfoEntity entity) {
        FlightInfo flightInfo = new FlightInfo();
        flightInfo.setId(entity.getId());
        flightInfo.setFlightId(entity.getFlight().getId());
        flightInfo.setFlightDetails(entity.getFlightDetails());
        flightInfo.setPrice(entity.getPrice());
        return flightInfo;
    }

    private FlightInfoEntity mapToEntity(FlightInfo flightInfo) {
        FlightInfoEntity entity = new FlightInfoEntity();
        entity.setId(flightInfo.getId());
        // Use service logic to fetch FlightEntity reference.
        entity.setFlightDetails(flightInfo.getFlightDetails());
        entity.setPrice(flightInfo.getPrice());
        return entity;
    }
}

6. RoleService

@Service
public class RoleService {
    private final RoleRepository roleRepository;

    @Autowired
    public RoleService(RoleRepository roleRepository) {
        this.roleRepository = roleRepository;
    }

    // Fetch All Roles
    public List<Role> fetchAllRoles() {
        return roleRepository.findAll()
                .stream()
                .map(this::mapToPojo)
                .collect(Collectors.toList());
    }

    // Fetch Role by ID
    public Role fetchRoleById(Long id) {
        return roleRepository.findById(id)
                .map(this::mapToPojo)
                .orElseThrow(() -> new RuntimeException("Role not found with ID: " + id));
    }

    // Add a Role
    public Role addRole(Role role) {
        RoleEntity savedEntity = roleRepository.save(mapToEntity(role));
        return mapToPojo(savedEntity);
    }

    // Update a Role
    public Role updateRole(Long id, Role role) {
        RoleEntity existingEntity = roleRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Role not found with ID: " + id));

        existingEntity.setRoleName(role.getRoleName());
        RoleEntity updatedEntity = roleRepository.save(existingEntity);

        return mapToPojo(updatedEntity);
    }

    // Delete a Role
    public void deleteRole(Long id) {
        roleRepository.deleteById(id);
    }

    // Mapping Methods
    private Role mapToPojo(RoleEntity entity) {
        Role role = new Role();
        role.setId(entity.getId());
        role.setRoleName(entity.getRoleName());
        return role;
    }

    private RoleEntity mapToEntity(Role role) {
        RoleEntity entity = new RoleEntity();
        entity.setId(role.getId());
        entity.setRoleName(role.getRoleName());
        return entity;
    }
}

Repeat similar patterns for SeatService, StatusService, and UserService using the same logic and structure.

Let me know if you’d like the code for the remaining services!