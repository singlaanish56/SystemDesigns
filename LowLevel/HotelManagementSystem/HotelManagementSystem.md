Hotel Management System

# Requirements
The hotel management system should allow guests to book rooms, check-in, and check-out.
The system should manage different types of rooms, such as single, double, deluxe, and suite.
The system should handle room availability and reservation status.
The system should allow the hotel staff to manage guest information, room assignments, and billing.
The system shoud support multiple payment methods, such as cash, credit card, and online payment.
The system should handle concurrent bookings and ensure data consistency.
The system should provide reporting and analytics features for hotel management.
The system should be scalable and handle a large number of rooms and guests.


# Entities


1. Reservation.

Basic entity, which has the reservationId , period -> startDate/endDate, list<ProductUse>, reservationstatus, bookingStatus,  userProfiles, list<FormOfPayments>, taxes, charges

everything else is self explainable bu the reservationStatus can be booked, modify, cancel
booking status can be confirmed or pending (pending is useful when the stays are booked free online, hotel can overbook for these days)

2. ProductUse

Basic entity to track the productType, the quantity, startDate/EndDate
the productType is an ENUM, its could be SINGL, DOUBLE, DELUXE, which denoted a room
it can also be non Room Items, like towel/ swimming pool or a club etc (everything but the room) because essentially these things do also have a inventory information/ there is a limit to what can be used, and is essential for the calculation later on
the startDate, endDate can donate the usage days, you can have two room but for different days, this can also handle the upgrades, eg the booking was for 21-23, and you had booked the double room, didnt like it so updgraded to deluxe for 22-23
so the list of productUse in the Reservation would have two productUses, single 21-22, deluxe 22-23

3. Form Of Payments,

another basic entity would handle the PaymentInfo, (tokenized), list<deposits>, list<refund>, list<guarantee>

 paymentInfo tracks the payment methods used until now by the customer, remember the customer can pay in multiple ways or parts. Online while booking but by credit card when checkout

the list of deposists /refund would be append only in the system, this can be used to track the money deposited by the customer ,while booking. refund would track any refunds.
appends only logs, because we are essentially storing the txn infomration. for eg if the customer did double payment, we can undo that with adding a refund item. But not delete the deposit item. This would help us track (how many times deposit was recoignized by the system).

Guarantee on the other hand is something when the customer gives the creditCard to be charged upon / its not actually charged until the end of the booking but the amount is also not deposited yet. Or at the end of the bookinf the customer can do a cash payment.

Strategy Pattern to handle the scenarios with the different type of payments
credit card are tokenized , we can use an external credit card tokemizer, which helps converts between the network and storage tokens. Hence comply with the PCI-DSS

4. PaymentInfo

Basic entity, PaymentTypeEnum ( credit card, cash , online), std:optional<CreditCardInfo>. amount

based on type enum we can check whether the creditcard info is required or not.



5. ReservationManagement

list<Reservation>, map<reservationId,Reservation> search

this is a singleton, that handles the CRUD + Search operations on the reservation, so makes the apis public.
this is also helps validate the incoming information (input /output validation).

this also does the business logic on the incoming information, missing data / checkIn / checkOut data mismatch, plus interacts with other singletons -> Inventory and Pricing to get the required and fill the reservation.

the concurency has to be handled on a particular reservationId, so we can do is uses the mutexes and locks to lock list before ( this is a low level design , otherwise we will get the database level lock on the item)

6. InventoryManagement

tuple stayInfo = <totalAvailableInventory, deductedInventory , overBokedInventory>
map<productType,map<date,stayInfo>> poroductTyeAvailability


map<reservationId, map<productType,map<date,{stayInfo, stayType}>>> reservationInfo


the first map stores the availabe inventory information per productType / for every date

the second map stored the information per reserationId, because it can happen that the previous counters were in pending, so inventory need that info nextTime when the reservation is confirmed, add back to overBookedInventory, deduct from the confirmed ones.
This also helps inventory verify whether a request is valid you cant reconfirmed any already confirmed reservation, and prevent double booking

this helps with the inventoryManagement, can store the exisitng inventoru for the given roomTypes, 
provides two set of api.

CRUD + Search to actually add the quantity of inventory available in the system. (used by the hotel).
Search can be used to get  the information about the days / or per productType.

second set of the API is open to the reservation singleton, to book the inventory
BookStay() -> book a confirmed stay for the days, productType, quantity. this will return an error if the inventory is not found
ReleaseStay() -> similarly to add a stay back into the pool for any modification
HoldStay() -> used for the pending reservations, allows inventory to overbook subject to confirmation.


again the concurrency and double booking can be prevented by obtaining a lock on the maps, for both reading an updating the information(in real life, it would be a database lock)

7. Pricing Management.

map<reservationId, list<reservation>>
just maitains the previously image.


this can do the pricing calculation based on the productType / quantity / dates. and return the prices according. this can also stores the information regarding the reservation.
which helps with the calculation, if its an updgrade should cost more than booking earlier.
Can helps provide isolated APIs for the hotel to update the pricing realtime, seeing the change in demand. Upcoming holiday seasons.

this is called by the reservation management singleton, after it calls the Inventory Management to confirm the availabilty of the stays.

8. HotelManagementSystem.

the central singleton that manages the other singletons in the system, it helps with teh booking through ReservationManagement.
access to the  InventoryManagement / Pricing Management, internal API for the hotel staffs used to do the updates


9. Error

errorCode, errorMessage, pointer. this is the central information and the error thrown by all the system involved
helps centralize the error handling.

