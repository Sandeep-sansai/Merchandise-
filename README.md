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
        return new ArrayList<>(flights);
    }

    public Flight getFlightById(int flightId) {
        return flights.stream().filter(flight -> flight.getFlightId() == flightId).findFirst().orElse(null);
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