Pub Sub System


# Requirements

The Pub-Sub system should allow publishers to publish messages to specific topics.
Subscribers should be able to subscribe to topics of interest and receive messages published to those topics.
The system should support multiple publishers and subscribers.
Messages should be delivered to all subscribers of a topic in real-time.
The system should handle concurrent access and ensure thread safety.
The Pub-Sub system should be scalable and efficient in terms of message delivery.


# Entities and Classes

1. Publisher 

this will provide the basic stc for interaction with the actual PubSub System.
In c++ this can take the publish(topic, payload)  can also contain a configure() methid where to can set a configuration for a particular publisher
the publish method will use the settings from the config to send the message to topic / queue


2. Consumer/Subscriber <Interface>

this is the interface for the subscriber, similar to the publish. You can consume(), subscribe(topic), configure()

the subscribe function in this case cant be overriden, once provided the topic, will add to the list of topics.

this can also provide an OnRecived() func that can print out the message , whenever the topiv recieves a new message
this can be overridden by the children classes.


3. Topics

this is the base entoty class to create the topic, topicId, topicName, and other metaData about the topic
will provide the basic getter setters.


4. TopicsManager <Singleton>

a singleton as part of the bigger system, keeps track of the available topic and , allows the moderator / even publisher to create a topic and publish to.
This also help other flows in the system to verify the topic first, and error out adequately. Gives it a single responsibility for the topics (and their handling)



5. PubSubSystem <Singleton>

manages the map of topic-list<Subscribers>
then each topics can have a array of messages which can published to each subscriber.

Why the Array??

Array would maintain the casual relationahsip and the order of the message that are being pushed in the system.
this can also ensure that the subscriber can the know the last message that was recieved by it.

and then can consumer the next position in the array can be recieved as wel.


For concurrent access?

i am unsure why do we need concurrent access , once a  message is pushed onto the array, the last read node is known.
its just appending the new message, rather than overiting a value ( which could have been read by the subscriber)

the cleanup job to ensure if the message is read from, we remove the node from the array, we can lock the array and the flush the nodes


6.[optional] Message

message class can have a message id /
and the string for the content as well