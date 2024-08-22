# Money-Heist-Merch

import dao.impl.FlightDAOImpl;
import service.FlightService;
import service.impl.FlightServiceImpl;
import pojo.Flight;

import java.util.List;
import java.util.Scanner;

public class AirlineSystem {
    private Scanner scanner = new Scanner(System.in);
    private FlightService flightService;

    public AirlineSystem() {
        FlightDAOImpl flightDAO = new FlightDAOImpl();
        this.flightService = new FlightServiceImpl(flightDAO);
    }

    public void start() {
        while (true) {
            System.out.println("\n1. Admin\n2. User\n3. Exit");
            System.out.print("Choose your role: ");
            int roleChoice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (roleChoice) {
                case 1:
                    adminMenu();
                    break;
                case 2:
                    userMenu();
                    break;
                case 3:
                    System.out.println("Exiting the system. Goodbye!");
                    return;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private void adminMenu() {
        while (true) {
            System.out.println("\nAdmin Menu:");
            System.out.println("1. Add Flight");
            System.out.println("2. View All Flights");
            System.out.println("3. View All Bookings");
            System.out.println("4. Exit to Main Menu");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    addFlight();
                    break;
                case 2:
                    viewAllFlights();
                    break;
                case 3:
                    viewAllBookings();
                    break;
                case 4:
                    return;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private void addFlight() {
        System.out.print("Enter Source: ");
        String source = scanner.nextLine();
        System.out.print("Enter Destination: ");
        String destination = scanner.nextLine();
        System.out.print("Enter Time: ");
        String time = scanner.nextLine();

        flightService.addFlight(source, destination, time);
        System.out.println("Flight added successfully!");
    }

    private void viewAllFlights() {
        System.out.println("\nAll Flights:");
        flightService.getAllFlights().forEach(System.out::println);
    }

    private void viewAllBookings() {
        System.out.println("\nAll Bookings:");
        // Implement viewing of all bookings if needed.
    }

    private void userMenu() {
        while (true) {
            System.out.println("\nUser Menu:");
            System.out.println("1. Book a One-Way Flight");
            System.out.println("2. Book a Two-Way Flight");
            System.out.println("3. Exit to Main Menu");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    bookOneWayFlight();
                    break;
                case 2:
                    bookTwoWayFlight();
                    break;
                case 3:
                    return;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private void bookOneWayFlight() {
        System.out.println("\nAvailable One-Way Flights:");
        List<Flight> flights = flightService.getAllFlights();
        flights.forEach(System.out::println);

        System.out.print("Select a flight by entering the flight number: ");
        int flightIndex = scanner.nextInt() - 1;
        scanner.nextLine(); // Consume newline

        if (flightIndex >= 0 && flightIndex < flights.size()) {
            Flight selectedFlight = flights.get(flightIndex);
            handleBooking(selectedFlight);
        } else {
            System.out.println("Invalid flight selection. Please try again.");
        }
    }

    private void bookTwoWayFlight() {
        System.out.println("\nAvailable Outbound Flights:");
        List<Flight> outboundFlights = flightService.getAllFlights();
        outboundFlights.forEach(System.out::println);

        System.out.print("Select an outbound flight by entering the flight number: ");
        int outboundFlightIndex = scanner.nextInt() - 1;
        scanner.nextLine(); // Consume newline

        if (outboundFlightIndex >= 0 && outboundFlightIndex < outboundFlights.size()) {
            Flight outboundFlight = outboundFlights.get(outboundFlightIndex);
            System.out.println("Selected Outbound Flight: " + outboundFlight);

            System.out.print("Enter Return Date (format: YYYY-MM-DD): ");
            String returnDate = scanner.nextLine();

            Flight returnFlight = findReturnFlight(outboundFlight.getDestination(), outboundFlight.getSource(), returnDate);
            if (returnFlight != null) {
                System.out.println("Selected Return Flight: " + returnFlight);
                handleBooking(outboundFlight);
                handleBooking(returnFlight);
            } else {
                System.out.println("Sorry, there's no flight from the destination to the source.");
            }
        } else {
            System.out.println("Invalid flight selection. Please try again.");
        }
    }

    private Flight findReturnFlight(String source, String destination, String date) {
        for (Flight flight : flightService.getAllFlights()) {
            if (flight.getSource().equals(source) && flight.getDestination().equals(destination)) {
                return flight;
            }
        }
        return null;
    }

    private void handleBooking(Flight flight) {
        boolean bookingDone = false;
        while (!bookingDone) {
            displaySeats(flight);
            System.out.print("Enter seat number to book or 0 to cancel: ");
            int seatNumber = scanner.nextInt() - 1;
            scanner.nextLine(); // Consume newline

            if (seatNumber == -1) {
                bookingDone = true;
            } else if (seatNumber >= 0 && seatNumber < flight.getSeats().length) {
                if (flightService.bookSeat(flight, seatNumber)) {
                    System.out.print("Enter Passenger Name: ");
                    String passengerName = scanner.nextLine();
                    System.out.println("Booking successful! Seat Number: " + (seatNumber + 1));
                } else {
                    System.out.println("Seat already booked. Please choose a different seat.");
                }
            } else {
                System.out.println("Invalid seat number. Please try again.");
            }
        }
    }

    private void displaySeats(Flight flight) {
        String[] seats = flight.getSeats();
        System.out.println("\nSeat Availability:");
        for (int i = 0; i < seats.length; i++) {
            System.out.println((i + 1) + ": " + seats[i]);
        }
    }
}


















cancel user seats



booking.java


public class Booking {
    private String bookingId;
    private Flight flight;
    private String passengerName;
    private int seatNumber;
    private static int bookingCounter = 1000;

    public Booking(Flight flight, String passengerName, int seatNumber) {
        this.bookingId = "BK" + (++bookingCounter);
        this.flight = flight;
        this.passengerName = passengerName;
        this.seatNumber = seatNumber;
    }

    public String getBookingId() { return bookingId; }
    public Flight getFlight() { return flight; }
    public String getPassengerName() { return passengerName; }
    public int getSeatNumber() { return seatNumber; }

    @Override
    public String toString() {
        return "Booking ID: " + bookingId + ", Passenger Name: " + passengerName + ", Seat Number: " + (seatNumber + 1) + ", " + flight.toString();
    }

    public void cancelBooking() {
        flight.getSeats()[seatNumber] = "Available";
    }
}














import dao.impl.FlightDAOImpl;
import service.FlightService;
import service.impl.FlightServiceImpl;
import pojo.Flight;

import java.util.List;
import java.util.Scanner;

public class AirlineSystem {
    private Scanner scanner = new Scanner(System.in);
    private FlightService flightService;

    public AirlineSystem() {
        FlightDAOImpl flightDAO = new FlightDAOImpl();
        this.flightService = new FlightServiceImpl(flightDAO);
    }

    public void start() {
        while (true) {
            System.out.println("\n1. Admin\n2. User\n3. Exit");
            System.out.print("Choose your role: ");
            int roleChoice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (roleChoice) {
                case 1:
                    adminMenu();
                    break;
                case 2:
                    userMenu();
                    break;
                case 3:
                    System.out.println("Exiting the system. Goodbye!");
                    return;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private void adminMenu() {
        while (true) {
            System.out.println("\nAdmin Menu:");
            System.out.println("1. Add Flight");
            System.out.println("2. View All Flights");
            System.out.println("3. View All Bookings");
            System.out.println("4. Cancel a Booking");
            System.out.println("5. Exit to Main Menu");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    addFlight();
                    break;
                case 2:
                    viewAllFlights();
                    break;
                case 3:
                    viewAllBookings();
                    break;
                case 4:
                    cancelBooking();
                    break;
                case 5:
                    return;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private void addFlight() {
        System.out.print("Enter Source: ");
        String source = scanner.nextLine();
        System.out.print("Enter Destination: ");
        String destination = scanner.nextLine();
        System.out.print("Enter Time: ");
        String time = scanner.nextLine();

        flightService.addFlight(source, destination, time);
        System.out.println("Flight added successfully!");
    }

    private void viewAllFlights() {
        System.out.println("\nAll Flights:");
        flightService.getAllFlights().forEach(System.out::println);
    }

    private void viewAllBookings() {
        System.out.println("\nAll Bookings:");
        // Implement viewing of all bookings if needed.
    }

    private void cancelBooking() {
        System.out.print("Enter Booking ID to cancel: ");
        String bookingId = scanner.nextLine();
        boolean cancelled = flightService.cancelBooking(bookingId);
        if (cancelled) {
            System.out.println("Booking cancelled successfully. The seat is now available.");
        } else {
            System.out.println("Booking ID not found.");
        }
    }

    private void userMenu() {
        while (true) {
            System.out.println("\nUser Menu:");
            System.out.println("1. Book a One-Way Flight");
            System.out.println("2. Book a Two-Way Flight");
            System.out.println("3. View My Bookings");
            System.out.println("4. Cancel a Booking");
            System.out.println("5. Exit to Main Menu");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    bookOneWayFlight();
                    break;
                case 2:
                    bookTwoWayFlight();
                    break;
                case 3:
                    viewMyBookings();
                    break;
                case 4:
                    cancelBooking();
                    break;
                case 5:
                    return;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private void bookOneWayFlight() {
        System.out.println("\nAvailable One-Way Flights:");
        List<Flight> flights = flightService.getAllFlights();
        flights.forEach(System.out::println);

        System.out.print("Select a flight by entering the flight number: ");
        int flightIndex = scanner.nextInt() - 1;
        scanner.nextLine(); // Consume newline

        if (flightIndex >= 0 && flightIndex < flights.size()) {
            Flight selectedFlight = flights.get(flightIndex);
            handleBooking(selectedFlight);
        } else {
            System.out.println("Invalid flight selection. Please try again.");
        }
    }

    private void bookTwoWayFlight() {
        System.out.println("\nAvailable Outbound Flights:");
        List<Flight> outboundFlights = flightService.getAllFlights();
        outboundFlights.forEach(System.out::println);

        System.out.print("Select an outbound flight by entering the flight number: ");
        int outboundFlightIndex = scanner.nextInt() - 1;
        scanner.nextLine(); // Consume newline

        if (outboundFlightIndex >= 0 && outboundFlightIndex < outboundFlights.size()) {
            Flight outboundFlight = outboundFlights.get(outboundFlightIndex);
            System.out.println("Selected Outbound Flight: " + outboundFlight);

            System.out.print("Enter Return Date (format: YYYY-MM-DD): ");
            String returnDate = scanner.nextLine();

            Flight returnFlight = findReturnFlight(outboundFlight.getDestination(), outboundFlight.getSource(), returnDate);
            if (returnFlight != null) {
                System.out.println("Selected Return Flight: " + returnFlight);
                handleBooking(outboundFlight);
                handleBooking(returnFlight);
            } else {
                System.out.println("Sorry, there's no flight from the destination to the source.");
            }
        } else {
            System.out.println("Invalid flight selection. Please try again.");
        }
    }

    private Flight findReturnFlight(String source, String destination, String date) {
        for (Flight flight : flightService.getAllFlights()) {
            if (flight.getSource().equals(source) && flight.getDestination().equals(destination)) {
                return flight;
            }
        }
        return null;
    }

    private void handleBooking(Flight flight) {
        boolean bookingDone = false;
        while (!bookingDone) {
            displaySeats(flight);
            System.out.print("Enter seat number to book or 0 to cancel: ");
            int seatNumber = scanner.nextInt() - 1;
            scanner.nextLine(); // Consume newline

            if (seatNumber == -1) {
                bookingDone = true;
            } else if (seatNumber >= 0 && seatNumber < flight.getSeats().length) {
                if (flightService.bookSeat(flight, seatNumber)) {
                    System.out.print("Enter Passenger Name: ");
                    String passengerName = scanner.nextLine();
                    System.out.println("Booking successful! Seat Number: " + (seatNumber + 1));
                } else {
                    System.out.println("Seat already booked. Please choose a different seat.");
                }
            } else {
                System.out.println("Invalid seat number. Please try again.");
            }
        }
    }

    private void displaySeats(Flight flight) {
        String[] seats = flight.getSeats();
        System.out.println("\nSeat Availability:");
        for (int i = 0; i < seats.length; i++) {
            System.out.println((i + 1) + ": " + seats[i]);
        }
    }

    private void viewMyBookings() {
        // Implement logic to view user's specific bookings.
    }
}