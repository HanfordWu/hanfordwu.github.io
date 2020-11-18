### Main methods of a Graph

- Accessor methods
  - endVertices(e): Return an array of the two end vertices of e
  - opposite(v, e): Return the vertex opposite of v on e; error if e is not incident of v
  - areAdjacent(v, w): return true if v and w are adjacent; false otherwise
  - replace(v, x): replace element at vertex v with x
  - replace(e, x): replace element at edge e with x
- Update methods
  - insertVertex(x): insert and return a new vertex storing element x
  - insertEdge(v, w, x): insert and return a new edge (v,w) storing element x
  - removeVertex(v): remove vertex v and all its incident edges, and return the element stored at v
  - removeEdge(e): remove edge e and return the element stored at e
- Iterable collection methods
  - vertices(): return an iterable collection of all the vertices of the graph
  - edges(): return an iterable collection of all the edges of the graph

### Data Structures for Graphs

There are three popular ways of representing graphs, which are referred to as:
- Edge List Structure 
- Adjacency List Structure 
- Adjacency Matrix Structure

All three use a collection to store the vertices of the graph

However, for edges, there is a fundamental difference between the first two structures and the latter.

The first two structures only store edges **actually** present in the graph, resulting in a **O(n + m)** space complexity (n vertices and m edges)

The adjacency matrix structure stores an edge **placeholder for each** pair of vertices whether or not the edge exists, resulting in a **O(n2)** space complexity.




### Edge List Structure

- In this representation of a graph, both vertices and edges are represented as objects

- The vertices objects are stored in a collection/sequence V, and the edges are stored in another collection E

- V and E can be array lists, node lists or other types

- Each entry in V points to a vertex object

- Each entry in E points to an edge object

- A vertex object stores the value of that vertex and a pointer to its location in V

- An edge object stores the value of that edge, two pointers to its two vertices, and a pointer to its location in E

![alt](https://i.imgur.com/GaqQB0r.png)

Vertex sequence:
- sequence of vertex objects


Vertex object:
- element
- reference to position in vertex sequence

Edge object
- element
- origin vertex object
- destination vertex object
- reference to position in edge sequence

Edge sequence:
- sequence of edge objects


Edge List Structure Analysis:

1. Edge list is simple to implement, however it has significant performance limitations
2. Finding the end points of an edge, or the opposite vertex of an edge is O(1)
3. The insertion of a vertex, the insertion of an edge, as well as the removal of an edge is also O(1)
4. However, to find the incident edges of a vertex v, a complete search of the edges list is required, resulting in O(m) instead of O(deg(v))
5. Finding if a vertex w is adjacent  to a vertex v  would also require searching all edges to find an edge with endpoints v and w, resulting in O(m)
6. Removing  a vertex would also result in O(m), since an entire search of the list is required to remove all incident edges of that vertex.

### Adjacency List Structure

The adjacency list structure includes all the structural components of the edge list structure plus: 
  - A vertex object v holds a reference to a collection, referred to as the incidence collection of v or l(v). The incidence collection of v is traditionally a list whose elements store references to the edges incident on v.  

  - The edge object for an object e connecting vertices v and w has an additional two references, which reference the two elements associated with that edge e in l(v) and l(w). This allows for finding all incident edges of a vertex from an edge to that vertex.  

  - The space utilization is hence still proportional to the number of vertices and edges of the graph

![alt](https://i.imgur.com/f0He9lt.png)



### Adjacency Matrix Structure

The adjacency matrix structure includes all the structural components of the edge list structure plus: 

- The vertex object is extended to include a distinct integer i in the range [0 – N-1], which is referred to as the index of v
 
- A matrix, which is a two-dimensional array A of size n x n, such that  A[i, j] holds a reference to the edge (v, w), where v is the vertex with index i and w is the vertex with index j. If the edge (v, w) does not exist, then A[i, j] holds null.

![alt](https://i.imgur.com/JAGldgD.png)


Historically, Adjacency Matrix was proposed first to represent graphs. 
With the exception of areAdjacent(), Adjacency List performs best compared to the other two structures.