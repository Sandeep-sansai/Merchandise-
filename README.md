# Money-Heist-Merch

import dao.Flight

DAO;
import pojo.Flight;

import java.util.List;

public class FlightServiceImpl implements FlightService {
    private FlightDAO flightDAO;

    public FlightServiceImpl(FlightDAO flightDAO) {
        this.flightDAO = flightDAO;
    }

    @Override
    public void addFlight(String source, String destination, String time) {
        flightDAO.addFlight(new Flight(source, destination, time));
    }

    @Override
    public List<Flight> getAllFlights() {
        return flightDAO.getAllFlights();
    }

    @Override
    public boolean bookSeat(Flight flight, int seatNumber) {
        return flight.bookSeat(seatNumber);
    }

    @Override
    public boolean cancelBooking(String bookingId) {
        List<Flight> flights = flightDAO.getAllFlights();
        for (Flight flight : flights) {
            List<Booking> bookings = flight.getBookings();
            for (Booking booking : bookings) {
                if (booking.getBookingId().equals(bookingId)) {
                    booking.cancelBooking();
                    bookings.remove(booking);
                    return true;
                }
            }
        }
        return false;
    }
}