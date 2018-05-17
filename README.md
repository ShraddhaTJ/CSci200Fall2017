







Bloom Filter Lecture 8
 

Background




● Space efficient, probabilistic data structure for querying membership introduced by Burton Bloom in 1970.
● It tells us whether a search key is definitely not in the set or may be in the set.
● Uses in dictionaries, DBs, and Blocked URL check in Chrome browser.
 

Two Bloom Filters operations:




● The ability to add an extra object 'x' to the set 'S'.




●	To determine whether a given object 'x' is a member of set 'S'.
 

Compare to Hashed Based Approach




● Hashed based approach has higher false positive element probability. ● It requires more memory space.
● The query cost is much higher than the hashed based approach. ● It does not store the object itself.
 

How Bloom Filter Works?




M bits array (initially set to 0) with K hash functions.

add(key):

● count = 0 ● do
○	count++
○	Index = next_hash_function(key) ○	Array[ index ] = 1
● While count < K
 

How Bloom Filter works?



is_member(key):

● count = 0 ● do
○	count++
○	index = next_hash_function(key)
● While ( count < K ) OR ( isContains(array[ index ]) ) ● If count == K then:
○	Return True ● Else:
○	Return False
 

Demo




https://www.jasondavies.com/bloomfilter/


https://www.geeksforgeeks.org/bloom-filters-introduction-and-python-implementation/
 








Gossip Protocol
 

Gossip papers




● https://htor.inf.ethz.ch/publications/img/corrected_gossip.pdf

● https://web.stanford.edu/~boyd/papers/pdf/gossip.pdf

● https://github.com/thomai/gossip-python



● Sequential to parallel
● https://stackoverflow.com/questions/39159438/python-multithreading-vs-multiprocessing-vs-sequential-execution
 
Problem




● Broadcast or multicast data to multiple nodes simultaneously. ● DB replication, video streaming, etc...
 
Usage




● Amazon S3 and Dynamo DB ● Bit Torrent
● Consul
 
From Epidemiology to Distributed Systems




● Disease spread quickly and robustly.
● Our goal is to spread an update as fast and reliable as possible.
● Can we apply these ideas to the distributed systems?
 
3 Stages in Gossip
1. Susceptible: Node "n" is not yet infected by the disease.
2. Infective: Node "n" is infected and capable to spread the disease. 3. Removed: Node "n" has been infected and has recovered from the
disease.




1. Susceptible: Node "n" has not yet received an update.
2. Infective: Node "n" has received an update and is now ready to forward the update.
3. Removed: Node "n" has the update but is no longer willing to share it.
 
2-Style of Epidemic Protocols




Anti-entropy

a. Each Node "n" periodically contacts a random peer "p" selected from the pool.
b. Then, n-p engages in an information exchange protocol: n to p (push), n from p (pull), or both push-pull.
 
2-Style of Epidemic Protocols




Rumor mongering

a. Nodes are initially ignorant.
b. When an update is received by a node, it becomes a hot rumor.
c. While a node holds a hot rumor, it periodically chooses a random peer from the pool and sends (push) the rumor to it.
d. Eventually, a node will lose interest in spreading the rumor.
 
Gossip Protocol




● Each node in the system periodically exchanges information with a subset of nodes selected randomly.
● Complete membership table is unrealistic in a large scale dynamic cluster.
● Instead, each node maintains a small local membership table that provides a partial view on the complete set of nodes.
● Periodically refreshes the table using a gossiping procedure.
 
Peer Selection

● Uniform Random ● Highest Age
 
Data Propagation



● Push
● Push-pull
 








PoW, PoS, & ZK-Proof CMPE 273
 
What is mining?




● To verify the legitimacy of a transaction so that we can avoid double-spending problem.
 
Transaction Verification Methods




● Proof of Work (PoW) ● Proof of Stake (PoS)
● Zero Knowledge Proof (ZK)
 
