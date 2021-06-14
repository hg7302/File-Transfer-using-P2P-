# File-Transfer-using-P2P-
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
