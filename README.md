# Maximum Capacity Path Performance Analysis
An attempt to improve the classical algorithms using modern data structures

##1. Introduction 
This report provides the details of the implementation of the project implemented for Max Capacity Path. The implementation can be analyzed on three aspects. First the programming paradigm used, secondly usage of efficient data structures of the native language or custom implementation of a popular data structure and lastly the performance analysis of the implementation. 

##2. Defining the Problem Statement 
The problem statement is to find the Max-Capacity Path for a set of Dense and Sparse Randomly generated Graphs. Constructing a Max-Capacity or popularly called Maximum Bandwidth Path has been a basic operation in the study of network QoS routing. It is the problem of finding a path between two designated vertices in a weighted graph, maximizing the weight of the minimum-weight edge in the path. In our implementation, we have used three algorithms to find Maximum Capacity Path for Dense and Sparse Graphs. These algorithms are Dijkstra’s modified algorithm to find Maximum Capacity Path using and without using Heaps respectively and lastly using the Kruskal’s modified algorithm to find the Maximum Spanning Tree. In the Kruskal’s modified version, we get a Maximum Spanning Tree which has the edge that contains the maximum weight. We will extract our path between the source and destination from this Maximum Spanning Tree structure. For random graph generation, we have a constraint related to the number of vertices and the vertex degree. For each graph, number of vertices is 5000. For the Sparse Graph the degree should be fixed i.e. exactly 6. On the other hand in the case of Dense Graph, the vertex degree should be near to 20 percent i.e. nearly 1000. The randomizer for such a sample set can create a disconnected graph sets in place of a single graph. In that scenario our path finding algorithms will fail to resolve that issue we will create a path between the source and the destination travelling through the remaining vertices before testing the path finding algorithms. This will make sure that we will get atleast one path between the source and destination. 

##3. Environment and Programming Language Details 
The solution has been made in Java programming language. The latest stable version of Java has been used, which is Java 8 update 25. The operating system used for development is Windows 8.1. 

