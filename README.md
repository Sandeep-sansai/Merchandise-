# Money-Heist-Merch

POJO


FLIGHT.JAVA


package pojo;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

public class Flight {
    private static int idCounter = 1;
    private int flightId;
    private String source;
    private String destination;
    private Date date;
    private String time;
    private Map<Integer, Boolean> seats; // true = booked, false = available

    public Flight(String source, String destination, Date date, String time, int seatCount) {
        this.flightId = idCounter++;
        this.source = source;
        this.destination = destination;
        this.date = date;
        this.time = time;
        this.seats = new HashMap<>();
        for (int i = 1; i <= seatCount; i++) {
            this.seats.put(i, false); // initialize all seats as available
        }
    }

    public int getFlightId() {
        return flightId;
    }

    public String getSource() {
        return source;
    }

    public String getDestination() {
        return destination;
    }

    public Date getDate() {
        return date;
    }

    public String getTime() {
        return time;
    }

    public Map<Integer, Boolean> getSeats() {
        return seats;
    }

    public void bookSeat(int seatNumber) {
        this.seats.put(seatNumber, true);
    }

    public void cancelSeat(int seatNumber) {
        this.seats.put(seatNumber, false);
    }
}



TICKET.JAVA



package pojo;

public class Ticket {
    private static int ticketCounter = 1;
    private int ticketId;
    private int flightId;
    private int seatNumber;
    private String userName;

    public Ticket(int flightId, int seatNumber, String userName) {
        this.ticketId = ticketCounter++;
        this.flightId = flightId;
        this.seatNumber = seatNumber;
        this.userName = userName;
    }

    public int getTicketId() {
        return ticketId;
    }

    public int getFlightId() {
        return flightId;
    }

    public int getSeatNumber() {
        return seatNumber;
    }

    public String getUserName() {
        return userName;
    }
}



FLIGHT DAO

package dao;

import pojo.Flight;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

public class FlightDAO {
    private List<Flight> flights = new ArrayList<>();

    public void addFlight(Flight flight) {
        flights.add(flight);
    }

    public List<Flight> getAllFlights() {
        return flights;
    }

    public Flight getFlightById(int flightId) {
        for (Flight flight : flights) {
            if (flight.getFlightId() == flightId) {
                return flight;
            }
        }
        return null;
    }

    public void removeFlight(int flightId) {
        flights.removeIf(flight -> flight.getFlightId() == flightId);
    }

    public List<Flight> findFlights(String source, String destination, Date date) {
        List<Flight> matchingFlights = new ArrayList<>();
        for (Flight flight : flights) {
            if (flight.getSource().equalsIgnoreCase(source)
                    && flight.getDestination().equalsIgnoreCase(destination)
                    && flight.getDate().equals(date)) {
                matchingFlights.add(flight);
            }
        }
        return matchingFlights;
    }
}


TICKET DAO




package dao;

import pojo.Ticket;
import java.util.ArrayList;
import java.util.List;

public class TicketDAO {
    private List<Ticket> tickets = new ArrayList<>();

    public void addTicket(Ticket ticket) {
        tickets.add(ticket);
    }

    public List<Ticket> getTicketsByUser(String userName) {
        List<Ticket> userTickets = new ArrayList<>();
        for (Ticket ticket : tickets) {
            if (ticket.getUserName().equalsIgnoreCase(userName)) {
                userTickets.add(ticket);
            }
        }
        return userTickets;
    }

    public Ticket getTicketById(int ticketId) {
        for (Ticket ticket : tickets) {
            if (ticket.getTicketId() == ticketId) {
                return ticket;
            }
        }
        return null;
    }

    public void removeTicket(int ticketId) {
        tickets.removeIf(ticket -> ticket.getTicketId() == ticketId);
    }

    public List<Ticket> getAllTickets() {
        return tickets;
    }
}


FLIGHT SERVICE



package service;

import dao.FlightDAO;
import pojo.Flight;
import java.util.Date;
import java.util.List;

public class FlightService {
    private FlightDAO flightDAO = new FlightDAO();

    public void addFlight(String source, String destination, Date date, String time, int seatCount) {
        Flight flight = new Flight(source, destination, date, time, seatCount);
        flightDAO.addFlight(flight);
        System.out.println("Flight added successfully with ID: " + flight.getFlightId());
    }

