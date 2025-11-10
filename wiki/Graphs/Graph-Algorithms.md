# Graph Algorithms

Repository: https://github.com/pharo-ai/graph-algorithms

Graphs algorithms are very useful for different types of problems. We also have a great booket [Booklet-PharoGraphs](https://github.com/SquareBracketAssociates/Booklet-PharoGraphs) in which we go into details of the logic of the algorithms and its implementation in Pharo.

### Table of Contents

- [Implemented graph algorithms](#implemented-graph-algorithms)  
- [How to use the graph algorithms - API](#how-to-use-the-graph-algorithms---api)  
- [Graph generation algorithms](#graph-generation-algorithms)  

## Implemented graph algorithms

Currently, we have these algorithms implemented:

  - Topological Sorting
  - Shortest Path
	+ BFS (Breadth First Search)
	+ DFS (Depth First Search)
	+ Dijkstra's Algorithm
	+ in DAG (Directed Acyclic Graph)
	+ Bellman-Ford's Algorithm (for DCG, Directed Cyclic Graph)
	+ Floyd-Warshall's Algorithm
	+ A* Algorithm
  - Longest Path
	+ in DAG (variation of shortest path in DAG)
	+ in DCG (variation of Bellman-Ford's Algorithm)
  - Minimum Spanning Tree
	+ Kruskal’s Algorithm
	- Prim’s Algorithm
  - Strongly Connected Components
	+ Tarjan's Algorithm
    + Graph Reducer
  - Maximum Flow
	+ Edmonds-Karp's Algorithm
	+ Dinic's Algorithm
  - Link Analysis
	+ HITS (Hyperlink-Induced Topic Search) Algorithm
  - Matching (Independent Edge Set)
	+ Maximum-weight Matching Approximation Algorithm. With variations for minimum-weight and maximum-cardinality
	+ Stable Matching Algorithm (for the so-called marriage problem)

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
