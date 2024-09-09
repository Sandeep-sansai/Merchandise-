[Role] -- (1:M) -- [Credentials] -- (1:1) -- [UserInfo]
   |                                  |
   |                                  |
[City] -- (M:1) -- [Flight] -- (1:M) -- [FlightInfo] -- (M:1) -- [Booking] -- (1:1) -- [Canceled]
                                            |                                |
                                            |                                |
                                          [Seat]                         [Payment]




CREATE TABLE Role (
    role_id INT AUTO_INCREMENT PRIMARY KEY,
    role_name VARCHAR(50) NOT NULL
);

CREATE TABLE Credentials (
    credential_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    role_id INT,
    FOREIGN KEY (role_id) REFERENCES Role(role_id)
);

CREATE TABLE UserInfo (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    credential_id INT,
    name VARCHAR(100) NOT NULL,
    age INT NOT NULL,
    email VARCHAR(100),
    phone VARCHAR(20),
    FOREIGN KEY (credential_id) REFERENCES Credentials(credential_id)
);

CREATE TABLE City (
    city_id INT AUTO_INCREMENT PRIMARY KEY,
    city_name VARCHAR(100) NOT NULL
);

CREATE TABLE Flight (
    flight_id INT AUTO_INCREMENT PRIMARY KEY,
    source_city_id INT,
    destination_city_id INT,
    FOREIGN KEY (source_city_id) REFERENCES City(city_id),
    FOREIGN KEY (destination_city_id) REFERENCES City(city_id)
);

CREATE TABLE FlightInfo (
    flight_info_id INT AUTO_INCREMENT PRIMARY KEY,
    flight_id INT,
    date DATE NOT NULL,
    time TIME NOT NULL,
    total_seats INT NOT NULL,
    available_seats INT NOT NULL,
    FOREIGN KEY (flight_id) REFERENCES Flight(flight_id)
);

CREATE TABLE Status (
    status_id INT AUTO_INCREMENT PRIMARY KEY,
    status_name VARCHAR(50) NOT NULL
);

CREATE TABLE Booking (
    booking_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    flight_info_id INT,
    status_id INT,
    booking_date DATE,
    booking_time TIME,
    total_passengers INT,
    FOREIGN KEY (user_id) REFERENCES UserInfo(user_id),
    FOREIGN KEY (flight_info_id) REFERENCES FlightInfo(flight_info_id),
    FOREIGN KEY (status_id) REFERENCES Status(status_id)
);

CREATE TABLE Canceled (
    canceled_id INT AUTO_INCREMENT PRIMARY KEY,
    booking_id INT,
    canceled_date DATE,
    canceled_time TIME,
    FOREIGN KEY (booking_id) REFERENCES Booking(booking_id)
);

CREATE TABLE Seat (
    seat_id INT AUTO_INCREMENT PRIMARY KEY,
    flight_info_id INT,
    seat_number INT NOT NULL,
    is_booked BOOLEAN NOT NULL,
    FOREIGN KEY (flight_info_id) REFERENCES FlightInfo(flight_info_id)
);

CREATE TABLE Payment (
    payment_id INT AUTO_INCREMENT PRIMARY KEY,
    booking_id INT,
    amount DECIMAL(10, 2) NOT NULL,
    payment_date DATE,
    payment_method VARCHAR(50),
    FOREIGN KEY (booking_id) REFERENCES Booking(booking_id)
);


INSERT INTO Role (role_name) VALUES ('Admin'), ('User');

INSERT INTO City (city_name) VALUES ('New York'), ('Los Angeles'), ('Chicago'), ('Houston'), ('Miami');

INSERT INTO Credentials (username, password, role_id) VALUES 
('admin1', 'adminpass', 1), 
('user1', 'userpass1', 2), 
('user2', 'userpass2', 2), 
('user3', 'userpass3', 2), 
('user4', 'userpass4', 2);

INSERT INTO UserInfo (credential_id, name, age, email, phone) VALUES 
(2, 'John Doe', 30, 'john@example.com', '1234567890'),
(3, 'Jane Smith', 28, 'jane@example.com', '2345678901'),
(4, 'Mark Johnson', 35, 'mark@example.com', '3456789012'),
(5, 'Emma Williams', 25, 'emma@example.com', '4567890123');

INSERT INTO Flight (source_city_id, destination_city_id) VALUES 
(1, 2), 
(2, 3), 
(3, 4), 
(4, 5), 
(5, 1);

INSERT INTO FlightInfo (flight_id, date, time, total_seats, available_seats) VALUES 
(1, '2024-09-01', '08:00:00', 100, 95), 
(2, '2024-09-02', '10:00:00', 150, 145), 
(3, '2024-09-03', '12:00:00', 200, 198), 
(4, '2024-09-04', '14:00:00', 180, 175), 
(5, '2024-09-05', '16:00:00', 120, 118);

INSERT INTO Status (status_name) VALUES ('Booked'), ('Canceled');

INSERT INTO Booking (user_id, flight_info_id, status_id, booking_date, booking_time, total_passengers) VALUES 
(1, 1, 1, '2024-08-25', '09:00:00', 2), 
(2, 2, 1, '2024-08-26', '11:00:00', 1), 
(3, 3, 2, '2024-08-27', '13:00:00', 3), 
(4, 4, 1, '2024-08-28', '15:00:00', 4), 
(1, 5, 1, '2024-08-29', '17:00:00', 1);