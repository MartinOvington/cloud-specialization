## Cloud Computing Concepts Part 2

The second course continues where the first course left off, with more teaching of the internals of the cloud - concepts, techniques and industry systems.

### Week 1 Leader Election, Mutual Exclusion

Leader election is necessary in distributed systems for various reasons. From the clients perspective, they usually only wish to communicate with one of the nodes in the system, which must then act as the coordinator for the rest of the system. Another example that we have seen is where a global sequence number must be maintained, so we need a designated server for performing this function.

Within the group of nodes, there must be agreement among them as to which node is the leader. For this consensus problem, we need a leader election algorithm to decide the leader. Important considerations for the algorithm is what happens if the leader fails, who can call for an election and how we maintain safety (maximum one, best leader) and liveness (eventually a leader is elected).

The ring leader election algorithm allows any node in the ring to call an election. As the message passes around the ring, nodes with the better attributes than the previous best modifies that message with its own details and then passes it on around the ring. When a node receives its own details, it knows it is the best would-be leader and then passes on a message to inform the other nodes that it is now the leader. Within this system we require some method of failure detection such that a predecessor or successor to a failed leader can initiate a new election.

Paxos-like protocols can be used for leader election, which is what is done within Google Chubby (locking system) and Apache Zookeeper. Within Google Chubby, leaders are elected based on leaders reaching a quorum of votes. After elections, a leader receives a master lease, which guarantees that another election is not run for a set period of time. In Zookeeper, each server creates a sequence number for itself and the highest sequence number in the election messages determines the leader. It uses a two-phase commit for the leader selection which ensures that safety is maintained.

The Bully algorithm for leader election works by all processes knowing each others process IDs. When the current coordinator is detected by a given process, processes which know they are the next highest ID sends Coordinator messages to all lower IDs, which completes the election. Other processes initiate an election by sending an election message to processes that have higher IDs than itself. When a process gets an election message, it sends election messages to processes with higher IDs than itself. By detecting failures by timeouts, these election messages will eventually allow the highest ID process to detect the failure of the coordinator and send out Coordinator messages to elect itself.

Mutual exclusion is important in distributed systems as it is common for there to be multiple processes trying to read/write to the same shared resource. The problem can be broken down into the idea that within our processes we can have **critical sections** within our code that should be accessed by at most one process at a time. Within a single OS we can rely on all processes being with the same machine, which allows us to use semaphores, mutexes and condition variables. These do not work on distributed systems as they really on shared variables that we do not have in this context. Our solution for this problem requires that we have safety (max one process in critical section at a time), liveness (every request to access critical section eventually granted) and we may also desire some level of fairness by having requests be granted in FIFO ordering.

One solution to mutual exclusion in distributed systems is the central solution. We elect a leader which is responsible for maintaining a queue of requests and a special access token that allows access to the critical section. Other processes wanting to enter the critical section requests the token from the master and when it exits the critical section it must release the token back to the master.

The Ricart-Agrawala algorithm is a classical distributed systems algorithms from 1981. It allows for faster access to the critical section compared to the ring-based token passing approach. Processes multicasts requests containing lamport timestamps and then waits for all replies before entering the critical section. All processes queues outgoing replies based on the lamport timestamps and in that way fairness is maintained based on the causality inferred from the lamport timestamps.

Maekawa's algorithm is another attempt to solve the mutual exclusion problem. Access to the critical section requires replies from _some_ of the other processes, a similar to requiring a quorum. Specifically, each process is associated with a _voting set_ that it requires votes from, the optimal size of which is around the square root of the total number of nodes. It uses the intersecting voting sets to ensure mutual exclusion.

### Week 2 Concurrency and Replication Control

Remote Procedure Calls (RPCs) are an abstraction that allows a process to call a function in another process, an important concept in distributed systems. We can have different semantics for the execution of RPCs, which is needed due to the possibility of messages being dropped or processes failing. These include At-Most-Once, At-Least-Once and Maybe/Best-Effort calling semantics. **Idempotent** operations are those that can be repeated multiples times without side effects, which work well with At-Least-Once semantics. RPCs can be implemented using a stack consisting of Caller <-> Client stub <-> Caller comms module <-> Callee comms module <-> Dispatcher <-> Server stub <-> Callee. Marshalling may be required for communication between processes due to difference in architectures e.g. Big endian and Little endian. It involves using middleware that has a common data representation and converting to this format is called marshalling.

