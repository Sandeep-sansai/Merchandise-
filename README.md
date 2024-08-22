package service;

import dao.FlightDAO;
import pojo.Flight;
import java.util.Date;
import java.util.List;

public class FlightService {
    private FlightDAO flightDAO = new FlightDAO();

    public void addFlight(String source, String destination, Date date, String time, int seatCount) {
        int flightId = flightDAO.getAllFlights().size() + 1; // Simple ID generation
        Flight flight = new Flight(flightId, source, destination, date, time, seatCount);
        flightDAO.addFlight(flight);
    }

    public List<Flight> getAllFlights() {
        return flightDAO.getAllFlights();
    }

    public Flight getFlightById(int flightId) {
        return flightDAO.getFlightById(flightId);
    }

    public void cancelFlight(int flightId) {
        flightDAO.removeFlight(flightId);
    }

    public List<Flight> findFlights(String source, String destination, Date date) {
        return flightDAO.findFlights(source, destination, date);
    }
}