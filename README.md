# Network-Creator

## Purpose
We want to create a network of people in which nodes represent people and edges between nodes represent relationships between people. Our goal, through this network, is to capture various properties about people and their relationships. To this end, we will construct and maintain a graph-based data structure called Network.


### The Network Data Structure
-The Network data structure uses two other structures called Node and Edge. We first define the structure of Node and Edge. 
-The Node data structure represents a person with a name and is defined as follows:
struct Node {
   int K            // non-negative integer K
   char* name[K]   // null-terminated string of K characters
}

-The Edge data structure represents a relationship between two people. It is defined as follows:
struct Edge {
  Node* p1	     // address of a node p1
  Node* p2	    // address of a node p2
  int relation	// non-negative integer to indicate type of relationship
}

Notice that Edge does not contain the actual person nodes in a relationship, but a reference to (or address of) the nodes that are related. Relationships are bidirectional, i.e., if p1 is related to p2 then p2 is also related to p1. The relation attribute/property is 1 if the relationship is a friendship. It is 2 if the relationship is an acquaintance. It is 3 if the relationship is family. It is 0, if the relationship type is unknown.

Further, the nodes and the edges connecting the nodes are captured in a graph-like structure called Network defined as follows:
struct Network {
  int N            // Total no. of nodes possible in the network.
  int E            // Total no. of edges possible in the network.
  int X            // no. of nodes currently in the network.
  int Y            // no. of edges currently in the network.
  Node* nodes[N]   // An array of node addresses.
  Edge* edges[E]   // An array of edge address.
}

## There are also functions within this program that will allow us to implement the social network

### Network* create_network(int I, int J)
The function create_network takes two integers, I and J, as arguments in registers $a0 and $a1. I is the total no. of nodes possible in the network and J is the max no. of edges in the network. Both I and J must be non-negative numbers. Otherwise, the function returns -1 in register $v0. If I and J are non-negative, the function instantiates the network structure in the heap and returns the address of the network in register $v0. The initial value of X and Y is 0 in the initial instantiation of the network. Further, the nodes and edges arrays should also be initialized with 0.

For e.g., if create_network(5, 10) then a block of 76 bytes should be allocated in the heap as the network will have 5 nodes and 10 edges. This is how the structure will look like in the heap:



###Node* add_person(Network* ntwrk, char* name)

The function add_person takes the address of a Network structure, ntwrk, in register a0 creates a person node with the null-terminated string name in register a1 (note char* is C-syntax for string), adds it to the network, and returns the network's address in register v0 and 1 in register v1.

Creating a person involves instantiating a Node structure in the heap, incrementing the current no. of nodes in the network by 1, and storing the Node's heap reference in the nodes array in the network.

The function returns -1 in registers v0 and v1 without changing anything in the memory (including the heap) in the following cases:

-If the Network is at capacity.
-If an empty string is passed as the name of a person.
-If a person with the same name exists in the network.


### Node* get_person(Network* network, char* name)

The function get_person takes two arguments -- a reference to Network in $a0 and a string name in $a1. The string name is null-terminated. The function should return a reference to the person node in Network that has its name set to the input argument name in register v0 and 1 in register v1. If no such person is found, then the function should return -1 in register v0 and register v1.


 ### Network* add_relation(Network* ntwrk, char* name2, char* name2, int relation_type)

The function add_relation takes a reference to Network in $a0, two null-terminated strings, name1 and name2, in $a1 and $a2, and an integer in $a3. If name1 and name2 exist in the Network, then an edge between person1, with name1, and person2, with name name2, and the appropriate relation type should be created in the heap based on the Edge structure defined previously. A reference to this edge should be added to the edges array in network. The function should return the network address in register v0 and 1 in register v1 if the relation was added successfully to the network.

The function fails to add the relation if:
-If no person with name1 exists in the network, or
-If no person with name2 exists in the network, or
-The network is at capacity, that is, it already contains the maximum no. of edges possible, or
-A relation between a person with name1 and a person with name2, or vice-versa already exists in the network. Relations must be unique, or
name1 == name2. A person cannot be related to themselves.
-If 0 > relation_type > 3
The function must return -1 in registers v0 and v1 if the relation could not be added to the network.



### FriendNode* get_distant_friends(Network* ntwrk, char* name)

A person p1 is a distant relation of p2 if p1 and p2 are not immediate friends, that is, there is no edge between p1 and p2 but there is a path in the network connecting p1 and p2 and the path has more than one edge.  For example, consider a network of 6 persons p1, p2, p3, p4, p5, p6. Suppose the friendships in the network are captured as follows:

    p1   p6
    /\  /
  p2  p3
  /    \
p5      p4




Note two people in the network can be related to each other but may not be friends. For instance, in the network above, suppose p1 and p3 are siblings instead of friends then p1 and p4 are not distant friends even if there is a path connecting p1 and p4 via p3.

Assume the network passed as argument to the function is a connected graph.

The function get_distant_friends takes a reference to the network in $a0 and a null-terminated string indicating person name in $a1. It returns a linked list of friend nodes in register v0, where each node is a distant friend of a person with name. It returns -1 in v0 if the person with name has no distant friends. It returns -2 in v0 if the person with name does not exist in the network.

A linked list is a linear collection of elements where each element points to the address of the next element in memory. To create a linked list of distant friend nodes, use the following structure.

struct FriendNode {
  char* name
  FriendNode* friendnode
}

Create a linked-list structure with FriendNodes in the heap and return a reference to the head of the linked list, i.e., the first element.

The first attribute in FriendNode is the name of the person who is a distant friend. The second attribute is a reference to (or address of) the next distant friend node. In the example network shown above, suppose the name of person node p1 is passed as argument to get_distant_friends along with the network address. In this case, the function should return a linked list of friend nodes in $v0 such that $v0 has a reference to a FriendNode fnode1, which has the name for p4 and a reference to a FriendNode fnode2. The FriendNode fnode2 has the name of p5 and a reference to fnode3. FriendNode fnode3 has the name of p6 and null as the reference to the next element. The null element indicates the end of the linked list. The elements in the linked list do not have to be linked in the same order as specified in the current example. For instance, the linked list returned could also be linked as p5 -> p4 -> p6.




