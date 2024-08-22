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
        return tickets.stream().filter(ticket -> ticket.getTicketId() == ticketId).findFirst().orElse(null);
    }

    public void removeTicket(int ticketId) {
        tickets.removeIf(ticket -> ticket.getTicketId() == ticketId);
    }

    public List<Ticket> getAllTickets() {
        return new ArrayList<>(tickets);
    }
}