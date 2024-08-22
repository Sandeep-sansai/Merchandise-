# Money-Heist-Merch

package pojo;

public class Ticket {
    private int ticketId;
    private int flightId;
    private int seatNumber;
    private String userName;

    public Ticket(int ticketId, int flightId, int seatNumber, String userName) {
        this.ticketId = ticketId;
        this.flightId = flightId;
        this.seatNumber = seatNumber;
        this.userName = userName;
    }

    // Getters and Setters
    public int getTicketId() { return ticketId; }
    public void setTicketId(int ticketId) { this.ticketId = ticketId; }
    public int getFlightId() { return flightId; }
    public void setFlightId(int flightId) { this.flightId = flightId; }
    public int getSeatNumber() { return seatNumber; }
    public void setSeatNumber(int seatNumber) { this.seatNumber = seatNumber; }
    public String getUserName() { return userName; }
    public void setUserName(String userName) { this.userName = userName; }
}