    public List<Flight> getAllFlights() {
        return flightDAO.getAllFlights();
    }

    public void cancelFlight(int flightId) {
        flightDAO.removeFlight(flightId);
        System.out.println("Flight cancelled successfully with ID: " + flightId);
    }

    public List<Flight> findFlights(String source, String destination, Date date) {
        return flightDAO.findFlights(source, destination, date);
    }

    public Flight getFlightById(int flightId) {
        return flightDAO.getFlightById(flightId);
    }
}



TICKET SERVICE




package service;

import dao.TicketDAO;
import pojo.Flight;
import pojo.Ticket;
import java.util.List;

public class TicketService {
    private TicketDAO ticketDAO = new TicketDAO();

    public void bookTicket(Flight flight, int seatNumber, String userName) {
        if (flight.getSeats().get(seatNumber)) {
            System.out.println("Seat already booked!");
            return;
        }
        flight.bookSeat(seatNumber);
        Ticket ticket = new Ticket(flight.getFlightId(), seatNumber, userName);
        ticketDAO.addTicket(ticket);
        System.out.println("Ticket booked successfully! Ticket ID: " + ticket.getTicketId());
    }

    public void cancelTicket(int ticketId, Flight flight) {
        Ticket ticket = ticketDAO.getTicketById(ticketId);
        if (ticket != null) {
            flight.cancelSeat(ticket.getSeatNumber());
            ticketDAO.removeTicket(ticketId);
            System.out.println("Ticket cancelled successfully! Ticket ID: " + ticketId);
        } else {
            System.out.println("Ticket not found!");
        }
    }

    public List<Ticket> viewBookedTickets(String userName) {
        return ticketDAO.getTicketsByUser(userName);
    }

    public List<Ticket> getAllTickets() {
        return ticketDAO.getAllTickets();
    }
}




presentation


package presentation;

import service.FlightService;
import service.TicketService;
import pojo.Flight;
import pojo.Ticket;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;
import java.util.Scanner;

public class Main {
    private static FlightService flightService = new FlightService();
    private static TicketService ticketService = new TicketService();
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        while (true) {
            System.out.println("Are you an Admin or User? (Enter 'admin' or 'user' or 'exit' to quit): ");
            String userType = scanner.nextLine();

            if (userType.equalsIgnoreCase("admin")) {
                adminMenu();
            } else if (userType.equalsIgnoreCase("user")) {
                userMenu();
            } else if (userType.equalsIgnoreCase("exit")) {
                System.out.println("Exiting system...");
                break;
            } else {
                System.out.println("Invalid input, please try again.");
            }
        }
    }

    private static void adminMenu() {
        while (true) {
            System.out.println("Admin Menu:");
            System.out.println("1. Add Flight");
            System.out.println("2. View All Flights");
            System.out.println("3. Cancel Flight");
            System.out.println("4. View All Booked Tickets");
            System.out.println("5. Logout");
            System.out.println("Choose an option:");

            int choice = Integer.parseInt(scanner.nextLine());

            switch (choice) {
                case 1:
                    addFlight();
                    break;
                case 2:
                    viewAllFlights();
                    break;
                case 3:
                    cancelFlight();
                    break;
                case 4:
                    viewAllBookedTickets();
                    break;
                case 5:
                    System.out.println("Logging out...");
                    return;
                default:
                    System.out.println("Invalid choice, please try again.");
            }
        }
    }

    private static void userMenu() {
        System.out.println("Enter your username:");
        String userName = scanner.nextLine();

        while (true) {
            System.out.println("User Menu
:”);
System.out.println(“1. Book Ticket”);
System.out.println(“2. View My Booked Tickets”);
System.out.println(“3. Cancel Ticket”);
System.out.println(“4. Search Flights”);
System.out.println(“5. Logout”);
System.out.println(“Choose an option:”);

        int choice = Integer.parseInt(scanner.nextLine());

        switch (choice) {
            case 1:
                bookTicket(userName);
                break;
            case 2:
                viewMyBookedTickets(userName);
                break;
            case 3:
                cancelTicket(userName);
                break;
            case 4:
                searchFlights();
                break;
            case 5:
                System.out.println("Logging out...");
                return;
            default:
                System.out.println("Invalid choice, please try again.");
        }
    }
}

