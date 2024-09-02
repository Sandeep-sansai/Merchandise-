public class Restaurant {
    private int id;
    private String name;
    private String branch;
    private String priceRange;
    private String cuisine;
    private double rating;

    // Constructors, Getters, and Setters
    public Restaurant(int id, String name, String branch, String priceRange, String cuisine, double rating) {
        this.id = id;
        this.name = name;
        this.branch = branch;
        this.priceRange = priceRange;
        this.cuisine = cuisine;
        this.rating = rating;
    }

    // Getters and Setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getBranch() { return branch; }
    public void setBranch(String branch) { this.branch = branch; }
    public String getPriceRange() { return priceRange; }
    public void setPriceRange(String priceRange) { this.priceRange = priceRange; }
    public String getCuisine() { return cuisine; }
    public void setCuisine(String cuisine) { this.cuisine = cuisine; }
    public double getRating() { return rating; }
    public void setRating(double rating) { this.rating = rating; }
}




import java.util.Date;

public class Reservation {
    private int id;
    private int restaurantId;
    private String customerName;
    private Date reservationDate;
    private int numberOfGuests;

    // Constructors, Getters, and Setters
    public Reservation(int id, int restaurantId, String customerName, Date reservationDate, int numberOfGuests) {
        this.id = id;
        this.restaurantId = restaurantId;
        this.customerName = customerName;
        this.reservationDate = reservationDate;
        this.numberOfGuests = numberOfGuests;
    }

    // Getters and Setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public int getRestaurantId() { return restaurantId; }
    public void setRestaurantId(int restaurantId) { this.restaurantId = restaurantId; }
    public String getCustomerName() { return customerName; }
    public void setCustomerName(String customerName) { this.customerName = customerName; }
    public Date getReservationDate() { return reservationDate; }
    public void setReservationDate(Date reservationDate) { this.reservationDate = reservationDate; }
    public int getNumberOfGuests() { return numberOfGuests; }
    public void setNumberOfGuests(int numberOfGuests) { this.numberOfGuests = numberOfGuests; }
}




import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class RestaurantDAO {
    private Connection connection;

    public RestaurantDAO(Connection connection) {
        this.connection = connection;
    }

    public List<Restaurant> getAllRestaurants() throws SQLException {
        List<Restaurant> restaurants = new ArrayList<>();
        String query = "SELECT * FROM restaurants";
        try (Statement stmt = connection.createStatement(); ResultSet rs = stmt.executeQuery(query)) {
            while (rs.next()) {
                restaurants.add(new Restaurant(
                    rs.getInt("id"),
                    rs.getString("name"),
                    rs.getString("branch"),
                    rs.getString("priceRange"),
                    rs.getString("cuisine"),
                    rs.getDouble("rating")
                ));
            }
        }
        return restaurants;
    }

    public List<Restaurant> getRestaurantsByFilters(String branch, String priceRange, String cuisine) throws SQLException {
        List<Restaurant> restaurants = new ArrayList<>();
        String query = "SELECT * FROM restaurants WHERE branch = ? AND priceRange = ? AND cuisine = ?";
        try (PreparedStatement pstmt = connection.prepareStatement(query)) {
            pstmt.setString(1, branch);
            pstmt.setString(2, priceRange);
            pstmt.setString(3, cuisine);
            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    restaurants.add(new Restaurant(
                        rs.getInt("id"),
                        rs.getString("name"),
                        rs.getString("branch"),
                        rs.getString("priceRange"),
                        rs.getString("cuisine"),
                        rs.getDouble("rating")
                    ));
                }
            }
        }
        return restaurants;
    }

    public void addReservation(Reservation reservation) throws SQLException {
        String query = "INSERT INTO reservations (restaurantId, customerName, reservationDate, numberOfGuests) VALUES (?, ?, ?, ?)";
        try (PreparedStatement pstmt = connection.prepareStatement(query)) {
            pstmt.setInt(1, reservation.getRestaurantId());
            pstmt.setString(2, reservation.getCustomerName());
            pstmt.setDate(3, new java.sql.Date(reservation.getReservationDate().getTime()));
            pstmt.setInt(4, reservation.getNumberOfGuests());
            pstmt.executeUpdate();
        }
    }

    public List<Reservation> getReservationsByCustomer(String customerName) throws SQLException {
        List<Reservation> reservations = new ArrayList<>();
        String query = "SELECT * FROM reservations WHERE customerName = ?";
        try (PreparedStatement pstmt = connection.prepareStatement(query)) {
            pstmt.setString(1, customerName);
            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    reservations.add(new Reservation(
                        rs.getInt("id"),
                        rs.getInt("restaurantId"),
                        rs.getString("customerName"),
                        rs.getDate("reservationDate"),
                        rs.getInt("numberOfGuests")
                    ));
                }
            }
        }
        return reservations;
    }
}