Transactions are a series of operations executed by a client that results in an RPC to a server. Transactions must either execute fully and commit or abort and have no lasting effect on the server. They must have the properties of atomicity (all or nothing principle) and isolation such that they indivisible from point of view of other transactions. We can summarise the properties of transactions using the **ACID** acronym: Atomicity, Consistency (before and after transactions), Isolation, Durability (persistence). Interleaving of transactions operating on the same data can result in lost updates (writes) and inconsistent retrieval (reads). These problems could be avoided by executing all transactions serially, but this hurts performance, so instead we wish to maximise concurrency. We use the notion of serial equivalence of interleavings of transactions if the end result is indistinguishable between interleavings. The following pairs of operations are conflicting in that their order of execution matters: read(x) & write(x), write(x) & read(x), write(x) & write(x). The following are not conflicting: read(x) & read(x), read/write(x) & read/write(y). Before committing a transaction T we check for serially equivalence with other transactions and then we can decide to abort/rollback T if serial equivalence was violated.

Pessimistic concurrency control prevents objects from accessing the same object e.g. by locking. Under this system we can allow objects to be in read mode where multiple transactions can use it or a write lock, which is exclusive. Deadlocks occur when we have two processes trying acquire two or more locks in a way that is cyclical and therefore never resolves. We can combat this using lock timeouts, but this can be inefficient. Deadlock detection can also be used to break deadlocks. However, the best way to combat deadlock is to write the system to avoid deadlocks occurring in the first place.

Optimistic concurrency control attempts to increase concurrency over pessimistic control.
One approach is to have no restrictions on write and reads, but to check for serial equivalence at the time of the commit. If serial equivalence is violated, the updates are rolled back - reads must also be aborted as they may have read 'dirty' data. Multi-version concurrency control is another approach and uses a tentative per-transaction version of objects and a committed version. Tentative versions have a timestamp and on reads and writes we find the 'correct' tentative version to read or write from. In eventual consistency systems such as Cassandra and DynamoDB, Last-Write-Wins (LWW) is used.

Replication control deals with the handling of operations when there are objects stored at multiple servers with or without replication. Fault-tolerance is achieved by increasing the number of copies of an object we have. Load-balancing is important considerations when designing system and spreading read/writes across our replicas can help to spread the load across servers. **Availability** is related to the failure probability, with more replicas we have higher availability as there is a lower probability that all replicas have failed. Two challenges arise from having replicas - there should be replication transparency (to clients it looks like only one copy) and there needs to be some level of replication consistency. A two-phase commit is used to maintain atomicity of transactions across servers holding replicas for a given transaction. The coordinator keeps logs that can be used to replay transactions in the case that a replica server crashes before replying yes/no to the transaction request. Paxos can be used to decide whether to commit a transaction and order updates.

### Week 3 Stream Processing, Distributed Graph Processing, Structure of Networks, Scheduling

Stream processing is used when we can large amounts of incoming data and we require real-time views of data e.g. twitter trends, website statistics, database intrusion detection. To achieve this we need a low latency and high-throughput system. Batch processing is not suitable for this task as we not have notions of partial results in this system and we must wait for entire computation of entire dataset to complete. Apache Storm is a widely used stream processing system. It consists of tuples streams, spouts, bolts and topologies. A spout is a source of streams, often reading from crawler or database and a bolt processes these streams into output streams. We combine spouts and bolts to from a topology. The topology can have cycles and often terminate in a single stream that forms the result. The architecture of a storm cluster is split into a master node running Nimbus, worker nodes running Supervisors and Zookeeper that coordinates the Nimbus and Supervisor communication.

