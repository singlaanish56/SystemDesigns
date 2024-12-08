Stack Overflow

# Requirements
1. Users can post questions, answer questions, and comment on questions and answers.
2. Users can vote on questions and answers.
3. Questions should have tags associated with them.
4. Users can search for questions based on keywords, tags, or user profiles.
5. The system should assign reputation score to users based on their activity and the quality of their contributions.
6. The system should handle concurrent access and ensure data consistency.


# Entities

1. User -> userid, username, name , email etc, list->questions, list->answer,  upvoted, downvotes, reputation score
2. Group -> group topic, vector<Users> mods, list->questions, sorted Index order BasedOn Questions popularity
3. Question/ Answer is of the same Post,  postTopic, postBody, postid, postparentid, userposted, upvoted, tags, downvotes, list->immediate answers


# Classes

1. Stackoverflow Class -> Works with all the singleton manager as the entry point for requests
2. Reputation Manager , calculated reputations based on strategies, is called by the PostManager, whenever there is an activity on the questions / answer. This Manager takes in account the reputationStrategy Set By the Group to assign a point to the User. Which then is assigned through the User Manager
The reputation strategies can be provided by us and configured by the groups creates . moderaters. This strategy is used by the reputation manager to ccalculate the required score for a post (question / answer) as part of that group
3. SearchManager, top level search agent, which maybe goes throught he given group/ matches the text, can hold the index and reverse index for the group and text search, whenever a top level question or an answer is added to  it 
has various api, which it suses the entity managers to do its tasks, it has verious api when the user is specifically search by tags /posts / groups etc
4. Post Manager , other giving CRUD operations to interact with the POSt entity, this also houses the map<groups, posts> as well as reverse index, on words -> list of posts. which helps for the searchManager to quickly perform tasks
5. User Manager, performa the authentication routes, signin / logout / signin / other CRUD operations which may be required to interact with the Use entity
6. Group Manager, apart from CRUD operation on Groups and maintaining a list of groups, this also holds a list of rules, which we can configure a group can have, age limite, reputation limit, upvote limite, account age limit etc.


while questions can be without a parentid, answers have a parentid=>questionid, similarly comments on the answer will have the parentid = top answer
not really sure we need a different vote class, we can upvote/downvote class but doesnt server any clarity as such over the others



# Suggestions 

1. Notification Manager that handles the notifs, strategies configurable by each user  for a post/ question
2. POstManager to hold the observer list to everyone who allows to subscribe
3. Vote Class to handle the vote as well any exceptions during the concurrent access