private static void addFlight() {
    try {
        System.out.println("Enter source:");
        String source = scanner.nextLine();
        System.out.println("Enter destination:");
        String destination = scanner.nextLine();
        System.out.println("Enter date (dd-MM-yyyy):");
        String dateStr = scanner.nextLine();
        Date date = new SimpleDateFormat("dd-MM-yyyy").parse(dateStr);
        System.out.println("Enter time (HH:mm):");
        String time = scanner.nextLine();
        System.out.println("Enter number of seats:");
        int seatCount = Integer.parseInt(scanner.nextLine());

        flightService.addFlight(source, destination, date, time, seatCount);
    } catch (ParseException e) {
        System.out.println("Invalid date format. Please try again.");
    } catch (NumberFormatException e) {
        System.out.println("Invalid input for seat count. Please enter a valid number.");
    }
}

private static void viewAllFlights() {
    List<Flight> flights = flightService.getAllFlights();
    if (flights.isEmpty()) {
        System.out.println("No flights available.");
    } else {
        for (Flight flight : flights) {
            System.out.println("Flight ID: " + flight.getFlightId() + ", Source: " + flight.getSource() + 
                               ", Destination: " + flight.getDestination() + ", Date: " + flight.getDate() + 
                               ", Time: " + flight.getTime());
        }
    }
}

private static void cancelFlight() {
    System.out.println("Enter the flight ID to cancel:");
    int flightId = Integer.parseInt(scanner.nextLine());
    flightService.cancelFlight(flightId);
}

private static void viewAllBookedTickets() {
    List<Ticket> tickets = ticketService.getAllTickets();
    if (tickets.isEmpty()) {
        System.out.println("No tickets booked.");
    } else {
        for (Ticket ticket : tickets) {
            System.out.println("Ticket ID: " + ticket.getTicketId() + ", Flight ID: " + ticket.getFlightId() + 
                               ", Seat Number: " + ticket.getSeatNumber() + ", User: " + ticket.getUserName());
        }
    }
}

private static void bookTicket(String userName) {
    searchFlights();
    System.out.println("Enter the flight ID to book:");
    int flightId = Integer.parseInt(scanner.nextLine());
    Flight flight = flightService.getFlightById(flightId);

    if (flight != null) {
        System.out.println("Available seats:");
        for (int seatNumber : flight.getSeats().keySet()) {
            if (!flight.getSeats().get(seatNumber)) {
                System.out.print(seatNumber + " ");
            }
        }
        System.out.println("\nEnter seat number to book:");
        int seatNumber = Integer.parseInt(scanner.nextLine());
        ticketService.bookTicket(flight, seatNumber, userName);
    } else {
        System.out.println("Flight not found.");
    }
}

private static void viewMyBookedTickets(String userName) {
    List<Ticket> tickets = ticketService.viewBookedTickets(userName);
    if (tickets.isEmpty()) {
        System.out.println("No tickets booked.");
    } else {
        for (Ticket ticket : tickets) {
            System.out.println("Ticket ID: " + ticket.getTicketId() + ", Flight ID: " + ticket.getFlightId() + 
                               ", Seat Number: " + ticket.getSeatNumber());
        }
    }
}

private static void cancelTicket(String userName) {
    viewMyBookedTickets(userName);
    System.out.println("Enter the ticket ID to cancel:");
    int ticketId = Integer.parseInt(scanner.nextLine());
    Ticket ticket = ticketService.getTicketById(ticketId);
    if (ticket != null) {
        Flight flight = flightService.getFlightById(ticket.getFlightId());
        ticketService.cancelTicket(ticketId, flight);
    } else {
        System.out.println("Ticket not found.");
    }
}

private static void searchFlights() {
    try {
        System.out.println("Enter source:");
        String source = scanner.nextLine();
        System.out.println("Enter destination:");
        String destination = scanner.nextLine();
        System.out.println("Enter date (dd-MM-yyyy):");
        String dateStr = scanner.nextLine();
        Date date = new SimpleDateFormat("dd-MM-yyyy").parse(dateStr);

        List<Flight> flights = flightService.findFlights(source, destination, date);
        if (flights.isEmpty()) {
            System.out.println("No flights found for the specified criteria.");
        } else {
            for (Flight flight : flights) {
                System.out.println("Flight ID: " + flight.getFlightId() + ", Time: " + flight.getTime());
            }
        }
    } catch (ParseException e) {
        System.out.println("Invalid date format. Please try again.");
    }
}

}
