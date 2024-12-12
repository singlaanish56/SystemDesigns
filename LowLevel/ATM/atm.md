Automatic Teller Machine


# Requirements

1. The ATM system should support basic operations such as balance inquiry, cash withdrawal, and cash deposit.
2. Users should be able to authenticate themselves using a card and a PIN (Personal Identification Number).
3. The system should interact with a bank's backend system to validate user accounts and perform transactions.
4. The ATM should have a cash dispenser to dispense cash to users.
5. The system should handle concurrent access and ensure data consistency.
6. The ATM should have a user-friendly interface for users to interact with.


# Photo


# Not so obvious things

1 .atm has a state for each part of the txns
2. atm interacts with the bank server, to actually perform the transaction, now in interview we can add lock and mutexes on the datastructure we will use to emulate the working of the bankServer
3. In real life scenario the concurrent access will be gaurded by the ACID principles and the lock on the databse itself both on the to and fro account and otherwise
4. Bank server also interacts with a creditCardtokenizer, to get the storage token or network token for the card ( we cant store cc in clear(even if encrypted)). this Tokenizer also helps us verify the creditCard using luhn's Algo
5. Account also holds the list of observers it like to have, maybe for account you want all possible notification, email, number. Or if you want switch off all notification.
6, This oberserver list can be used by the receipt deispenser at the atm as well, to deternmine whether or not ot inform of the transaction for both the account ids ( to and from)
7. All these transaction is serialized , and any problem at a stage is undoed and rollbacked(the concept of transactions and exclusive locks)