##4. Programming Implementation Details 
The solution has been made using object oriented concept. The solution has five classes. Each of the class contain variables and methods important for devising the solution of the problem. Now we will discuss the above classes in details. Following are the classes in the solution.
####1. Graph
This class stores the graph representation in the form of an adjacency matrix. The adjacency matrix has fast retrieval in terms of the edge lookup. The overhead for adjacency matrix is the memory. In our scenario, memory is not a restriction so we have chosen the adjacency matrix representation. This class also stores an array which contains the degree count of each vertex. This array plays an important role when the randomizer code snippet runs to generate a random graph. In that scenario, this array keep a track of the vertices whose maximum degree has been achieved and also those whose not have been achieved during the process. The array helps the randomizer to choose only those vertices whose maximum degree is yet to be achieved and helps in keeping a check on the graph connectivity. Also there are two more variable one which defines the maximum degree count for all the vertices and second is a list of Edges which will be utilized when Kruskal’s algorithm will be implemented. 
The methods of this class or the subroutines of this class has implementation to initialize, update and delete values of the variables of this class. One of the most important method is addEdge. This method adds an edge in the graph by taking the edge vertices and edge weight as its parameters and subsequently updating values in the adjacency matrix. Another important method is getEdgeWeight which return the edge weight values between two nodes. In case there is no edge, it returns zero. Apart from these two important methods there are helper methods to set and return the maximum degree count value, return edge list and also return total vertices of the graph. There is also a method for clearing the graph attributes values which we will discuss in the randomizer methodology. 
####2. Vertex
This is a simple class having the two variable which is vertexName (which is the vertex identifier) and distance (which stores the relative distance of the vertex from the source). It has methods to set and return values of the two variables. Apart from this, there is another method to compare two vertices on the basis of the relative distance from the source. This method will be helpful in choosing the right vertex during Dijkstra’s algorithm implementation. 
####3. Edge
This is a simple class having the two variable which has three variables namely firstVertex, secondVertex and edgeWeight. As evident from the name of the variables, this class stores the connecting vertices and the weight of the edge. It has methods to set and return values of these variables. Apart from this, there is another method to compare two edges on the based on the value of edge weight. This method and this class play significant role in the Kruskal’s algorithm where sorted edges are processed one after the other. 
####4. GraphUtilities
It is the most important class of the solution since it has all the algorithmic implementations and the working logic for the program. This class has all static methods. It takes the objects created from the previously three mentioned classes and processes the data and generates desired output. Now the important methods of this class are following : 
#####denseGraphGenerator
This method generates a random graph of 5000 vertices with nearly 20 percent connectivity. The working logic for this implementation is to choose a random vertex and check its degree count. If it is less than the maximum degree then choose other random vertices to create edges between them. Till all the vertices are exhausted. This logic has not been kept in an unbounded loop because there can be condition where there are set of vertices connected to each other but each their vertex degree is not 20 and there is no vertex left whose vertex degree is less than 20. This will be a racing condition where such vertices will keep finding other vertices to full fill their degree count and an infinite loop will follow. To avoid such a condition, I have followed a logic to try to fill the vertex degree of an individual vertex. If we are able to do that then the degree count will improve otherwise move ahead to fill the vertex degree of another vertex with remaining vertices in a serial order of one pass only. In this case there will be vertices which will be few vertices left which will have their degree slightly less than 20 but such vertices will be very low. 
￼ 
######sparseGraphGenerator
Similar to the method of dense graph generator, this method also creates a random graph but with a degree of exact six. To accomplish this logic of achieving exact degree of six, we have to go for an unbounded loop until the degree of each vertex is satisfied. In this scenario as well, the race condition can happen where a connected set of vertices left (all having degree less than 6). They cannot connect themselves more as they are already connected and there are no other vertices left having degree less than 6. In that case, graph cannot be generated. Upon testing on a large sample spaces I observed that if the randomizer is able to generate this sequence in nearly 5000 steps (out of which many will be wrong set of vertices having their vertex degree exhausted). I have taken a reset counter on 10000 steps. If the randomizer is able to produce the graph in less than 10000 steps then we are will accept this graph otherwise we will discard this semi generated graph and start from scratch until we create a graph with exact 6 vertex degree each. On an average out of 10 attempts, the randomizer successfully generates graph 7 times and 3 times it goes into a condition where it get stuck with the condition where it is not able to satisfy the vertex count of few vertices. As we have added a checked of Graph reset for those 3 cases, every time we are able to get a random graph with an expense of extra time for the three cases. In this way, we are not getting stuck with graphs of inappropriate degree count.  
######dijkstraMCPath
This method is a modified version of the Dijkstra’s shortest path algorithm to find the path of maximum capacity. This version do not use heap structures. It uses a Java’s native data structure called SortedSet which is a highly optimized data structure for storing objects or primitive data type where order is to be maintained at every insert or delete operation. To establish the fact that SortedSet is a best non heap implementation, I tested other native Java data structures (formally called Collections) on even larger data sets compared to our problem. SortedSet proved as the most efficient mechanism to maintain ordered list compared any other data structure in Java or even the custom implementations. The algorithm for modified Dijkstra is presented on the next page. The need of a sorted list in the second line of Step 4, leads to usage of SortedSet. In this method we are storing the maximum capacity for each vertex in a array named d. The backtracking of the path between the source and the destination can be done using another array named dad. We vertex names and their indexes in this array can be interchanged to perform the backtrack the resultant path.

_Modified Dijkstra’s Algorithm Assumption_
* -infinity = −∞
* Inputs: Set V (vertices of graph G), Set E (The weighted edges of graph G) 
* Start vertex: source vertex s and destination vertex: t
* Output: The maximum capacity path from s to any vertex in G, including t.

```python
Step 1. for each vertex v = 1 to n do status[v] = unseen 
                                 d[v] = -infinity
        Step 2. status[s] = intree,
                         dad[s] = 0, d[s] = 0
        Step 3. for each edge[s,w] do
                           status[w] = fringe
                           dad[w] = s
                           d[w] = weight[s,w]
Step 4. while there are fringes do Let v be the fringe with max d[v] 
                                 status[v] = intree
                                 foreach edge[v,w] do
                                         if status[w] = unseen then
                                                 status[w] = fringe
						 dad[w] = v 
						d[w] = Min(d[v], weight[v,w])
					else if status[w] = fringe and d[w] < Min(d[v], weight[v,w]) then 
                                                 d[w] = min(d[v], weight[v,w])
                                                 dad[w] = v
```
######dijkstraMCPathUsingHeap
This method is the implementation of the Dijsktra’s modified algorithm of finding Maximum capacity path using heap. Its implementation is same as the previous algorithm except in place of SortedSet, we are using a Heap. This Heap is a Max-Heap and has been custom implemented. The Heap itself utilizes methods named insertMaxHeap which in itself uses methods named maxHeapify to maintain the heap structure after every insertion or deletion of an element. The heap structure is an array based heap structure.