Distributed graph processing can be performed using graph processing systems such as Google's Pregel system. A graph consists of a network of vertices (nodes) and edges that connect nodes. Examples of graphs are the internet graph of routers/switches, WWW graph of URL links and social graphs such as Facebook. Graph processing aims to summarise and derive properties or statistics from the graph e.g. finding shortest paths between pairs of vertices. The challenge of graph processing is that graphs can be very large, often too large to store/process on a single server. The iterative nature of graph processing makes frameworks sign as Hadoop MapReduce a poor choice. Pregel works on a cluster of servers and uses a master/work model. The master maintains a list of workers, monitors works and restarts them on failures and provides monitoring tools. The workers are responsible for processing vertices and communicating with other works. Persistent date is stored in a distributed fashion on Google File System (GFS) or BigTable and temporary data is stored on local disk. Failure-tolerance is achieved by checkpointing, failure detection and recovery.

Networks have different types of complexity. Structural complexity can be due to the sheer number of vertices and edges. Evolution can occur whereby the network changes over time e.g. people making new friends on Facebook. There can be a large amount of diversity in networks, some vertices may have many edges, some few. The vertices may represent entities that have different properties e.g. servers with different specifications. We can also have emergent phenomena where the combined simple behaviour of multiple vertices lead to complex behaviours as a result of interactions with each other. Many networks can be classified as 'small world networks' whereby any given pairs of vertices are connected by a relatively short path, even if the network is large. Clustering coefficient measures something akin to transitivity - if A-B edge exists and A-C edge exists what is the probability of C-B edge existing. Small world networks have a higher cluster coefficient and random networks have lower cluster coefficient. Small world networks have the property that if you remove random vertices, connectivity remains high, but removing a few high-degree vertices is more likely to reduce connectivity.

Schedulers should aim to achieve good throughput or response time and a high utilisation of resources. Different scheduling algorithms exist for single-processor scheduling - FIFO, Shortest Task First (STF), Round-Robin. FIFO and STF are preferable for batch applications and round-robin works well with systems that need high interactivity. Hadoop YARN has two popular schedulers - Capacity Scheduler and Hadoop Fair Scheduler. The Hadoop Capacity Scheduler uses multiple FIFO queues and guarantees each queue a set proportion of the cluster and puts higher priority tasks in queues that have more resources allocated to it. The Hadoop Fair Scheduler gives each job an equal share of resources. It divides the cluster into pools and divides resources equally between pools and prevents the cluster from being hogged by one user/job. Dominant-Resource Fair Scheduling takes into account the different proportions of the total system's resources that a task requires and attempts to fairly distribute resources according to these requirements.

### Week 4 Distributed File Systems, Distributed Shared Memory, Sensor Networks

File systems are presented to the user as a high-level abstractions that presents it as containing files and directories, which prevent users from having to think about disk blocks and memory blocks. Typical files contain a header followed by blocks containing the actual file contents. The header contains details such as timestamps, file type, ownership, access control list and reference count. Directories are simply a special case of a file and contains details of the files it contains. Distributed File Systems (DFS) stores files on a server and the client performs operations on files using RPCs. From our DFS we desire transparency for the client, functionality to support multiple concurrent client usage and replication so that we achieve fault tolerance. In DFSs we may need to authenticate the user based on some component of their messages and as they are often multi-user systems we may need some kind of authorisation e.g. through access control lists.

The Network File System (NFS) was developed by Sun Microsystems in 1980s and is widely used today. Clients and servers have virtual file systems that allows them to uses a unified API for the underlying Unix File system and NFS system. The client's NFS system communicates with the NFS system in the server, allowing clients to interact with files on the local disk of the server. Clients can mount remote files to their local directories and use the normal Unix file descriptors to work with the files. Several server optimisations exist in NFS including server caching and a choice between using delayed writes (temporarily write to memory) or write-throughs (immediate write to disk). Clients maintain caches of recently accessed blocks and a notion of **freshness** and time-modified to decide when the block needs to be refetched.