Z One party can approve to another party without conveying any other information.
● Ali Baba Cave Example
○	Peggy is the prover and Victor is the verifier.
○	Peggy knows the secret to open a magic door in a cave.
○	Victor wants to know whether Peggy knows the secret word.
○	Peggy does not want to reveal her knowledge (the secret) to Victor or others.













https://en.wikipedia.org/wiki/Zero-
 
ZK Proof




● A prover to prove that they have some knowledge of a secret without revealing any information: h = hash(secret)
● Camenisch-Stadler notation: ○	ZKPoKx{(w) : L(w, x)}
■	x: public info known to both the prover and the verifier ■	w: witness or a secret information
■	L: Language represents the condition that the statement witness must satisfy.
 
Sigma Protocols (Σ-Protocols) for knowledge





● Proof scheme ZKPoKA{(a) : g a = A}
○ where g is the generator of a group G of prime order p.
○ Example: https://github.com/sithu/python-zk-proofs/blob/master/simple-zk-proofs.py
 
3 Properties of ZK Proof




1. Compleness
a.	If the statement is true, the honest verifier will be convinced of the fact by an honest prover.
2. Soundness
a.	If the statement is false, no cheating prover can convince the honest verifier that it is true, except with some small probability.
3. Zero-knowledge
a.	Just knowing the statement, not the secret, is sufficient to show that the prover knows the secret.
 









Consensus Approach
 
Consensus algorithm



oConsensus == Agreement
oConsensus algorithm for managing a replicated log.

oIt allow a collection of machines to work as a coherent group that can survive when its members fail.

oIt is a main building block in building reliable large-scale distributed software systems.
 
Quorum Consensus algorithm?



oA distributed system is a set of processes working together to form a complete system.

oSometimes, it's important that some processes agree on a result or a change of state before it's actually reflected by the system.

oQuorum represents the number of participants required to agree on a result before it can be applied.
 
Quorum Consensus algorithm?



oSuppose you have two nodes (A & B) storing the same set of data.

oN = # of replicas (nodes)
oR = # of READ nodes in quorum oW = # of WRITE nodes in quorum oIf R = 1, W = 1, then R+W = N
o Client can write into A and read from B (might not see its latest write): inconsistent view.

o If R = 2, W = 1, then then R+W > N
o Writes go to either A or B, but client must read from both A and B: consistent view.
 
Example-1: R=3, W=1 for 3 Replica



oThis improves performance for writes at the expense of reads

oIt is probably a bad idea if reads are more common than writes.

oThis choice of quorums is bad because a write might happen at a single replica that then fails. If that replica were to lose its state, the outcome of the write would be lost. So generally we would like to have W>1.
 
Example-2: R=1, W=3 for 3 Replica



oThis works very well for reads which is generally good if reads are common.



oBut it is undesirable for writes because if one of the replicas is down or inaccessible, a write cannot complete until that replica recovers.
 
Example-3: R=2, W=2 for 3 Replica






oThis choice is a good compromise compared to the R=1 and W=3 choice, since it increases the cost of reads in return for providing reasonable availability for writes.
 
What is Raft consensus algorithm?




oA lot simpler than Plaxo algorithm. oEasy to understand
oState space reduction to reduces inconsistency window between nodes.

oDecomposition into sub components: Leader election
Log replication Safety
 
What is Leader Election?



oThe process of choosing a leader from a pool of machines.

oLeader election is the process of determining a process as the manager of some task distributed among several processes (computers). ----
(Garcia, Molina 1982)
 
Why Leader Election?



oCentralized control simplifies data and task synchronization.

oIt is a single point of failure, but solution is to choose a new leader upon failure.

oMany algorithms for leader election.
 
When Leader Election?



oDuring system startup or when an existing leader fails reported by either its own heartbeat or its clients.

oA client that gets no response from the leader for a predefined time-out interval suspects a failure and initiates leader election.
 
Raft: Novel Features



