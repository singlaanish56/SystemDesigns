suggestions from the jordan's video that make a lot of sense

https://www.youtube.com/watch?v=5V6Lam8GZo4&list=PLjTveVh7FakJOoY6GPZGWHHl4shhDT8iV

1. hash to get the  to tiny url information, because the hashes would be evenly distributed
2. to deal with the hash collision, we assign the +1 hash to the url.
3. to deal with two people trying to write the same hash to the sytem, we preload the hashes into the database with the null vlues, so we can lock the row
and dont need the preidcate locks.
4. On the similar lines, we can use a multu-leader / leaderless writes, because the conflicsts resolution mechanisms are not perfect, Last write wins is not the correct scenarios in this case
5. We need single leader, and need to partition the db based on the short links or the hashes
6. Use of consistenet hashing to keep track and minimize the reshuffling
7. because its a read heaby operation , to use an on disk index, we use the b-tree index because the reads are much quicker, writes are slower (because straight to disk)
8. So in this case a  db with single leader, partitions, on disk btree based is the MySQL
9. Hot reads for the cache can he handles by partioning the cache similar to db based on the short urls, so that all the caches dont recompute the result
10. Cache is filled through write around, and is invalidated though the LRU policy.
11. For analytics, what makes sense is stream solution, that takes the read event and does a batch jonb to compute the click and push back to the db
12. in this case kafka works best as the write only log, which can be partioned similar to db for the given short urls, hence a particular partition handles limited urls
13. Same with the spark streaming engine, which computes the batch jobs and trigger a table update with teh click values 
14. Expiration or invalidation of the tiny urls can just be a batch job