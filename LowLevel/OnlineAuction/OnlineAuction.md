Online Auction

# Requirements

The online auction system should allow users to register and log in to their accounts.
Users should be able to create new auction listings with details such as item name, description, starting price, and auction duration.
Users should be able to browse and search for auction listings based on various criteria (e.g., item name, category, price range).
Users should be able to place bids on active auction listings.
The system should automatically update the current highest bid and notify the bidders accordingly.
The auction should end when the specified duration is reached, and the highest bidder should be declared the winner.
The system should be extensible to accommodate future enhancements and new features.

# Entities


lot of the description is in the image

User Management is the singleton, which manages the users
AuctioHouse Listing manages the auctionListing , and the users creation , also store the reverseIndex for the search based on the cretieria, we can keep different maps as well.

AuctionListing uses a state pattern, which state has an accept function ,the NotStarted accept function may take subscribers to notify to users
Active state accepts takes in the bids
abandoned accepts takes in nothing, only notifies.
the closed accpet notifies the last biggest big and the last bid to pay up

similarly we have obeserve pattern for the notification for the users for each auction, based on whatever their preference is.


the concurrency is maintained by the auction house singleton, which maintains the access ot the final maps and the creation of user / auctions listing and bids
now for low level programming this can be done with mutexes and locks on the data structures and the general creation.