o Strong Leader
o Log entries only flow from the leader to other servers. o Easier management of the replicated log.


o Leader Election
o Raft uses randomized timers to elect leaders.
o Likewise to other algorithms, heartbeat is required.


o Membership Changes
o Raft uses joint consensus approach where the majorities of two different configurations overlap during transitions.
o This allows the cluster to continue operation normally during configuration changes.
 
Raft: Three Roles



oThe Leader oThe Follower oThe Candidate



oAt any given time each serer is in one of the above three states.

oIn a normal operation, there is exactly one leader!
 
The Candidate




F








C		F Ask For Vote From Followers
 
The Candidate




F








C	F



Receives Votes From
Followers
 
Becoming the Leader




F








L	F
 
The Leader



F








L	F
 
The Leader



F








L	F
 
When the Leader dies



C








L	F
 









Non-Consensus Approach
 


CALM Principle






Consistency As Logical Monotonicity (CALM)
 
Monotonic






oIf a block of code satisfies a simple property: adding things to the input can only increase the output.
 
Non-Monotonic






oA block of code may need to "retract" a previous output if more is added to its input.
 
CALM principle says that…



ologically monotonic distributed code is eventually consistent without any need for coordination protocols (distributed locks, two-phase commit, paxos, raft, etc.) meaning non-consensus.

oeventual consistency can be guaranteed in any program by protecting non-monotonic statements with coordination protocols.
 









CRDT
 
What is CRDT?





oConflict-free Replicated Data Type



oConvergent Replicated Data Type



oCommutative Replicated Data Type
 
Commutative



oThe Commutative Property is the one that refers to moving stuff around



a + b = b + a 2 + 3 = 3 + 2



ab = ba 2×3 = 3×2
 
Associative


o"associative" comes from "associate" or "group".
oThe Associative Property is the rule that refers to grouping.



a + (b + c) = (a + b) + c 2 + (3 + 4) = (2 + 3) + 4



a(bc) = (ab)c 2(3×4) = (2×3)4
 
How CRDT stores/corrects Data?


oNode0 { 123, 456 } oNode1 { 123 } oNode2 { 123, 456 }



{123, 456} U { 123 } U {123, 456} = {123, 456}



{123, 456} Δ { 123 } Δ {123, 456} = { 456 }
 
CRDT WRITE (Fan Out)




{123}	{123}	{123}
 
CRDT WRITE Partial Failed




{123}	{123}	{}
 
CRDT READ (Fan In)




{123}	{123}	{}
 
CRDT READ (with Repaired)




{123}	{123}	{123}
 









Consistency without Consensus = CRDT
 
ACID 2.0




oAssociative



oCommutative



oIdempotent



oDistributed
 


Parallel Programming in Distributed Systems




Lecture 10
 
Parallelism building blocks
 








Programming Models
 
Programming Model 1 : Shared Memory



 Multiple threads communicating through memory.
 Threads communicate implicitly by writing and reading shared variables.
 Atomicity, locking and synchronization for correctness.
 Many scalability issues - deadlocks, starvation, race condition
 
Programming Model 1 : Distributed Memory



 Explicit data exchange between processors/nodes/JVMs)
 Data distribution and communication orchestration is essential for performance.
 
Programming Model 2 : Message Passing


 Shared nothing.
 Processes communicate by explicit send-receive pairs  De facto standard for parallel computing
 Actor Model - supports in Actor (Scala & Java), Erlang, etc..
 
Shared Memory or Message Passing?


o Depends on the program!
o Can build Shared Memory with Message Passing and vice-versa


Advantages of Shared Memory	Advantages of Message Passing

 
 Implicit communication
 Low overhead when cached
 
 Explicit communication (send/receive)
 Easier to control data placement
 



Disadvantages of Shared Memory

 

 Complex to build scalable system  Requires synchronization
 Hard to control data placement within caching system
 
Disadvantages of Message Passing

 Message passing overhead can be quite high.  More complex to program
 Introduces question of reception technique (interrupts/polling)
 
