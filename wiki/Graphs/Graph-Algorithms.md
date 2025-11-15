# Graph Algorithms

Repository: https://github.com/pharo-ai/graph-algorithms

Graphs algorithms are very useful for different types of problems. We also have a great booklet [Booklet-PharoGraphs](https://github.com/SquareBracketAssociates/Booklet-PharoGraphs) in which we go into details of the logic of the algorithms and its implementation in Pharo.

### Table of Contents

- [Implemented graph algorithms](#implemented-graph-algorithms)  
- [How to use the graph algorithms - API](#how-to-use-the-graph-algorithms---api)  
- [Graph generation algorithms](#graph-generation-algorithms)
- [Comparison of the implemented algorithms](#comparison-of-the-implemented-algorithms)
- [External links](#external-links)

## Implemented graph algorithms

Currently, we have these algorithms implemented:

- Topological Sorting
  + Kahn's Algorithm
- Shortest Path
  + BFS (Breadth First Search)
  + DFS (Depth First Search)
  + Dijkstra's Algorithm
  + in DAG (Directed Acyclic Graph)
  + Bellman-Ford Algorithm (for DCG, Directed Cyclic Graph)
  + Floyd-Warshall Algorithm
  + A* Algorithm
- Longest Path
  + in DAG (variation of shortest path in DAG)
  + in DCG (variation of Bellman-Ford's Algorithm)
- Minimum Spanning Tree
  + Kruskal’s Algorithm
  + Prim’s Algorithm
- Strongly Connected Components
  + Tarjan's Algorithm
  + Graph Reducer
- Maximum Flow
  + Edmonds-Karp Algorithm
  + Dinic's Algorithm
- Link Analysis
  + HITS (Hyperlink-Induced Topic Search) Algorithm
- Matching (Independent Edge Set)
  + Maximum-weight Matching Approximation Algorithm 
    (with variations for minimum-weight and maximum-cardinality)
  + Gale-Shapely's Stable Matching Algorithm (for the so-called marriage problem)

## How to use the graph algorithms - API

All the graph algorithms of this library share a common API also. The class AIGraphAlgorithm provides the common API to add nodes, edges, searching the nodes, etc.

Some of the common methods are:

- `algorithm nodes:`
- `algorithm nodes`
- `algorithm edges`
- `algorithm edges:from:to:`
- `algorithm edges:from:to:weight:`
- `algorithm findNode:`
- `algorithm run`

For example, for using the topological sort algorithm for the following graph, we can run this code snippet

<img src="https://user-images.githubusercontent.com/33934979/144241102-639f4ff8-6bc2-41ad-9082-ea6e8dada306.png" width="300" />

```st
"First define the nodes and the edges"
nodes := #( $A $B $C $D $E $F $G ).
edges := #( #( $A $B ) #( $A $C ) #( $B $E ) #( $C $E ) #( $C $D )
    #( $D $E ) #( $D $F ) #( $E $G ) #( $F $G ) ).

"Instantiate the graph algorithm"
topSortingAlgo := AITopologicalSorting new.

"Set the nodes and edges"    
topSortingAlgo nodes: nodes.
topSortingAlgo
    edges: edges
    from: [ :each | each first ]
    to: [ :each | each second ].

"Run to obtain the result"
topologicalSortedElements := topSortingAlgo run.
```

Or if we want to find the shortest path in a weighted graph:

<img src="https://user-images.githubusercontent.com/33934979/144241616-8cc92bf7-959b-4f47-817d-6d1cd2f3cf70.png" width="300" />

```st
nodes := $A to: $F.
edges := #( #( $A $B 5 ) #( $A $C 1 ) #( $B $C 2 ) #( $B $E 20 )
    #( $B $D 3 ) #( $C $B 3 ) #( $C $E 12 ) #( $D $C 3 )
    #( $D $E 2 ) #( $D $F 6 ) #( $E $F 1 ) ).

dijkstra := AIDijkstra new.
dijkstra nodes: nodes.
dijkstra
    edges: edges
    from: [ :each | each first ]
    to: [ :each | each second ]
    weight: [ :each | each third ].

shortestPathAToB := dijkstra runFrom: $A to: $B.
pathDistanceAToB := (dijkstra findNode: $B) pathDistance.

dijkstra end: $F.
shortestPathAToF := dijkstra reconstructPath.
pathDistanceAToF := (dijkstra findNode: $F) pathDistance.

dijkstra reset.
shortestPathBToE := dijkstra runFrom: $B to: $E.
```

## Graph generation algorithms

This library also contains algorithms for generating regular and random graphs. This algorithms are not loaded by default. To load them, you can either load them manually using [Iceberg directly from the Pharo image](https://github.com/pharo-vcs/iceberg/wiki/Tutorial) or load the `GraphGenerators` baseline group.

The algorithms implemented are:

- Albert Barabasi Graph Generator
- Atlas Graph Graph Generator
- Erdos Renyi GNM Graph Generator
- Erdos Renyi GNP Graph Generator
- Grid 2D Graph Generator
- Grid 3D Graph Generator
- Hexagonal Lattice Graph Generator
- Kleinberg Graph Generator
- Triangular Lattice Graph Generator
- Waltz Strogatz Graph Generator

## Comparison of the implemented algorithms

This section summarizes for each provided graph algorithm in the library its input, its output, some remark and the implemented time complexity. 

**Abbreviations**: DAG means Directed Acyclic Graph, DCG means Directed Cyclic Graph, DG means DG Directed Graph (cyclic or not). V represents the number of vertices and E the number of edges.

### Topological sorting

| Algorithm | Input | Output       | Remark                 | Complexity |
| --------- | ----- | ------------ | ---------------------- | ---------- |
| Kahn's    | DAG   | sorted nodes | working list iteration | O(V + E)   |

### Shortest path

| Algorithm      | Input                                          | Output                      | Remark                                                                                                                      | Complexity     |
| -------------- | ---------------------------------------------- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------- | -------------- |
| BFS            | unweighted DG and starting node                | path nodes                  | BFS in queue                                                                                                                | O(V + E)       |
| DFS            | unweighted DG and starting node                | path nodes                  | DFS in queue                                                                                                                | O(V + E)       |
| Dijkstra's     | DG with non-negative weights and starting node | path nodes                  | [dynamic programming](https://en.wikipedia.org/wiki/Dynamic_programming#Dijkstra's_algorithm_for_the_shortest_path_problem) | O(V²)          |
| in DAG         | weighted DAG and starting node                 | path nodes                  | based on topological sorting                                                                                                | O(V + E)       |
| Bellman-Ford   | weighted DG and starting node                  | path nodes                  | edge [relaxation](https://en.wikipedia.org/wiki/Relaxation_(iterative_method))                                              | O(V \* E)      |
| Floyd-Warshall | DG with no negative weight cycles              | path for every node pair    | data structure: two matrices                                                                                                | O(V³)          |
| A*             | DG, start and end node                         | path from start to end node | Extension of Dijkstra's algorithm                                                                                           | O(E \* log(V)) |

### Strongly connected components

| Algorithm     | Input                     | Output                         | Remark                          | Complexity |
| ------------- | ------------------------- | ------------------------------ | ------------------------------- | ---------- |
| Tarjan's      | Unweighted DG             | list of node lists             | based on DFS                    | O(V + E)   |
| Graph Reducer | Tarjan's algorithm output | nodes of the new reduced graph | no default reduction of weights | ?          |

### Maximum flow

| Algorithm    | Input                              | Output             | Remark           | Complexity |
| ------------ | ---------------------------------- | ------------------ | ---------------- | ---------- |
| Edmonds-Karp | weighted DG, source and sink nodes | maximal flow value | uses BFS         | O(V * E²)  |
| Dinic's      | weighted DG, source and sink nodes | maximal flow value | layering concept | O(V² \* E) |

### Link analysis

| Algorithm | Input | Output                                       | Remark                                  | Complexity |
| --------- | ----- | -------------------------------------------- | --------------------------------------- | ---------- |
| HITS      | DG    | hub score and authority score for every node | optional weights to improve performance | O(V + E)   |

### Minimum spanning tree

| Algorithm | Input                                            | Output                           | Remark                                                                                   | Complexity       |
| --------- | ------------------------------------------------ | -------------------------------- | ---------------------------------------------------------------------------------------- | ---------------- |
| Kruskal’s | undirected weighted graph (non-negative weights) | subset of edges (tree or forest) | [disjoint-set data structure](https://en.wikipedia.org/wiki/Disjoint-set_data_structure) | O(V \* log(E))   |
| Prim’s    | connected undirected weighted graph              | subset of edges (tree)           | very similar to Dijkstra's algorithm                                                     | O( V \* (V + E)) |

Both algorithms are  [greedy](https://en.wikipedia.org/wiki/Greedy_algorithm).

### Matching

| Problem          | Algorithm      | Input                                                       | Output                                           | Remark                             | Complexity     |
| ---------------- | -------------- | ----------------------------------------------------------- | ------------------------------------------------ | ---------------------------------- | -------------- |
| Maximum Matching | Graph Matching | undirected weighted graph                                   | independent edges set                            | greedy approximation               | O(E \* log(V)) |
| Stable Matching  | Gale-Shapely's | nodes in group A or B with preferences over the other group | set of edges, each one relating a different pair | groups A and B are of equal size n | O(n²)          |

## External links

Almost all items are well described in Wikipedia. For a couple of items, the brief posts in [Jiho's Blog](https://berryjune07.github.io/computer-science) are outstanding.

### Graph problems

- Topological sorting: [Wikipedia]([Topological sorting - Wikipedia](https://en.wikipedia.org/wiki/Topological_sorting))

- Shortest path: [Wikipedia](https://en.wikipedia.org/wiki/Shortest_path_problem)

- Minimum spanning tree: [Wikipedia](https://en.wikipedia.org/wiki/Minimum_spanning_tree), [Jiho's Blog](https://berryjune07.github.io/computer-science/minimum-spanning-tree.html), [Prim's vs Kruskal's (Geeks for geeks)](https://www.geeksforgeeks.org/dsa/difference-between-prims-and-kruskals-algorithm-for-mst), [Prim's vs Kruskal's (Baeldung)](https://www.baeldung.com/cs/kruskals-vs-prims-algorithm)

- Strongly connected components: [Wikipedia](https://en.wikipedia.org/wiki/Strongly_connected_component)

- Maximum flow: [Wikipedia](https://en.wikipedia.org/wiki/Maximum_flow_problem), [Edmond-Karp vs Dinic's](https://blog.truegeometry.com/api/exploreHTML/4a9efcd40aefb604cbe4a9ad70982c12.exploreHTML)

- Link analysis: [Wikipedia](https://en.wikipedia.org/wiki/Link_analysis)

- Matching: [Wikipedia](https://en.wikipedia.org/wiki/Matching_(graph_theory)), [Brilliant course](https://brilliant.org/wiki/matching-algorithms)

### Graph algorithms

- A*: [Wikipedia](https://en.wikipedia.org/wiki/A*_search_algorithm)

- Bellman-Ford's: [Wikipedia](https://en.wikipedia.org/wiki/Bellman–Ford_algorithm), [Jiho's Blog](https://berryjune07.github.io/computer-science/bellman-ford-algorithm.html)

- BFS: [Wikipedia](https://en.wikipedia.org/wiki/Breadth-first_search), [Jiho's Blog](https://berryjune07.github.io/computer-science/dfs-and-bfs.html#bfs)

- DFS: [Wikipedia](https://en.wikipedia.org/wiki/Depth-first_search), [Jiho's Blog](https://berryjune07.github.io/computer-science/dfs-and-bfs.html#dfs)

- Dijkstra's: [Wikipedia](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm)

- Dinic's: [Wikipedia](https://en.wikipedia.org/wiki/Dinic%27s_algorithm), [Geeks for geeks](https://www.geeksforgeeks.org/dsa/dinics-algorithm-maximum-flow)

- Edmonds-Karp's: [Wikipedia](https://en.wikipedia.org/wiki/Edmonds–Karp_algorithm)

- Floyd-Warshall's: [Wikipedia](https://en.wikipedia.org/wiki/Floyd–Warshall_algorithm), [Jiho's Blog](https://berryjune07.github.io/computer-science/floyd-warshall-algorithm.html)

- Gale-Shapely's: [Wikipedia](https://de.wikipedia.org/wiki/Stable_Marriage_Problem#Gale-Shapley-Algorithmus)

- HITS: [Wikipedia](https://en.wikipedia.org/wiki/HITS_algorithm)

- Kahn's: [Wikipedia](https://en.wikipedia.org/wiki/Topological_sorting#Kahn's_algorithm)

- Kruskal’s: [Wikipedia](https://en.wikipedia.org/wiki/Kruskal%27s_algorithm)
  
  - Disjoint-Set data structure: [Wikipedia](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)
  
  - Disjoint-Set Forest: [Jiho's Blog](https://berryjune07.github.io/computer-science/disjoint-set-forest.html)

- Matching Approximation: [Cornell lecture](https://www.cs.cornell.edu/courses/cs6820/2014fa/matchingNotes.pdf)

- Prim's: [Wikipedia](https://en.wikipedia.org/wiki/Prim%27s_algorithm)

- Tarjan's: [Wikipedia](https://en.wikipedia.org/wiki/Tarjan%27s_strongly_connected_components_algorithm)
