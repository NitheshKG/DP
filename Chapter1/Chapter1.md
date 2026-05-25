# CHAPTER 1

## Scale From 0 to millions of users

- Lets start with the basics, We are having a webapp, user, db and cache
- User hits the url then web app process it and returns the response

### Flow 1:-

1. User hits the url from any of his device. ("https://nitheshkg.in")
2. It routes to the DNS where the DNS returns the IP Address
3. The IP address is inturn passed to the Web Server
4. The Web Server returns the HTML page back to the user


- Lets determine on the traffic source of the above flow, both comes from the web app and mobile app
- Web App: Server side logic (python, java, etc) + Client Side Language (HTML, JS)

### Flow 2:-

- Lets have a api request which fetches the user object based on the id 

API : GET /users/12 - Retrieves the user object for id = 12

{
    "id": 12,
    "name": "Nithesh",
    ...
}

### Database:-
- Since we are growing in the userbase list one server is not enough so we can have one for the web and one for the DB

- Which DB to use : 
    - RDBS (Tables; MySQL, PostgreSQL, OracleDB)
    - No SQL (CouchDB, Neo4j, Cassandra, Hbase, Dynamo DB) => These are grouped into 4 categories key-value stores, graph stores, column stores, document stores.

    - When to use the NoSQL DBs

        1. Super low latency
        2. unstructured data or no relational data
        3. only need to serialise and deserialise data (JSON, XML, YAML, etc)
        4. need to store massive amount of data

    - NoSQL DB Types:

        1. key-value stores (DynamoDB, Redis) :- Stores the data as key-value
            - Caching
            - Session Storage
            - Fast Lookups
            - Shopping carts

            Example :-
                {
                    "user1": "Nithesh",
                    "user2": "Rahul"
                }
            
        2. Document stores (mongoDB, CouchDB) :- Store data as JSON-like documents
            - Web Apps
            - APIs
            - Flexible Schemas
            - User Profiles

            Example :-
                {
                    "name": "Nithesh",
                    "skills": ["Python", "Flask"],
                    "experience": 5
                }
        
        3. Column stores (Cassandra, HBase) :- Instead of storing data row-by-row like SQL databases, they store data column-wise.
            - Big data
            - Analytics
            - Massive write operations
            - Time-Series data

            Example :-
                IDs   -> [1]
                Names -> [Nithesh]
                Ages  -> [25]

        4. Graph stores (Neo4j):-  Node(entities) & Edges(relationships)
            - Social networks
            - Recommendation Systems
            - Fraud Detection
            - Network Relationships



    | Type         | Stores Data As        | Best Use Case         |
    | ------------ | --------------------- | --------------------- |
    | Key-Value    | key → value           | Fast retrieval        |
    | Document     | JSON documents        | Flexible applications |
    | Column Store | Columns               | Big data & analytics  |
    | Graph        | Nodes + relationships | Connected data        |



### Vertical vs Horizontal Scaling :-

- Vertical Scaling (scale up) : process of adding more power(CPU, RAM, etc) to your servers
    - When to use: 
        1. Low Traffic
        2. Simplicity

    - Disadvantages:
        1. Hard limit -  we cannot add unlimited CPU and memory to a single server
        2. Failover & Redundency - If the server goes down the entire application goes down with it completely

- Horizontal Scaling (scale out): Process of adding more servers into the pool of resources
    - When to use:
        1. Large scale applications

    - Disadvantages:
        1. Complicates if the application doesn't require multi server setups


### Load Balancer :-

- In the previous designs the users are directly connected to the server, if the muliple users tries to access the website and if the web server's load limit reaches then the user will have their data served slow or fail to connect to the server. To handle this we utilise the **Load Balancer**

- It evenly distributes the incomming traffic among the web servers.

- As mentioned in the diagram: user connects the public ip directly to the load balancer and the private 
ip's are used for the security as from the load balancer it reacher the servers using the private ip's.

- Private ip is an IP address reachable only between the servers in the same network.

- If the server 1 goes offilne all the traffic will be routed to the server 2 (Horizontal scaling) this prevents the server to go offline

- We can add more servers to the web server pool once the website traffic grows rapidly

- Current design efficient for the web traffic but has only one DB so it does not support the failover and redundanc. Solution: DB replication 


### DB Replication :-

- DB replication - master/slave relationship between original(master) and the copies (slaves)

- DB replication advantages - Better performance, Reliablility, high availability

- If only one slave databse is available and it goes offline then the read will be temporarily directed to the master database until new db server replaces the old one

- if master goes down then the slave promoted to the master 


### Cache

- Temporary storage that stores the result of an expensive responses or frequently accessed data in memory.

- Flow : read-through cache

    1. Web server once getting a request checks if the cache has the available response.
    2. If it has it sends them flow ends if not
    3. Web server process the request stores to the cache and send back to the client


### Caching strategies :-

| Strategy                       | How It Works                                                  | Best Use Case                    | Advantages                                   | Disadvantages                               | Important Notes                |
| ------------------------------ | ------------------------------------------------------------- | -------------------------------- | -------------------------------------------- | ------------------------------------------- | ------------------------------ |
| **Cache-Aside (Lazy Loading)** | App checks cache first → on miss fetches DB and updates cache | Read-heavy systems               | Simple, cost-efficient, only hot data cached | First request slow, stale data risk         | Most commonly used strategy    |
| **Write-Through**              | Write to cache and DB together                                | Strong consistency systems       | Cache always fresh, faster reads             | Slower writes, unnecessary caching possible | Good for user/session data     |
| **Write-Back / Write-Behind**  | Write to cache first, DB updated asynchronously               | Write-heavy systems              | Very fast writes, reduced DB load            | Data loss risk if cache crashes             | Eventual consistency           |
| **Read-Through**               | Cache itself loads data from DB on miss                       | Centralized caching systems      | Cleaner application code                     | Complex cache layer                         | App interacts only with cache  |
| **Refresh-Ahead**              | Cache refreshes before TTL expires                            | Frequently accessed hot data     | Very low latency                             | Extra DB usage                              | Good for dashboards/stock data |
| **TTL-Based Caching**          | Cache expires automatically after configured time             | Slightly stale data acceptable   | Easy to implement                            | TTL tuning difficult                        | Common with Redis              |
| **Distributed Cache**          | Shared cache across multiple servers                          | Scaled microservices             | Shared state, scalable                       | Network overhead                            | Uses tools like Redis          |
| **Local/In-Memory Cache**      | Cache stored in app memory                                    | Single-server ultra-fast systems | Extremely fast                               | Not shared across servers                   | Example: Python dict           |
| **CDN Cache**                  | Static files cached near users globally                       | Images, CSS, videos              | Global low latency                           | Invalidation complexity                     | Uses providers like Cloudflare |
