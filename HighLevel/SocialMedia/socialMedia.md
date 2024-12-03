Social Media

# Requirements

## Functional Requirements

1. User login / logout / signup
2. User can post -> photos, videos, text
3. User can comment / quote / retweet /like other users's post
4. nested comments / quotes
5. User can follow other users
6. The feed generation / updates


## Non Functional Requirements

1. high availablity
2. low latency
3. scalibility
4. durability

## Capacity Requirements

### Assumptions

1. 500 million total users
2. 10 million uploads/ post a day
3. 2 million daily active users
4. 10% are media uploads

### Rough Estimation

Requests/Second 10*10^6 / 86400 = 120/sec

text tweeet storage 200bytes * 120/sec 24000 bytes/sec 24KB/sec 24*60*60*24*365=0.75 * 10^9 KB = 1TB/year
media storage average = 400Kb * 12/sec = 4800KB/sec 4800*3600*24*365 = 0.15 * 10^12 KB/year = 0.1 PB/year

bandwidth/sec =24kb/sec + 4800KB/sec = 5MB/sec

# Core Entities

User
User Actions -> post, comments, like retweet
Post
Feed Generation


# API Design

POST /login , /signup , /signout routes

//can be different as well here but for simplicity 
POST users/{userid}/post
{
    content
    presinged url
    parentid
    post type -> like, original post, comment, retweet
}

GET users/{userid}/allPosts, allMedia, userProfile (load all tweet information and stuff)

POST users/{userid}/generateFeed
{
    feedType -> only followed, a group, you may like
}


# High Level Design

User generation service is simple, can use sql database, there isnt much to it
for the user posts there can be a cache of the latest post per user id, to quickly add to the feed of other, cache can be partioned accordingly by the id

ElasticSearch agent to search through the tweets of a person

For Post, specifically media we have an object storage, which can cached according to a a follower count, at certain follower its certain the image would be rendered into other people feeds
similarly a cache for the hot posts or potentionally hot posts , made by bug account,  can be cached by the topics, maybe something like a reverse search for a list of topics

For comments / quotes we use the parent id concept, post will content the list of comments or quotes on the first level,
as you click on load more, or keeping going down the rabbit load, eventualy comment acts as parent id and loads the immediate comments on this

this way you dont have to display everything, going back just loads the parentsid comments

Now for the feed generartion, we can use the cache we maintain for the posts, based on the hot topics and generate the feeds for a user id, depends on which type of feed
they want,
strategy can configured based on the topic tweet a particular user likes, and we can store that topic when a user interacts with the tweet.
plus we can maintain a  seperate cache of reverse index with topics and verified users and hot tweets,  to push on the relevant users.

as previously stated comments is also essentially posts (just displayed differently on UI), we can also display tweets where somebody you follow has commented on quoted on, or even liked on.



# Deep Dive

in this case there is lot of distributed / multiple backend across the system. While something like a post cache / comments cache can be allowed to be stale for a while
or the information on a post, regarding the comments or like can be evetualy consistent,

Feeds showing same or even similar tweets within lets says last few minutes, or just appending newer posts on top. If a user scroll down too long will see the same posts seen already
do we can maintain a cutoff from UI on how many tweets to display

But otherwise i dont see huge issues other than regular distributed systems workaround of replicas, partitions, sharding, and caches for various retrieval methods that exist in the system.