What is a sequential program?


o A single process or thread that executes instructions in order.
 
Bernstein's Condition to parallelize


o Two tasks T1 and T2 can run in parallel o IF
o input_to_T1 != output_from_T2 o AND
o input_to_T2 != output_from_T1 o AND
o output_from_T1 and T2 do NOT overlap
 
Concurrency vs. Parallelism


o Concurrency
o Gives "illusion" that multiple tasks running at same time.
o Interleaves execution


o Parallelism
o Physically simultaneous processing
o Requires a multicore system
 









Design Patterns
 
SPMD pattern




o Single Program Multiple Data: the same program runs against multiple data on each processor or node.
o Initialize
o Split data correctly and evenly o Run the same program
o Combine the results o Finalize
o Not supported for dynamic load balancing
 
Master-Worker Pattern


o Particularly relevant for problems using task parallelism pattern where task have no dependencies

o Embarrassingly parallel problems
o Main challenge is determining when the entire problem is complete
 
Future/Defer




o FutureTask (Java 5) can be used to load lengthy computation that can be started before the results are needed.
 
Fork-Join pattern


o Supports divide-and-conquer pattern.
 

 
Parallel Array - Fine-grained parallelism

o Parallelize large data set by recursively break it into smaller data sets.
 
Fork-Join Framework
 
Under the hood Fork-Join uses Work-Stealing
 
Fork-Join Parallel Array Performance Performance Speedup for MaxSalary

350

300

 
250                                                                                                       Best 200                                                                                                       Case

150

100
 
1000 10000 100000 1000000
10000000
 

50	Employees 0
1	2	4	8

Num Threads	(Running on Core 2 Quad System) src - http://www.ibm.com/developerworks/java/library/j-jtp03048.html
 
Major Organizing Principles


o How to choose a design pattern that facilitates the mapping of tasks to units of execution?



o 1. Organize by tasks
o 2. Organize by data decomposition o 3. Organize by flow of data
 
4 Steps to Creating a Parallel Program
 
Data Parallelism

o Perform same computation, but operate on different data.

o How to divide data?
o Array data structure - by rows, columns or blocks o Recursive data structure - trees into sub-trees



Thread 0


Thread 1


Thread 2


Time 
 
Task Parallelism


o Each execution process (thread) performs different functions on same or different data.




Thread 0



Thread 1



Thread 2



Time 
 
Pipeline Parallelism

o Wash(30min) + dry(40min) + fold(20min) = 90 min (Latency) o Sequential exec = 4 * 90 min = 6 hrs
o Pipelined exec = 30 + 4*40 + 20 = 3.5 hrs


Bandwidth = loads/hr
4/6 < 4/3.5 Bandwidth up! But still the same
latency
 
Amdahl's Law


o A program can be sped up by adding computing resources, based on proportion of serial and parallelizable components.

o p = fraction of work that can be parallelized o n = the number of processor



o Speedup =  old running time o	new running time
 
Amdahl's Law Example
 
Performance Scalability
 
3 Keys to Parallel Performance


o Coverage of parallelism in algorithm o Amdahl's Law
o Granularity of partitioning among CPUs. o Communication cost and load balancing
o Locality of computation and communication
o Communication between CPUs or between CPUs and their memories.
 
Single Thread Performance





o Two keys to faster execution
o Load balance the work among processors. o Make execution on each processor faster
 
Performance Considerations




o For computation-intensive (CPU-bound) tasks that do no I/O and access no shared data,
o N	or N	+ 1 threads optimal throughput
o More threads can degrade performance as the threads compete for CPU and memory resources.


o Load balancing factors o Tasks costs
o all tasks have equal (unit) cost or not? o Tasks dependencies
o tasks can execute in any order or have a predictable/dynamic structure? o Locality
o Zero, regular, irregular communications among tasks
 
