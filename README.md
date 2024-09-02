import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class MenuDAO {
    private Connection connection;

    public MenuDAO(Connection connection) {
        this.connection = connection;
    }

    public List<Menu> getMenuByRestaurantId(int restaurantId) throws SQLException {
        List<Menu> menuList = new ArrayList<>();
        String query = "SELECT * FROM menus WHERE restaurantId = ?";
        try (PreparedStatement pstmt = connection.prepareStatement(query)) {
            pstmt.setInt(1, restaurantId);
            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    menuList.add(new Menu(
                        rs.getInt("id"),
                        rs.getInt("restaurantId"),
                        rs.getString("itemName"),
                        rs.getDouble("price"),
                        rs.getString("description")
                    ));
                }
            }
        }
        return menuList;
    }
}



import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class UserRatingDAO {
    private Connection connection;

    public UserRatingDAO(Connection connection) {
        this.connection = connection;
    }

    public List<UserRating> getRatingsByRestaurantId(int restaurantId) throws SQLException {
        List<UserRating> ratingsList = new ArrayList<>();
        String query = "SELECT * FROM user_ratings WHERE restaurantId = ?";
        try (PreparedStatement pstmt = connection.prepareStatement(query)) {
            pstmt.setInt(1, restaurantId);
            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    ratingsList.add(new UserRating(
                        rs.getInt("id"),
                        rs.getInt("restaurantId"),
                        rs.getInt("userId"),
                        rs.getInt("rating"),
                        rs.getString("review")
                    ));
                }
            }
        }
        return ratingsList;
    }
}



import java.sql.SQLException;
import java.util.List;

public class RestaurantService {
    private RestaurantDAO restaurantDAO;
    private MenuDAO menuDAO;
    private UserRatingDAO userRatingDAO;

    public RestaurantService(RestaurantDAO restaurantDAO, MenuDAO menuDAO, UserRatingDAO userRatingDAO) {
        this.restaurantDAO = restaurantDAO;
        this.menuDAO = menuDAO;
        this.userRatingDAO = userRatingDAO;
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

    public List<Menu> getMenuByRestaurant(int restaurantId) throws SQLException {
        return menuDAO.getMenuByRestaurantId(restaurantId);
    }

    public List<UserRating> getRatingsByRestaurant(int restaurantId) throws SQLException {
        return userRatingDAO.getRatingsByRestaurantId(restaurantId);
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
            MenuDAO menuDAO = new MenuDAO(connection);
            UserRatingDAO userRatingDAO = new UserRatingDAO(connection);
            RestaurantService service = new RestaurantService(restaurantDAO, menuDAO, userRatingDAO);

            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.println("1. View Restaurants by Filters");
                System.out.println("2. Make a Reservation");
                System.out.println("3. View Reservations");
                System.out.println("4. View Restaurant Menu");
                System.out.println("5. View Restaurant Ratings");
                System.out.println("6. Exit");
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
                        reservations.forEach(r -> System.out.println("Reservation at Restaurant ID: " + r.getRestaurantId() + " on " + r.getReservationDate()));
                        break;
                    case 4:
                        System.out.println("Enter Restaurant ID:");
                        int menuRestaurantId = scanner.nextInt();
                        List<Menu> menus = service.getMenuByRestaurant(menuRestaurantId);
                        menus.forEach(m -> System.out.println(m.getItemName() + " - $" + m.getPrice() + ": " + m.getDescription()));
                        break;
                    case 5:
                        System.out.println("Enter Restaurant ID:");
                        int ratingRestaurantId = scanner.nextInt();
                        List<UserRating> ratings = service.getRatingsByRestaurant(ratingRestaurantId);
                        ratings.forEach(r -> System.out.println("User ID: " + r.getUserId() + ", Rating: " + r.getRating() + " - " + r.getReview()));
                        break;
                    case 6:
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