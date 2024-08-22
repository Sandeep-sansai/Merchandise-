# Money-Heist-Merch

package pojo;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

public class Flight {
    private int flightId;
    private String source;
    private String destination;
    private Date date;
    private String time;
    private Map<Integer, Boolean> seats; // Seat number as key, booked status as value

    public Flight(int flightId, String source, String destination, Date date, String time, int seatCount) {
        this.flightId = flightId;
        this.source = source;
        this.destination = destination;
        this.date = date;
        this.time = time;
        this.seats = new HashMap<>();
        for (int i = 1; i <= seatCount; i++) {
            seats.put(i, false); // Initialize all seats as unbooked
        }
    }

    // Getters and Setters
    public int getFlightId() { return flightId; }
    public void setFlightId(int flightId) { this.flightId = flightId; }
    public String getSource() { return source; }
    public void setSource(String source) { this.source = source; }
    public String getDestination() { return destination; }
    public void setDestination(String destination) { this.destination = destination; }
    public Date getDate() { return date; }
    public void setDate(Date date) { this.date = date; }
    public String getTime() { return time; }
    public void setTime(String time) { this.time = time; }
    public Map<Integer, Boolean> getSeats() { return seats; }
    public void setSeats(Map<Integer, Boolean> seats) { this.seats = seats; }
}