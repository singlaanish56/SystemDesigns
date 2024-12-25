Concert Ticketing

# Requirements

The concert ticket booking system should allow users to view available concerts and their seating arrangements.
Users should be able to search for concerts based on various criteria such as artist, venue, date, and time.
Users should be able to select seats and purchase tickets for a specific concert.
The system should handle concurrent booking requests to avoid double-booking of seats.
The system should ensure fair booking opportunities for all users.
The system should handle payment processing securely.
The system should generate booking confirmations and send them to users via email or SMS.
The system should provide a waiting list functionality for sold-out concerts.

# Entity

a lot of description is in the image, a lot of stuff so difficult to explain

We use the strategy pattern for the ticket pricing strategy per event, can be set by the ticket booker or the even creater
Singleton pattern for the ConcertTicketingSystem , UserManagement, PaymemtProcessor
StatePattern for the state of the seat , state of the booking
Oberserver Pattern for the User subscribed notification, can be multiple emails/ phone number, they wanna send the ticket to

for me a lot of cioncurreny in this case is handled by the database design,
ratelimiting and user tomeouts are handled by the high level design, frontend queues to handle the requests

apart from obviously locking the object memebers before upating them, which ideally is for the desing, in IRL you will call the database to do the job.