The Andrew File System (AFS) is another DFS and is mainly used in some Universities. It differs from NFS in that it works on the level of files rather than file-blocks. The design decision is based on usage patterns that show that most files are accessed by a single user and most files are small. Modern RAM capacity is large enough to cache such files and files are often consumed sequentially so having the entire file takes advantage of this locality. Reads and writes are done optimistically on local copies of files and writes are propagated to the server when the files are closed. The system uses _callback promises_ so that clients that are using a file are given notice when the server detects an update to its local copy.

Distributed Shared Memory (DSM) is the notion of processes having shared memory pages over a network. In order to achieve these we maintain a cache at each process, which processes read/write to first and we have page hits and page faults much like a local memory system. In DSM we have an owner, which is the process with the latest version of the page. Pages are in read or write states. Write state are exclusive to a single process so are mutually exclusive. Processes writing to a page multicast to other processes to invalidate their cached copies and reads are also done using multicasts if the process has no local copy. There are downsides to this approach. When two processes are both writing it can cause flip-flopping behaviour that results in a lot of network traffic. There is a need to specify page size, which may incur more overhead than is necessary for smaller read/writes, so it needs to optimised for the specific use case. We can have notions of weaker or stronger consistency within DSMs depending on how it is implemented, with strong levels of consistency resulting in slower speed.

