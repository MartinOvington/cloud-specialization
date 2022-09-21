# Cloud Computing Specialization

## Overview

The Cloud Computing Specialization is a set of six courses, consisting of lectures, readings, challenging programming assignments and culminated in a larger project. The aim of this report is to present the key takeaways of what I've learned and to document my experience in working through the courses. The courses were my first time working with cloud / distributed computing. I have previously studied operating systems via "Operating Systems: Three Easy Pieces", networking via "Computer Networking: A-Top-Down Approach" and algorithms via "Data Structures and Algorithms in Python", all of which were very useful in working through the material and doing the assignements.

1. [Cloud Computing Concepts Part 1](#cloud-computing-concepts-part-1)
2. [Cloud Computing Concepts Part 2](#cloud-computing-concepts-part-2)
3. Cloud Computing Applications Part 1: Cloud Systems and Infrastructure
4. Cloud Computing Applications Part 2: Big Data and Applications in the Cloud
5. Cloud Networking
6. Cloud Computing Project

## Cloud Computing Concepts Part 1

The first course in the specialization covered the internals of cloud computing - the fundamentals of distributed systems and the distributed algorithms that are used in such systems.

### Week 1 - What are Clouds, MapReduce

The market for cloud computing services has grown enormously in the last decade, with big players including Amazon AWS, Microsoft Azure and Google Cloud Computing Services. Cloud computing is a losely defined subset of distributed systems, usually focusing on providing private or public systems that can be rented on demand to provide a variety of computing and storage services. They are often build using connecting together lots of cheaper commodity hardware, which distinguishes clouds from other distributed systems where highly specialized, expensive high performance hardware is used. The service is usually provided from one or more datacentres, with big providers having many sites throughout the world so that customers can choose ones closer to them for lower latency. The motivation is largely to provide customers with easy to use services that have low or no upfront costs, thus saving them time and money for many use cases.

The MapReduce programming paradigm introduced by Google in 2004 and consists of a series of mapping and reducing cycles used to process data. It is a flexible model that works on Key-Value pairs and can be use in large variety of cases. It is particularly well suited to distributed computing as the Map and Reduce tasks can often be parallelized due to independence of the work, thus allowing the tasks to be split across multiple workers. Workers get their data input from a distributed file system e.g. Google File System (GFS), Hadoop Distributed File System (HDFS). Intermediate results from the Map tasks are stored on workers' local file system to exploit locality and reduce tasks then use these local data as input and outputs the result to the distributed file system. Apache Hadoop is an implementation of this paradigm and consists of a Global Resouce Manager (RM) that schedules tasks, Per-server Node Managers (NM) that are responsible for daemon and server specific functions and a Per-application Master that negotiates with the RM and NM and is also responsible for detecting task failures. Server failure is detecting via heartbeating and if a worker is performing a task slowly, the task is replicated and given to another worker.

### Week 2 - Gossip, Membership, Grids

Within distributed systems it is often necessary for nodes to have some knowledge of the state of other nodes within the network. Using a central node to aggregate and send out this information creates a single point of failure and may cause a bottleneck to develop in the network. The Gossip protocol, also known as Epidemic protocol is a solution to this problem that involves nodes sending out it's own aggregated state data to a randomly selected subset of the other nodes. Despite the randomness, it can be shown that such a system fairly reliably propagates data throughout the entire network and requires few cycles to do so - much like how gossip and epidemics quickly spread. Importantly, the protocol has the characteristic of fault tolerance, such that if any one node fails it usually does not consistitute a total failure of the system. Cassandra, a distributed key-value store is an example of a system that uses the gossip protocol for maintaining membership lists.

A group membership list is the aggregated information as to which nodes form the system at a given point in time. In distributed systems it is important to have accurate information as to which nodes form part of the group. We must therefore both detect when a new node joins the group and when a particular node has failed and is therefore no longer part of the group. A failed node may also later recover and may need some mechanism to rejoin the group. The problem of having an accurate membership list is made more difficult by the fact that nodes can fail silently and that our communication medium may drop or delay packets. We desire both **Completeness**, that each failure is in fact detected and **Accuracy**, that there is no mistaken failure detection. In reality we cannot guarantee both completeness and accuracy, so always prioritize completeness and sacrifice some accuracy. Other than gossip membership, other examples of protocol include SWIM, ring heartbeating and all-to-all heartbeating. Ring heartbeating does not work well with multiple failures and all-to-all heartbeating requires a relatively large amount of bandwidth.

Grids are used for computation-intensive computing, also known as High Performance computing (HPC). Some data processing tasks are very CPU intensive, but do require much storage or memory and such are more suited to a HPC grid solution. Scheduling for HPC consists of both intra and inter-site protocols.

### Week 3 - Peer-to-Peer (P2P) Systems, Distributed Hash Tables (DHT)

P2P systems were the first large, scaleable distributed systems and some of the techniques are used in key-values stores. They are therefore very relevant to the study of cloud computing. P2P systems are split into unstructured P2P systems and structured P2P systems. They have been commonly used for file sharing, both legal and illegal, with well known platforms such as Napster and BitTorrent.

The architecture of Napster is a central set of nodes that store the file directory with clients running their own client connecting to a set of one or more central servers. When clients connect to the server, they upload the list of files that they wish to share. Clients can search the servers for desired files using keywords and gets a list of hosts for these files. The client can then directly fetch the file from the best host, usually chosen based on the available bandwidth. Clients can join the network by communicating with a well known URL, which can then introduce the client to one of the central servers.

Gnutella uses a different structure to Napster, by eliminating the need for the central servers. Gnutella is therefore a more purely distributed system and avoids the legal problems that Napster had with storing the filenames on their central servers. Instead, peers themselves can act as servers within the distributed network. There are five main message types: Query (search), QueryHit (response to query), Ping (probe network for peers), Pong (reply to ping with address of other peers) and Push (for initiating file transer). When clients wish to search for a file, they flood peers with Query messages which are time-to-live (TTL) restricted. When a peer has the requested file, they respond with a QueryHit message, which can then begin the process of file transfer at the querying peer's discretion. The Ping/Pong messages help to deal with the high rate of churn that are typical of P2P systems, so perform the function of keeping neighbour lists fresh. The problem of this **unstructured** approach is that a high proportion of traffic is due to flooding messages in an unintelligent way.

FastTrack is a hybrid between Gnutella and Napster that takes advantage of healthier participants in the system by designating them as supernodes. Supernodes stores directory files in a subset of nearby peers. Peers then query the supernodes rather than flooding messages to all peers, which leads to faster search and less overall network traffic. Supernodes have the advantage of having the file table locally, which means that their own searches, on-average, require less network traffic.

BitTorent is a system that incentivizes less selfish behaviour. BitTorrent maintains a tracker for file. Peers get the tracker from a known website. Trackers then give the peer information as to some of the peers that are using the required files. Peers are split into seeders who have the full file and leeachers which has only some of the file. Files are split into **blocks**. Peers transfer blocks with tit-for-tat bandwidth, with preferences given to pers who transfer with higher bandwidth. Choking limits the number of neighbours to upload to the best neighbours, so uploading bandwidth is not overwhelmed.

Distributed Hash Tables (DHTs) allow for lookup similar to normal hash tables, but instead objects are in a distributed fashion over a cluster of nodes. The distributed nature introduces a new set of concerns: load-balancing, fault-tolerance, lookup/insert efficiency and locality. Chord is an example of a DHT which uses a **structured** approach, which allows for O(log(N)) memory usage, lookup latency and number of messages required for lookup. It uses consistent hashing of a peer's address, which then maps peers to one of 2^m logical points on a circle. Peers maintain a finger table, which contains the addresses of subset of peers in the circle, which allows for faster lookup of peers. Files are mapped using the same consistent hashing and stored at the peer at or the first clockwise peer after that key value on the logical circle. Failure tolerance can be achieved by replicating objects within a limited number of successors peers. Failures then require a stabilization protocol to maintain this level of replication.

Pastry is a P2P system that assigns IDs to nodes using consistent hashing like Chord, but instead uses routing tables based on prefix matching. It tries to pay attention the network topology and tries to make routes to immediate neighbours short. The greater locality is achieved naturally through the use of prefixes of the addresses. The result is that there is a shorter round-trip time than systems that do not account for the network topology, which means greater performance of this system.

Kelips builds on this idea of locality by maintaining k number of 'affinity groups', each of which is based on the locality of nodes with each other. Within an affinity group, nodes know of all other nodes within that group. One node within that group maintains a link to a node in each of the 'foreign' affinity groups. Lookup costs are then O(1), which is an approvement over Chord, but has the disadvantge of slightly worse memory usage at O(sqrt(N)).

### Week 4 - Key-Value NoSQL Stores, Time and Ordering

Key-value stores are pairs of keys of values, that allow you to lookup and retrieve values using a given key. Modern workloads has data that is often large and unstructures, has lots of random reads and writes, do no often require foreign keys, are sometimes write heavy and needs to perform joins infrequently. This means we can come up with a simpler, more suitable storage solution than tradition relational databases. Having a system where we can scale out in a cost effective fashion is desirable. NoSQL, 'Not Only SQL', helps us with many of these requirements. It is largely based on the the API consisting of get(key) and put(key, value). NoSQL tables can be structured or unstructured with no schema. NoSQL can use column based storage rather than row based storage that tradition relational database management systems (RDBMSs) use, which allow you to do column based searches faster.

Apache Cassandra is a distributed key-value store that was originally designed by Facebook. It places servers on a ring and stores keys at the server that is the successor to the key's mapping on the ring. There are primary replicas and backup replicas for all keys. There are two main replication strategies: SimpleStrategy (can be similar to Chord) and NetworkTopologyStrategy. The NetworkTopologyStrategy ensures that the second replica is stored on a different rack to the primary replica, which helps improve fault tolerance. An important consideration is **consistency** for reads and writes. Writes use an on-disk commit log for failure recovery. Writes are then applied to the memtable, which is the in-memory representation of multiple key-value pairs that can be searched by key. In the case of an unexpected shutdown, anything in the commit log is applied to the memtable. When the memtable becomes old or full, it is flushed to disk as an SSTable - the immutable data files that are used for persistent storage. SSTables can be compacted when these flushes occur and then the old, stale SSTables can be removed.

Cassandra uses **leadership election** for selecting the coordinator of requests, which is done by Apache Zookeeper (a Paxos variant). A Bloom Filter is used to efficiently check for existance of a key in the system, with some small probability of false positives. It uses bitmap in combination with a hash function to get a key's index into the bitmap, with possibilitys of keys overlapping - hence the false positives.

The CAP theorem states that we can only have guarantees on two out of the three properties: Consistency (same data on all nodes), Availability (data available all the time), Partition-tolerance (system works even if network split). We must sacrifice the guarantee on the third property. Companies providing cloud services have found that availability (low latency) is often strongly correlated with profits, so this property is usually prioritised over consistency. They instead often opt for **eventual consistency** - the idea that eventually nodes will be consistent if new writes stop coming into the system. Clients then have the possibility of receiving slightly stale data, but in many use cases this is not a problem. Systems such as Cassandra can also be configued to have different consistency levels for reads/writes: quorum (threshold of nodes), one replica, all replicas, any server (not replica).

HBase is an open source system developed by Yahoo that prioritises consistency over availability. It uses a write-ahead log to help to achieve this.

In cloud systems, synchronization is required for correctness or fairness. It is a challenge because each system has their own separate clock and communication happens over the internet, which means that they have to try to achieve synchronicity within an asynchronous model. Multiple algorithms exist for synchronizing clocks in distributed systems, with different properties. Cristian's algorithm is used for external synchronization - where a system must synchronize with a clock external to the system. It calculates round-trip-times (RTT) to help to form an error interval for the actual time and then it sets the time to the middle of the interval. The Network Time Protocol (NTP) is used for synchronizing clocks on the internet, which uses a tree structure, where children sets their clocks according to their parents clock and the RTT.

Logical (or Lamport) ordering uses a happens-before relationship among pairs of events across processes. The result is a partial ordering among events within the group of processes, meaning not all events will have a causal ordering between them. We cannot distinguish between **causal** and **concurrent** events in this system. Vector clocks solve this problem by using a vector of times (one per process in system) rather than a single time. This allows us to set up rules that allow us to identify concurrent and causal events.

### Week 5 Snapshots, Multicast, Paxos

Global snapshots in clouds is used for checkpointing, garbage collection, deadlock detection and termination of computation. The global snapshot is the global state of all the processes at a given point in time. Issues related to causality, time synchronization and messages in transit make creating this snapshot challenging. The Chandy-Lamport is an algorithm for creating a global snapshot in a running system. It uses marker messages, sent out from an initiator process and then multicast to other processes on receipt of the first marker messages that it has received for this snapshot. Processes record the events and messages that happen between this first marker messages and receiving marker messages on all other incoming channels. Global snapshots should aim to create **consistent cuts**, which means that all the events that they encapsulate are causally correct i.e. if A causes B and B is in the cut, then A should be in the cut.

Within a cloud system, a **liveness** is the guarantee that something good will happen eventually e.g. a computation finishes or nodes agree on a value. **Safety** is the guarantee that nothing bad ever happens e.g. no deadlock and no conflicting consensus.

The multicast problem is where we have a piece of information at a node that needs to be sent to a group of nodes. An example of multicast is storage systems like Cassandra that use multicast to send data between nodes that are part of replica group. Sometimes we desire FIFO ordering for multicast messages. Messages sent from a given node often need to obey FIFO ordering, but it often matters less if multicast from two _different_ nodes do not obey the ordering. FIFO ordering is achieved by using a vector of sequence numbers at each process and sends a per-process sequence number with messages. Another type is causal ordering, where messages must be delivered according to the causality of events. For causal ordering, a vector of sequence numbers is sent with each message. Total ordering ensures that all receivers receive messages in the same order in all processes and achieves this using a global sequence number given by an elected leader. Reliability of multicast refers to the property that either all correct processes receive it or no processes receive it. Virtual synchrony is a technique that attempts to preserve multicast ordering and reliability in spite of failures.

The concensus problem is relevant to reliable multicasting, membership/failure detection, leader election and mutual exclusion (exclusive resource access). Consensus involes getting a group of processes to agree on a value for a given request. It is more challenging to achieve concensus in asynchronous system due to unbounded time of process execution and message delay. A large part of the problem is that within an asynchronous system we do not know for certain if another process has failed, if it simply slow or the messages are being delayed or lost.

The concensus problem has been shown to be impossible to solve in asynchronous systems. Paxos is an algorithm that tackles the concensus problem in asynchronous systems by providing a safety guarantee and eventual liveness. It is used by systems such as Zookeeper and Google's Chubby system. Paxos works in rounds with a unique ballot ID for each round. Rounds are asynchronous and are broken into phases: leader election, leader proposes a value (bill) and finally leader multicasting final value (law). The leader election requires reaching a majority (quorum). The leader then sends proposed values to all other processes, which reply OK on receipt and log the value. If the leader gets a majority of OKs, then it treats it as concensus and the value is decided.

### Programming assignment 1

The programming assignment involved implementing a membership protocol. The code provided was split into an application layer, a peer-to-peer layer and an emulated network layer (EmulNet). The membership protocol was to be implemented in the P2P layer and had to satisfy completeness all the time, meaning non-faulty processes had to detect every node join, failure and leave. The protocol had to satisfy a certain level of accuracy of failure detection. The implementations of the protocol was tested under simulated message loss and single and multiple node failures.

The application layer was responsible for bootstrapping the P2P network by initializing all members of the P2P layer and having them issue join requests to the first node created. The nodes were responsible for maintaining their own membership lists and logging all joins and failures. Nodes could only communitcate via the EmulNet messaging API, which had functionality for sending and receiving messages. To test the correctness of the implementation, the application layer could choose to fail nodes at will or have messages be dropped. The performance of the protocol was assessed by checking the logs that the nodes produced.

The code was written in C++ and used a mixture of C++ and C-style coding, which made it somewhat challenging to work with. The emulated network required packing in data in a way that could be reliably retrieved at the other end and I chose to use carefully aligned structs to pass in and retrieve data from the network. Each node maintained a vector for it's membership list. The membership protocol chosen was all-to-all heartbeating. SWIM and Gossip could also have been implemented and would have used less 'bandwidth', but the simplicity of all-to-all heartbeating made it attractive. A fair amount of time was spent on debugging memory issues due to pecularities of the program using manual memory allocation, the 'new' and 'delete' keywords and implicit allocation/deallocation of memory with constructors and destructors. Using GDB and in particularly Valgrind was very useful to track down bugs. Valgrind still indicated some issues with the program at completion, but this related to code that was provided. In the end the protocol passed all tests for full marks.

## Cloud Computing Concepts Part 2

The second course continues where the first course left off, with more teaching of the internals of the cloud - concepts, techniques and idustry systems.

### Week 1 Leader Election, Mutual Exclusion

Leader election is necessary in distributed systems for various reasons. From the clients perspective, they usually only wish to communicate with one of the nodes in the system, which must then act as the coordinator for the rest of the system. Another example that we have seen is where a global sequence number must be maintained, so we need a designated server for performing this function.

Within the group of nodes, there must be agreement among them as to which node is the leader. For this consensus problem, we need a leader election algorithm to decide the leader. Important considerations for the algorithm is what happens if the leader fails, who can call for an election and how we maintain safety (maximum one, best leader) and liveness (eventually a leader is elected).

The ring leader election algorithm allows any node in the ring to call an election. As the message passes around the ring, nodes with the better attributes than the previous best modifies that message with its own details and then passes it on around the ring. When a node receives its own details, it knows it is the best would-be leader and then passes on a message to inform the other nodes that it is now the leader. Within this system we require some method of failure detection such that a predecessor or successor to a failed leader can initiate a new election.

Paxos-like protocols can be used for leader election, which is what is done within Google Chubby (locking system) and Apache Zookeeper. Within Google Chubby, leaders are elected based on leaders reaching a quorum of votes. After elections, a leader receives a master lease, which guarantees that another election is not run for a set period of time. In Zookeper, each server creates a sequency number for itself and the highest sequence number in the election messages determines the leader. It uses a two-phase commit for the leader selection which ensures that safety is maintained.

The Bully algorithm for leader election works by all processes knowing each others process IDs. When the current coordinator is detected by a given process, processes which know they are the next highest ID sends Coordinator messages to all lower IDs, which completes the election. Other processes intiate an election by sending an election message to proceeses that have higher IDs than itself. When a process gets an election message, it sends election messages to processes with higher IDs than itself. By detecting failures by timeouts, these election messages will eventually allow the highest ID process to detect the failure of the coordinator and send out Coordinator messages to elect itself.

Mutual exclusion is important in distributed systems as it is common for there to be multiple processes trying to read/write to the same shared resource. The problem can be broken down into the idea that within our processes we can have **critical sections** within our code that should be accessed by at most one process at a time. Within a single OS we can rely on all processes being with the same machine, which allows us to use semaphores, mutexes and condtion variables. These do not work on distributed systems as they really on shared variables that we do not have in this context. Our solution for this problem requires that we have safety (max one process in critical section at a time), liveness (every request to access critical section eventually granted) and we may also desire some level of fairness by having requests be granted in FIFO ordering.

One solution to mutual exclusion in distributed systems is the central soluion. We elect a leader which is responsible for maintaining a queue of requests and a special access token that allows access to the critical section. Other processes wanting to enter the critical section requests the token from the master and when it exits the critical section it must release the token back to the master.

The Ricart-Agrawala algorithm is a classical distributed systems algorithms from 1981. It allows for faster access to the critical section compared to the ring-based tokan passing approach. Processes multicasts requests containing lamport timestamps and then waits for all replies before entering the critical section. All processes queues outgoing replies based on the lamport timestamps and in that way fairness is maintained based on the causality inferred from the lamport timestamps.

Maekawa's algorithm is another attempt to solve the mutual exclusion problem. The basic idea is that you only require replies from _some_ of the other processes to get access to the critical section, a similar same idea to requiring a quorum. It uses the idea of intersecting voting sets to ensure mutal exclusion.