######kruskalMCPath
This is the last important method of class GraphUtilities. It implements a modified version of Kruskal’s algorithms to obtain a maximum spanning tree. This maximum spanning tree has an important output which is an array named dad that can be utilized to track the path between source and destination. It runs on the same principle of creating making sets of connected vertices and doing a union for set of vertices when we are connecting them with an edge. The edges are sorted using Heap Sort in descending order. The heap sort implementation is a custom implementation. It has an improvisation by implementing the path compression which flattening the structure of the tree whenever Find is used on it. We have created a separate method named printMCPath, if we are interested in printing the entire path between the source and the destination. In the executable, it is being utilized.

####MainProgram
This is the primary class which creates the objects of the classes Graph, Vertex and Edge then it utilizes the static method of class GraphUtilities to run the random graph generators and the maximum path finding algorithms. 

##6. Performance Analysis 
Below is the statistical data for the testing of the three algorithms for pair of Dense and Sparse Graphs. Each of the algorithm has been tested with 25 different graphs of dense and sparse type each. 



####Running statistics for Dense Graph: 
|              | Dijkstra's Algorithm (Without Heap) | Dijkstra's Algorithm (Using Heap) | Kruskal Algorithm (Using Heap Sort) |
|:------------:|:-----------------------------------:|:---------------------------------:|:-----------------------------------:|
| Average Time | 17.56646 ms               | 182.07786 ms            | 804.42791 ms              |
| Worst Time   | 48.65868 ms               | 427.36360 ms            | 1494.99238 ms             |

####Running statistics for Sparse Graph

|              | Dijkstra's Algorithm (Without Heap) | Dijkstra's Algorithm (Using Heap) | Kruskal Algorithm (Using Heap Sort) |
|:------------:|:-----------------------------------:|:---------------------------------:|:-----------------------------------:|
| Average Time |             19.53023 ms             |             76.93209 m            |              6.20184 ms             |
|  Worst Time  |             36.74146 ms             |            146.98471 ms           |             22.13404 ms             |
 

￼ ￼ ￼
From the above statistics, we can conclude some important remarks. Regarding Dense Matrix we can see the Dijkstra’s Algorithm (Without Heap) is performing significantly better because of the usage of efficient SortedSet. Kruskal’s algorithm is slowest on a dense graph because the possibility of multiple maximum spanning tree increases due to excessive number of edges. The running time of Dijkstra’s algorithm without heap is nearly 10 times faster than the Dijkstra’s algorithm with Heap and more than 30 times faster than the Kruskal’s algorithm 
On the other hand, when we observe the findings of the Sparse Graph, we find that Kruskal’ algorithm is working extremely fast. This is possible as the due to less number of edges compared to dense graph, the steps of creating a maximum spanning tree in a connected graph decreases as number of checks will reduce to seek the possibility of multiple paths. Coming back to Dijkstra’s algorithm without heap, we can 
observe it is not as efficient as Kruskal’s algorithm running time in this scenario but is a close enough value. In most of the cases the Dijkstra’s Algorithm without heap as almost 1.5 – 3 times the Kruskal’s algorithm running time. Dijkstra’s Algorithm without Heap is nearly 4 times faster than the implement using Heap. 
If we observe the asymptotic time complexity analysis of the three algorithms, we can state that for Dijkstra’s (Without Heap) it is O(nlogm +m), for Dijkstra’s (Using Heap) it is O(mlogn +nlogn) and for Kruskal’s algorithm it is O(mlog*m +mlogn) 

##7. Improvements done and future scope 
In the case of sparse Graph, the Dijkstra’s Algorithm using heap performed slowest while in the case of dense graph, Kruskal’s Algorithm performed slowest. The improvement which was done is on Union Find of Kruskal’s Algorithm in which used the path compression for Union rank. An improvement which can be done in future related to the Heap Sorting (for Kruskal’s algorithm) and Heap maintenance (for Dijkstra’s heap Algorithm). We may not be able to improve the basic algorithms but we can attempt different native unsynchronized data structures of Java 8 which uses highly optimized structures inherently to improve the resultant time of the algorithms. Complexity will remain the same but the net running times will reduce if we identify the appropriate collections and choose them after testing on extremely large, large and smaller sample sets compared to the existing problem. 
##8. References 
* Knuth D.E The Art of Computer Programming-Sorting and Searching.2nd Edition (Addison Wesley),220- 256
* A.V. Aho, I.E. Hopcroft and J.D. Ulbnan, The Design and Analysis of Computer Algorithms (Addison- Wesley, 1974), 124—139 
