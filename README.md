import java.util.List;
import java.util.Scanner;

public class AdminConsole {
    private FlightService flightService;
    private BookingService bookingService;
    private Scanner scanner;

    public AdminConsole(FlightService flightService, BookingService bookingService) {
        this.flightService = flightService;
        this.bookingService = bookingService;
        this.scanner = new Scanner(System.in);
    }

    public void start() {
        while (true) {
            System.out.println("\nAdmin Menu:");
            System.out.println("1. Add a flight");
            System.out.println("2. View all bookings");
            System.out.println("3. View a particular booking");
            System.out.println("4. Display flights by date");
            System.out.println("5. Display flights by source and destination");
            System.out.println("6. Cancel a flight");
            System.out.println("7. Logout");

            System.out.print("Select an option: ");
            int option = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (option) {
                case 1:
                    addFlight();
                    break;
                case 2:
                    viewAllBookings();
                    break;
                case 3:
                    viewBookingById();
                    break;
                case 4:
                    displayFlightsByDate();
                    break;
                case 5:
                    displayFlightsBySourceAndDestination();
                    break;
                case 6:
                    cancelFlight();
                    break;
                case 7:
                    return; // Logout
                default:
                    System.out.println("Invalid option. Please try again.");
            }
        }
    }

    private void addFlight() {
        System.out.print("Enter Flight ID: ");
        String flightId = scanner.nextLine();
        System.out.print("Enter Source: ");
        String source = scanner.nextLine();
        System.out.print("Enter Destination: ");
        String destination = scanner.nextLine();
        System.out.print("Enter Date (YYYY-MM-DD): ");
        String date = scanner.nextLine();
        System.out.print("Enter Time (HH:MM): ");
        String time = scanner.nextLine();
        System.out.print("Enter Total Seats: ");
        int totalSeats = scanner.nextInt();
        scanner.nextLine(); // Consume newline

        Flight flight = new Flight(flightId, source, destination, date, time, totalSeats);
        flightService.addFlight(flight);
        System.out.println("Flight added successfully!");
    }

    private void viewAllBookings() {
        List<Ticket> bookings = bookingService.getAllBookings();
        if (bookings.isEmpty()) {
            System.out.println("No bookings found.");
        } else {
            bookings.forEach(System.out::println);
        }
    }

    private void viewBookingById() {
        System.out.print("Enter Ticket ID: ");
        String ticketId = scanner.nextLine();
        Ticket ticket = bookingService.getBookingById(ticketId);
        if (ticket != null) {
            System.out.println(ticket);
        } else {
            System.out.println("Booking not found.");
        }
    }

    private void displayFlightsByDate() {
        System.out.print("Enter Date (YYYY-MM-DD): ");
        String date = scanner.nextLine();
        List<Flight> flights = flightService.getFlightsByDate(date);
        if (flights.isEmpty()) {
            System.out.println("No flights found for this date.");
        } else {
            flights.forEach(System.out::println);
        }
    }

    private void displayFlightsBySourceAndDestination() {
        System.out.print("Enter Source: ");
        String source = scanner.nextLine();
        System.out.print("Enter Destination: ");
        String destination = scanner.nextLine();
        List<Flight> flights = flightService.getFlightsBySourceAndDestination(source, destination);
        if (flights.isEmpty()) {
            System.out.println("No flights found for this route.");
        } else {
            flights.forEach(System.out::println);
        }
    }

    private void cancelFlight() {
        System.out.print("Enter Flight ID: ");
        String flightId = scanner.nextLine();
        flightService.removeFlight(flightId);
        System.out.println("Flight canceled successfully!");
    }
}























import java.util.List;
import java.util.Scanner;

public class UserConsole {
    private FlightService flightService;
    private BookingService bookingService;
    private Scanner scanner;

    public UserConsole(FlightService flightService, BookingService bookingService) {
        this.flightService = flightService;
        this.bookingService = bookingService;
        this.scanner = new Scanner(System.in);
    }

    public void start(String userId) {
        while (true) {
            System.out.println("\nUser Menu:");
            System.out.println("1. Book a one-way flight");
            System.out.println("2. Book a two-way flight");
            System.out.println("3. Cancel a ticket");
            System.out.println("4. View your bookings");
            System.out.println("5. Logout");

            System.out.print("Select an option: ");
            int option = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (option) {
                case 1:
                    bookOneWayFlight(userId);
                    break;
                case 2:
                    bookTwoWayFlight(userId);
                    break;
                case 3:
                    cancelTicket(userId);
                    break;
                case 4:
                    viewUserBookings(userId);
                    break;
                case 5:
                    return; // Logout
                default:
                    System.out.println("Invalid option. Please try again.");
            }
        }
    }