Ask yourself! (Brain's suggestions) o What do you mean by "faster"?
o Under what conditions will this approach actually be faster? o Under light/heavy load?
o With large/small data sets?
o How often are these conditions likely to arise in your situation?
o Is this code likely to be used in other situations where the conditions may be different? Average-case time or worst-case time?
o What hidden costs-safety, increased development or maintenance risk- are you trading for this improved performance? Is this a good tradeoff?
 







"Avoid premature optimization. First make it right, then make it fast-if it is not already fast enough." by Brian Goetz
 
References


o Multicore Programming Primer - MIT open courseware o 2009 Par Lab Parallel Programming Boot Camp (UCB)
o Java concurrency in practice by Brain Goetz, Tim Peierls, Joshua Bloch , Joseph Bowbeer, David Holmes, Doug Lea

o "A Java Fork/Join Framework" by Doug Lea
o "Java theory and practice: Stick a fork in it, Part 2" (developerWorks, March 2008): Explore the ParallelArray classes, which simplify parallel sorting and searching operations on in-memory data structures.
 









Performance
 
I/O Bottleneck
 
Performance Metrics




o Capacity: Amount of resource o Utilization: % of capacity using
o Processing Time: Time for a request to process.
o Latency: Any wait time to start processing the request including network transfer.

o Throughput: request/second
 
Threads




o General-purpose solution for managing concurrency o Multiple independent execution streams.
o Shared state.
o Pre-emptive scheduling
o Synchronization via Locks, Semaphore, Conditions.
 
Threads Usage




o OS: one kernel thread for each user process
o CPU-bound Apps: one kernel thread per CPU (E.g. Scientific applications)

o Distributed Systems: process requests concurrently (overlap I/Os) o GUIs: threads correspond to user actions
 
Threads Problems




o Synchronization
o Must coordinate access to shared resources with locks. o Forget to hold a lock? Corrupted data.



o Deadlock
o Circular dependencies among locks.
o Each process waits for some other process: system hangs!


o Threads break abstraction
o Callbacks don't work with locks.
 
Performance Improvement in Thread-based Systems


o Achieving good performance is hard. o Simple locking yields low concurrency
o Fine-grain locking increases complexity, reduces performance in normal case.
o OS limits performance due to different schedulers from between app and OS layers and context switching costs.



o Threads are not well supported in some systems or some libraries.
o Sys calls (kernel calls) are not multi-threaded in some Oses. o Few debugging tools
 
Events




o One execution stream: no CPU concurrency


o Register interest in event callbacks.


o Event loop (scheduler) waits for events, invokes handlers.


o NO preemption of event handlers.


o Handlers generally short-lived.
 
Events



o GUIs or Browser's HTML DOM:
o One handler for each event: press button, right-click on mouse) o Handler implements behavior (delete, undo files, etc.)


o Distributed Systems:
o One handler for each source of input (socket)
o Handler processes incoming request, sends response. o Event-driven I/O for I/O even loop.
 
Events Problems



o Long-running handlers make application non-responsive.

o Cannot maintain local state across events because handler must return.

o No CPU concurrency
o Event-driven I/O not always well supported (e.g. poor write buffering)
 
Threads vs. Events



o Events avoid concurrency as mush as possible, thread embrace:
o Easy to get started with events: no concurrency, no synchronization, no deadlock.
o With threads, even the simplest application faces the full complexity o Debugging easier with events.
o Application logic flow are typically sequential, so it's harder to code in event-driven style.
 
Threads vs. Events



o Events are faster than threads on single CPU: o No locking overheads.
o No context switching.
o Events are more portable than threads o Threads provide true concurrency:
o Can have long-running stateful handlers without freezes. o Scalable performance on multiple CPUs.
o Use events for GUIs, distributed systems.
o Only use threads where true CPU concurrency is needed.
 
Performance Improvement





oScale Up oScale Out
 










Caching
 
Different Types of Caching