import java.sql.SQLException;
import java.util.List;

public class RestaurantService {
    private RestaurantDAO restaurantDAO;

    public RestaurantService(RestaurantDAO restaurantDAO) {
        this.restaurantDAO = restaurantDAO;
    }

    public List<Restaurant> filterRestaurants(String branch, String priceRange, String cuisine) throws SQLException {
        return restaurantDAO.getRestaurantsByFilters(branch, priceRange, cuisine);
    }

    public void makeReservation(Reservation reservation) throws SQLException {
        restaurantDAO.addReservation(reservation);
    }

    public List<Reservation> viewReservations(String customerName) throws SQLException {
        return restaurantDAO.getReservationsByCustomer(customerName);
    }
}


import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.List;
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        try (Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/restaurant_db", "root", "password")) {
            RestaurantDAO restaurantDAO = new RestaurantDAO(connection);
            RestaurantService service = new RestaurantService(restaurantDAO);

            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.println("1. View Restaurants by Filters");
                System.out.println("2. Make a Reservation");
                System.out.println("3. View Reservations");
                System.out.println("4. Exit");
                int choice = scanner.nextInt();
                scanner.nextLine();  // consume newline

                switch (choice) {
                    case 1:
                        System.out.println("Enter Branch:");
                        String branch = scanner.nextLine();
                        System.out.println("Enter Price Range:");
                        String priceRange = scanner.nextLine();
                        System.out.println("Enter Cuisine:");
                        String cuisine = scanner.nextLine();
                        List<Restaurant> restaurants = service.filterRestaurants(branch, priceRange, cuisine);
                        restaurants.forEach(r -> System.out.println(r.getName() + " - " + r.getRating()));
                        break;
                    case 2:
                        System.out.println("Enter Restaurant ID:");
                        int restaurantId = scanner.nextInt();
                        scanner.nextLine();  // consume newline
                        System.out.println("Enter Customer Name:");
                        String customerName = scanner.nextLine();
                        System.out.println("Enter Number of Guests:");
                        int numberOfGuests = scanner.nextInt();
                        scanner.nextLine();  // consume newline
                        System.out.println("Enter Reservation Date (yyyy-mm-dd):");
                        String date = scanner.nextLine();
                        service.makeReservation(new Reservation(0, restaurantId, customerName, java.sql.Date.valueOf(date), numberOfGuests));
                        System.out.println("Reservation made successfully.");
                        break;
                    case 3:
                        System.out.println("Enter Customer Name:");
                        String name = scanner.nextLine();
                        List<Reservation> reservations = service.viewReservations(name);
                        reservations.forEach(r -> System.out.println("Reservation at Restaurant ID: " + r.getRestaurantId() + " for " + r.getNumberOfGuests() + " guests on " + r.getReservationDate()));
                        break;
                    case 4:
                        System.out.println("Exiting...");
                        scanner.close();
                        return;
                    default:
                        System.out.println("Invalid choice. Please try again.");
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

