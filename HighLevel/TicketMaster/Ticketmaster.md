Ticketmaster

# Requirements

## Functional Requirements

User able to view an event / book an event / search for an event
Artist able to regiater the event / concert
Able to handle the load when the event goes live
User able to see the booked event
Dynamic Pricing for events

## Non functional Requirements

prioritze searching  and viewwing events, able to handle rush load
able to handle ans scale for popular events
low latency / read heavy
protect user data
ensure no double booking 

## Capacity Estimation

let say 100k events at a give time
for average 10 section and each section has 500 seats
total no of eats 100k * 100 * 500 = 5 * 10^9
total space needed to store seat info around 20 bytes * 5 * 10^9 = 10^11 bytes = 100GB

# Core entities

User
Artist
Event
Venue/Stadium
Ticket
Booking


# API Design

USER

/signUP
/users/{userid}/logIn
/users/{userid}/logout
/users/{userid}/profile

Artist
/signUP
/artist/{artistId}/createEvent
/artist/{artistId}/event/{eventId}/deleteEvent/
/artist/{artistId}/event/{eventId}/updateEvent

Event

/event/search/keyword
/event/{eventId}

Booking

GET /booking/{bookingId}
POST /booking/events/{eventid}


# High Level Design

1. Search Event / View the event
can be straightforward the event / artist/ venue information can be stored as a reverseIndex is an elastic Search , where a change data capture(kafka) updates it for any new events
the events table can be single leade // replicated. because its read heavy the replicated dbs help with the read traffic

we can also maintain a cache based on LRU, because often when a popular artist's tickets goes live, people would have the same search keywords.
now as for the database it can be partioned by the regional venues together, this disberses the hot traffic, in case of super popular artist we can partition by the artist or an event, regional partition ensures we are scalable

the partition can be based on the number of stadium in the areas, for eg maybe middle america might not have a huge number of stadium so the each parition can cover a larger area

2. Book the event

this can be handled properly by an sql service, because it can support the transaction, now, the booking service can link with the external payment provider.
how can we ensure that there is no double booking, we can do something like a predictive locking, we can an entry in the booking table, for the artist, event, seatId, with other null fields and obtain a lock in to it.
so any other transaction trying to do the same cannot obtain the lock on it.

now based on the failure of an event booking, we want to return the available seats back into the pool after a time of 15minutes or so, we can do this with a event based queue, where each item along with seat information / event information, has the last transaction time as well, so the event can take the event if the given event time has passed can process it accordingly or append it back in the queue

the ticket can be booked using a distributed locked using redis with a ttl, which timeouts the ticket for whatever reason the booking doesnt go through


more covered with the image
