CREATE TABLE Users (
    userId VARCHAR(50) PRIMARY KEY,
    username VARCHAR(100) NOT NULL,
    password VARCHAR(100) NOT NULL
);



CREATE TABLE Flights (
    flightId VARCHAR(50) PRIMARY KEY,
    source VARCHAR(100) NOT NULL,
    destination VARCHAR(100) NOT NULL,
    date DATE NOT NULL,
    time TIME NOT NULL,
    totalSeats INT NOT NULL,
    bookedSeats INT NOT NULL
);


CREATE TABLE Tickets (
    ticketId VARCHAR(50) PRIMARY KEY,
    flightId VARCHAR(50),
    passengerName VARCHAR(100) NOT NULL,
    seatNumber INT NOT NULL,
    date DATE NOT NULL,
    age INT NOT NULL,
    userId VARCHAR(50),
    FOREIGN KEY (flightId) REFERENCES Flights(flightId),
    FOREIGN KEY (userId) REFERENCES Users(userId)
);


INSERT INTO Users (userId, username, password) VALUES
('U001', 'admin', 'adminpass'),
('U002', 'john_doe', 'password123'),
('U003', 'jane_doe', 'mypassword'),
('U004', 'alice_smith', 'alicepass'),
('U005', 'bob_jones', 'bobspass');


INSERT INTO Flights (flightId, source, destination, date, time, totalSeats, bookedSeats) VALUES
('F001', 'New York', 'Los Angeles', '2024-09-01', '08:00:00', 150, 50),
('F002', 'Chicago', 'Miami', '2024-09-02', '09:30:00', 200, 75),
('F003', 'San Francisco', 'Seattle', '2024-09-03', '10:45:00', 100, 25),
('F004', 'Dallas', 'Houston', '2024-09-04', '14:15:00', 120, 60),
('F005', 'Boston', 'Washington D.C.', '2024-09-05', '16:30:00', 180, 90);

INSERT INTO Tickets (ticketId, flightId, passengerName, seatNumber, date, age, userId) VALUES
('T001', 'F001', 'John Doe', 1, '2024-09-01', 30, 'U002'),
('T002', 'F002', 'Jane Doe', 5, '2024-09-02', 28, 'U003'),
('T003', 'F003', 'Alice Smith', 10, '2024-09-03', 35, 'U004'),
('T004', 'F004', 'Bob Jones', 15, '2024-09-04', 40, 'U005'),
('T005', 'F001', 'Chris Johnson', 20, '2024-09-01', 25, 'U002');


INSERT INTO Flights (flightId, source, destination, date, time, totalSeats, bookedSeats)
VALUES ('F006', 'Atlanta', 'Orlando', '2024-09-06', '12:00:00', 150, 0);


SELECT * FROM Tickets;


SELECT * FROM Tickets WHERE ticketId = 'T001';


SELECT * FROM Flights WHERE date = '2024-09-01';


SELECT * FROM Flights WHERE source = 'New York' AND destination = 'Los Angeles';


DELETE FROM Flights WHERE flightId = 'F006';


INSERT INTO Tickets (ticketId, flightId, passengerName, seatNumber, date, age, userId)
VALUES ('T006', 'F002', 'Emily White', 6, '2024-09-02', 22, 'U003');

UPDATE Flights
SET bookedSeats = bookedSeats + 1
WHERE flightId = 'F002';


INSERT INTO Tickets (ticketId, flightId, passengerName, seatNumber, date, age, userId)
VALUES ('T007', 'F003', 'Michael Brown', 7, '2024-09-03', 30, 'U004'),
       ('T008', 'F004', 'Michael Brown', 8, '2024-09-04', 30, 'U004');

UPDATE Flights
SET bookedSeats = bookedSeats + 1
WHERE flightId IN ('F003', 'F004');


DELETE FROM Tickets WHERE ticketId = 'T001';

UPDATE Flights
SET bookedSeats = bookedSeats - 1
WHERE flightId = (SELECT flightId FROM Tickets WHERE ticketId = 'T001');


SELECT * FROM Tickets WHERE userId = 'U002';


SELECT 
    t.ticketId,
    t.passengerName,
    t.seatNumber,
    t.date AS bookingDate,
    t.age,
    f.flightId,
    f.source,
    f.destination,
    f.date AS flightDate,
    f.time AS flightTime,
    u.userId,
    u.username
FROM 
    Tickets t
JOIN 
    Flights f ON t.flightId = f.flightId
JOIN 
    Users u ON t.userId = u.userId
WHERE 
    t.ticketId = 'T001';