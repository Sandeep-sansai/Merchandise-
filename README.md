# Money-Heist-Merch


UP FLIGHT DAO

package dao;

import pojo.Flight;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

public class FlightDAO {
    private List<Flight> flights = new ArrayList<>(); // Ensure the List is parameterized with Flight

    public void addFlight(Flight flight) {
        flights.add(flight);
    }

    public List<Flight> getAllFlights() {
        return flights; // This will now return a List<Flight>
    }

    public Flight getFlightById(int flightId) {
        for (Flight flight : flights) { // Correct type usage in the loop
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
        List<Flight> matchingFlights = new ArrayList<>(); // Ensure correct type
        for (Flight flight : flights) { // Correct type usage in the loop
            if (flight.getSource().equalsIgnoreCase(source)
                    && flight.getDestination().equalsIgnoreCase(destination)
                    && flight.getDate().equals(date)) {
                matchingFlights.add(flight);
            }
        }
        return matchingFlights;
    }
}



TICKET

package dao;

import pojo.Ticket;
import java.util.ArrayList;
import java.util.List;

public class TicketDAO {
    private List<Ticket> tickets = new ArrayList<>(); // Ensure the List is parameterized with Ticket

    public void addTicket(Ticket ticket) {
        tickets.add(ticket);
    }

    public List<Ticket> getTicketsByUser(String userName) {
        List<Ticket> userTickets = new ArrayList<>(); // Ensure correct type
        for (Ticket ticket : tickets) { // Correct type usage in the loop
            if (ticket.getUserName().equalsIgnoreCase(userName)) {
                userTickets.add(ticket);
            }
        }
        return userTickets;
    }

    public Ticket getTicketById(int ticketId) {
        for (Ticket ticket : tickets) { // Correct type usage in the loop
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
        return tickets; // This will now return a List<Ticket>
    }
}
