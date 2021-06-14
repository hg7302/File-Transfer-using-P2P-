# File-Transfer-using-P2P-
Node.py Program

Since this is a decentralized system, there are no separate server and client scripts. Instead each Node.py script acts as both a server and a client therefore allowing p2p connections to other Nodes. Any Node can join the network but initially it must know the IP and Port of another Node that is already part of the Chord network.

To Node.py requires command line arguments in the form: python Node.py IP Port
For example: python Node.py 127.0.0.1 5000

All subsequent Nodes also start in the same way.
Remember: A Node can have the same IP as another, but not the same IP and same Port.
Example to Get Started

Disclaimer: The IP and Port numbers are just for illustration. You can use a different combination.

The first Node of the Chord network will be initialized in same way as stated above.
To reiterate, we begin in the Node.py directory and run the command:
python Node.py 127.0.0.1 5000
Lets call this Node 1 for this example.

You start one or more subsequent Nodes in the same way by providing each with an IP Port combination. Lets call them Node 2, Node 3 and so on.

When the nodes start, you will be displayed with a number of options:
Join

Now you can connect one Node to another in any way. Once you choose to join the network, you can use any other's Node's IP Port combination to join the network. If you go to Node 2, choose join network and type in Node 1's IP Port. Both Node 1 and Node 2 will be now be joined and part of the network. Similarly, other Nodes can join.
Leave

Nodes can leave the network by informing the Chord and hence other Nodes of its departure. Note: The Chord also supports uninformed disconnection/failure of nodes, however informed leaving is still preferred.
Upload and Download File

Type the name of the file present in the same directory. Uploading file will send the file to the relevant Node based on hash of filename. Downloading will be done from the relevant Node with the file. Chord supports large file transfer by sending file in chunks (based on buffer size).
System architecture and design

Finger Table: 
This table stores information about other nodes in the system; each entry contains a node identifier and its network address (consisting of an IP address and a port number). Chord implements a faster search method by requiring each node to keep a finger table containing up to m entries, m is the number of bits in the hash key.


The i{th} entry of node n will contain successor((n+2^{i-1}) mod 2^m). The first entry of finger table is actually the node’s immediate successor (and therefore an extra successor field is not needed). Every time a node wants to look up a key k, it will pass the query to the closest successor or predecessor (depending on the finger table) of k in its finger table (the “largest” one on the circle whose ID is smaller than k), until a node finds out the key is stored in its immediate successor. With such a finger table, the number of nodes that must be contacted to find a successor in an N-node network is O(log N).
Node Join: Chord needs to deal with nodes joining the system concurrently and with nodes that fail or leave voluntarily. A basic stabilization protocol is used to keep nodes’ successor pointers up to date, which is sufficient to guarantee the correctness of lookups. Those successor pointers are then used to verify and correct finger table entries, which allows these lookups to be fast as well as correct.


If joining nodes have affected some region of the Chord ring, a lookup that occurs before stabilization has finished can exhibit one of three behaviours. 1. The common case is that all the finger table entries involved in the lookup are reasonably current, and the lookup finds the correct successor in O(log N) steps. 2. The second case is where successor pointers are correct, but the fingers are inaccurate. This yields correct lookups, but they may be slower. 3. In the final case, the nodes in the affected region have incorrect successor pointers, or keys may not yet have migrated to newly joined nodes, and the lookup may fail. Stabilization scheme guarantees to add nodes to a Chord ring in a way that preserves reachability of existing nodes, even in the face of concurrent joins and lost and reordered messages Whenever a new node joins, three invariants should be maintained (the first two ensure correctness and the last one keeps querying fast):
1.Each node’s successor points to its immediate successor correctly.
2.Each key is stored in successor(k)
3.Each node’s finger table should be correct.

3. Stabilization:
To ensure correct lookups, all successor pointers must be up to date. Therefore, a stabilization protocol is running periodically in the background which updates finger tables and successor pointers.The stabilization protocol works as follows:
Stabilize(): n asks its successor for its predecessor p and decides whether p should be n‘s successor instead (this is the case if p recently joined the system).
Notify(): notifies n‘s successor of its existence, so it can change its predecessor to n.
Fix_fingers(): updates finger tables.
check_predecessor(): Periodically checks in predecessor is alive
 4. IMPLEMENTATION
       Fault Tolerance: To ensure reliability of network any node which  is leaving the network will ensure that system is not reaching in an unstable state.The node which is leaving the network will ping the successor and transfer all the files if any uploaded to the node will be transferred to its successor ensuring reliability in the network.
Replication: Each file across the node replicated to the next successor when uploading a file to the node so that in case of any failure of node and if by any chance file is not transferred or the node becomes a failure, the file is being available to its successor node ensuring data reliability across the network.