    private void bookOneWayFlight(String userId) {
        System.out.print("Enter Flight ID: ");
        String flightId = scanner.nextLine();
        System.out.print("Enter Passenger Name: ");
        String passengerName = scanner.nextLine();
        System.out.print("Enter Seat Number: ");
        int seatNumber = scanner.nextInt();
        System.out.print("Enter Passenger Age: ");
        int age = scanner.nextInt();
        scanner.nextLine(); // Consume newline

        Flight flight = flightService.getFlightById(flightId);
        if (flight != null && flight.getBookedSeats() < flight.getTotalSeats()) {
            Ticket ticket = new Ticket(generateTicketId(), flightId, passengerName, seatNumber, flight.getDate(), age);
            bookingService.bookFlight(ticket, userId);
            flight.setBookedSeats(flight.getBookedSeats() + 1);
            System.out.println("Flight booked successfully! Ticket ID: " + ticket.getTicketId());
        } else {
            System.out.println("Flight not available or fully booked.");
        }
    }

    private void bookTwoWayFlight(String userId) {
        System.out.print("Enter Source: ");
        String source = scanner.nextLine();
        System.out.print("Enter Destination: ");
        String destination = scanner.nextLine();

        List<Flight> flights = flightService.getFlightsBySourceAndDestination(source, destination);
        if (flights.isEmpty()) {
            System.out.println("No flights available for the selected route.");
        } else {
            System.out.println("Available departure flights:");
            flights.forEach(System.out::println);

            System.out.print("Enter Flight ID for departure: ");
            String flightId1 = scanner.nextLine();
            System.out.print("Enter Passenger Name: ");
            String passengerName1 = scanner.nextLine();
            System.out.print("Enter Seat Number: ");
            int seatNumber1 = scanner.nextInt();
            System.out.print("Enter Passenger Age: ");
            int age1 = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            System.out.println("Available return flights:");
            List<Flight> returnFlights = flightService.getFlightsBySourceAndDestination(destination, source);
            if (returnFlights.isEmpty()) {
                System.out.println("No return flights available.");
                return;
            }
            returnFlights.forEach(System.out::println);

            System.out.print("Enter Flight ID for return: ");
            String flightId2 = scanner.nextLine();
            System.out.print("Enter Passenger Name: ");
            String passengerName2 = scanner.nextLine();
            System.out.print("Enter Seat Number: ");
            int seatNumber2 = scanner.nextInt();
            System.out.print("Enter Passenger Age: ");
            int age2 = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            Flight flight1 = flightService.getFlightById(flightId1);
            Flight flight2 = flightService.getFlightById(flightId2);

            if (flight1 != null && flight1.getBookedSeats() < flight1.getTotalSeats()
                    && flight2 != null && flight2.getBookedSeats() < flight2.getTotalSeats()) {
                Ticket ticket1 = new Ticket(generateTicketId(), flightId1, passengerName1, seatNumber1, flight1.getDate(), age1);
                Ticket ticket2 = new Ticket(generateTicketId(), flightId2, passengerName2, seatNumber2, flight2.getDate(), age2);
                bookingService.bookFlight(ticket1, userId);
                bookingService.bookFlight(ticket2, userId);
                flight1.setBookedSeats(flight1.getBookedSeats() + 1);
                flight2.setBookedSeats(flight2.getBookedSeats() + 1);
                System.out.println("Two-way flight booked successfully!");
                System.out.println("Departure Ticket ID: " + ticket1.getTicketId());
                System.out.println("Return Ticket ID: " + ticket2.getTicketId());
            } else {
                System.out.println("One or both flights are fully booked or not available.");
            }
        }
    }

    private void cancelTicket(String userId) {
        System.out.print("Enter Ticket ID: ");
        String ticketId = scanner.nextLine();
        Ticket ticket = bookingService.getBookingById(ticketId);

        if (ticket != null && ticket.getUserId().equals(userId)) {
            bookingService.cancelBooking(ticketId);
            Flight flight = flightService.getFlightById(ticket.getFlightId());
            if (flight != null) {
                flight.setBookedSeats(flight.getBookedSeats() - 1);
            }
            System.out.println("Ticket canceled successfully!");
        } else {
            System.out.println("Ticket not found or does not belong to you.");
        }
    }

    private void viewUserBookings(String userId) {
        List<Ticket> bookings = bookingService.getUserBookings(userId);
        if (bookings.isEmpty()) {
            System.out.println("You have no bookings.");
        } else {
            bookings.forEach(System.out::println);
        }
    }

    private String generateTicketId() {
        return "TICKET-" + System.currentTimeMillis();
    }
}