Sensor networks have become more popular as small, higher performance (Moore's Law), low-power-usage sensors have appeared in the market. These sensors collect an enormous amount of information and sensor technology helps to filter and process this information. The sensor nodes have some way of collecting information from the environment, a microprocessor, some sort of communication link and a power source like a battery. Types of sensors include acceleration, vibration, sound, CO/CO2, pathogen detectors etc. The transmission medium is typically radio frequency, but optical transmission may be used if the available power is lower, with the downside the line of sight is needed. The sensor nodes need a small, efficient OS. The TinyOS is one such OS, which has event-driven execution and a modular, decoupled structure, which is important as we do not want unnecessary overhead using power. Often sensors will work in bursts, so that they only use power when they have to and transmit during limited windows. They will sometimes aggregate and process data to save on the power needed to transmit it.

### Week 5 Security and Datacentre Outage Studies

As distributed systems must be designed to take into account the security. Types of security threats include leakage (unauthorised access), tampering (unauthorised modification) and vandalism (interference with normal service). Common attacks including eavesdropping, masquerading, message tampering, replay attacks and denial of service (DoS). These threats are addressed by ensuring our systems protect of confidentiality, integrity and availability. **Policies** refers to the properties that we wish our security systems to have. **Mechanisms** refers to how these policies are achieved. The mechanisms usually break down into authentication, authorisation and auditing. Before designing out security system we define the attacker model, which specified the predicted capabilities of a realistic attack. Then we design and implement our security mechanisms, analyse the effectiveness of the system and measure the performance impact under normal operation.

In cryptography, a key is a sequence of bytes assigned to a user that be used to lock (encrypt) and unlock (decrypt) messages by applying the key to it. There are two main types of cryptographic systems - symmetric and asymmetric. In symmetric cryptography we must have some way of securely sharing the keys that will be used to encrypt and decrypt messages, which may be challenging as the method is vulnerable to interception of the key, so we must have some secure method of delivering the key. Conversely, asymmetric cryptography uses a **private** and **public keys**, such that messages can be encrypted with one and decrypted with the other. The public-private key technique is used for securely sending messages by encrypting the message with the recipients public key, allowing the message to be decrypted by the recipient by using their own private key. It can also be used for implementing digital signatures by encrypting items with your private key, allowing others to verify your identify by a successful decryption using the associated public key. The downside of the public/private key method is that it is more computationally expensive.

Studying datacentre outages can be useful in identifying ways in which we can improve the design of systems and procedures to prevent and better deal with future outages. Datacentre outages can be caused by power problems, over-heating, fire, DoS attacks, but by far the most common (70%) is human error. Examples of human errors include a system operator mistakenly deleting a database and its backup, a maintenance contractor shutting down an air traffic control system and a technician pulling the wrong controller and crashing a redundant storage area network that had incidentally already suffered a controller failure. Another more secure example is a technician failure to disable a fire alarm actuator prior to testing and damaging disks through the noise from the alarm.

An AWS outage occurred in 2011. Amazon published a post-mortem analysis and took steps to prevent such an outage occurring again in the future. Several companies using AWS EC2 went down, which included Reddit and Foursquare. The outage affected the Elastic Block Storage (EBS) that EC2 instances access and they are replicated for durability. The system has a secondary network that is used for overflow, but this network has a lower capacity than the primary network. The outage occurred during a routine primary network capacity upgrade, but an error occurred where traffic was routed to the secondary network, which overwhelmed it due to its lower capacity. The effect was that EBS volumes that no replica were not accessible. EBS volumes that required a replica, but now no longer had one then tried to recreate the replica, which caused even more traffic to be routed through the secondary network, thus causing even more network congestion. The Amazon team had to disable the replication requests to ease the traffic. The problem was made worse by the replication mechanism not using a **back off** protocol when initial requests fail. There was also a race condition in the EBS code that occurred only during high request rates, which caused even more nodes to fail. Amazon eventually recovered most of the data, but 0.07% of affected volumes were lost. A key lesson is that failures are often **cascading**.

Facebook suffered an outage in 2010, where Facebook was unreachable for 2.5hrs. Facebook stores data in both persistent stores and caches, which includes configuration data. Configuration values in the cache are automatically verified. The outage occurred when Facebook made a change to the persistent copy of a configuration that made it invalid. Facebook cache servers recognised the incorrect configuration and then all of them tried to fix it, which required querying a cluster of databases. The mass requests quickly overwhelmed the database, which causes failed requests. The cache severs did not use a back off protocol, making the problem even worse. Facebook disabled the website, allowing the database to recover, then slowly allowed users back on the site. After this outage a new configuration system was designed. Key lessons here are to not aggressively retry for resources and instead use a back off protocol such as exponential back off.

Planet had an outage on 2008. It was the 4th largest web hosting company and hosted 22,000 websites. The outage started with an explosion in a datacentre. A transformer caught fire, which caused an explosion of battery acid fumes the UPS backup and there was a cascading of failures. Some servers may have been moved off site to other datacentres, but there were 9,000 servers, so they could not all be moved. A key lesson is that sometimes we can lose access to data, so it is imperative to have important data be replicated, preferable across different sites.

### Programming assignment 2: Key-value store

The second programming assignment was to implement a fault-tolerant key-value store. The key-value store was to be built within the same system that was worked on in the first programming assignment, so it was required that the membership protocol would be complete and have a high degree of accuracy so that nodes could detect failures.

Requests would be made to a node that was designated as the coordinator, which would handle CRUD (create, read, update, delete) requests. Key-value pairs were to be load-balanced across the nodes. The nodes were structured in a ring and the primary copy of the key-value pair was assigned to a node based on consistent hashing of the key. To support fault tolerance, two replicas were created of each key-value pair and were placed at the successors in the ring. Requests required a quorum consistency level, meaning that two of the three requests that were sent out needed to be in agreement. After a node failure, replicas had to be recreated such that the protocol would always attempt to have three replicas.

The protocol was implemented by having nodes keep track of their last known two successors and two predecessors. They detected failures by comparing this to any updates to their overall membership list. Nodes kept track of whether they contained the primary, secondary or tertiary key-value replicas. The primary node was always responsible for ensuring that it replicated its key-value pairs to its two successors, which meant sending out messages to potentially update a tertiary replica to a secondary replica and/or create new secondary and tertiary replicas. It was therefore necessary for a node to look at its predecessors and upgrade itself to a primary replica in the case that it was a secondary node and the primary failed or if it was the tertiary node and both the secondary and primary nodes had failed.

Both the coordinator and the servers logged the result of requests and the system was tested for its ability to respond correctly to both single and multiple node failures. It was challenging to figure out which messages had to be logged in the case of the servers sending and receiving stabilisation messages, but this was eventually resolved. Valgrind was very useful again in this assignment as a host of memory bugs required fixing before the system worked reliably.