o Geo Cache / CDN o Proxy Cache
o Web Browser Cache
o HTML5 Cache Manifest (offline browsing) o Web/HTTP Cache
o Application Cache o DB Query Cache
o RAM Cache o SSD Cache
o CPU Cache (L1,L2..)
 








Merkle Trees
 
Merkle trees


o Implemented based on Binary Trees.
o Hash the data and then stores the data hash in the leaf nodes only. o Each node has up to 2 children.
o Space efficient as it stores hashes instead of full content of the files.
o Uses for data verification in distributed systems: P2P networks, Bitcoin, Git.

o Bitcoin uses it as a digital fingerprint of the entire set of transactions.
 



























SRC: https://en.wikipedia.org/wiki/Merkle_tree
 
MerklE tree


o To store transactions: A, B, and C.
o HA = SHA256(SHA256(Transaction A))
o H = SHA256(SHA256(Transaction B))
o HAB = SHA256(SHA256(HA + HB((
o The process continues until there is only one node at the top, the node known as the merkle root.

o If there is an odd number of transactions, the last transaction
hash will be duplicated to create an even number of leaf nodes. o HCC = SHA256(SHA256(HC + HC((
 
Finding a Transaction (k) in a Merkle tree
 








Blockchain Technologies
 
Blockchain Currency - What is Bitcoin?


o Digital currency and online payment system that uses encryption techniques to
o Regulate the generation of units of currency o Verify the transfer of funds
o Decentralized virtual currency.
o Mining: anyone can use their machines to verify and record payments into the public ledger.
 
Bitcoin blockchian Technology Stack







Crypto-currency	


Bitcoin (BTC), Litecoin, etc…



Bitcoin protocol and client	


Software programs that conduct transactions



Bitcoin blockchain	


Underlying decentralized ledger
 
Keys, Addresses, & Wallets




o Ownership of bitcoin is established through private and public keys, wallet with a bitcoin address.

o Private Key => Public Key => Bitcoin Address (Wallet) using One-way Encryption.
 
Alice's Buying a Cup of Coffee from Bob


o $1.50 USD = 0.015 BTC
o Bob's Café Bitcoin Address: "1GdK9UzpHBzqzX2A9JFP3Di4w eBwqgmoQA"
 
A Bitcoin Transaction
 

 
Transaction Chain
 
Mining


o Miners validate new transactions and record them on the global ledger. o It is an operation of inverse hashing to determines a nonce.
o Miners receive two types of rewards: new coins created with each new block and transaction fees.

o First, miners complete to solve a difficult mathematical problem based on cryptographic hash algorithm (known as Proof-of-Work).

o The competition to solve the PoW algorithm is to earn the reward and the right to record transactions on the blockchain.

o Transactions that added to the blockchain are considered "confirmed".
 
Proof-of-Work vs Proof-of-Stake Algorithm


o Mathematical puzzle which is hard to solve on the Miner side but easy to check for the network.

o Miners compete to be the first to find a solution for the puzzle
 
P2P Consensus
 
Alice's Transaction to Bob's Cafe
 

 
What is Decentralized App?





	Organizationally Centralized	Organizationally Decentralized
Logically Centralized	Paypal	Bitcoin
Logically Decentralized	Excel	Email
 
Blockchain Smart Contracts





 
o A piece of code that lives on a blockchain, not a server.

o When a pre-programmed condition is triggered, the smart contract executes the corresponding contractual clause.

o No third party trust is required.

o No central point of failure.
 

IF ( user.sendsMoney(anotherUserId) ) {
runContract(); }
func runContract() { accountsCheck();b balanceCheck(); Transfer();
}
 
A Simple Smart Contract
 
Decentralized Governance Services


o The World's first blockchain-recorded marriage occurred at Disneyworld, Florida, on Oct 5, 2014.

o The vows were transmitted in the text annotation field, embedded in a Bitcoin txn of 0.1BTC ($32.50)
 
Decentralized Governance Services
 
Digital Art (Intellectual Property Protection)
