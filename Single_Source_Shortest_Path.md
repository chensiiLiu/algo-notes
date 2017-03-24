- Introduction

- 3 Solutions

  - Shortest Path on DAG

    Time: $O(m+n)$

  - Dijkstra's algorithm

    General method: $O(n^2)$, not bad when the graph is dense

    Heap: $O(mlgn)$, should be use when the graph is sparse

  - Bellman-Ford algorithm

## Introduction

- Input: directed graph G = (V, E)

   	    cost function: cost(u, v) is the cost on edge from u to v

  ​	    source vertex S

- Output: distance array, dist[1…n], dist[u] is the shortest distance from s to u

  ​               parent array, parent[1…n], used to generate the shortest path

- The proper algorithm depends on:

  - Is this graph a DAG (Directed acyclic graph)?
  - Does G  have negative weighted edges?	

There are 3 ways to solve this problem, choose one according to the situation:

- DAG, no matter negative edges or not:

  Use "Shortest path on DAG"

- Not DAG, no negative edges:

  Dijkstra's algorithm

- Not DAG, negative edges:

  Bellman-Ford algorithm

## 3 Solutions

#### Shortest Path on DAG

Use **topological sort** and look at the result. Any point in the right of 'u' is not related to the value of dist[u], according the property of topological sort.

```
relax(u, v, dist[], parent, cost[]) {
  if (dist[v] > dist[u] + cost[u, v]) {
    dist[v] = dist[u] + cost[u, v];
    parent[v] = u; 
  }
}

SPonDAG(G, S) {
  for (u in topologicalOrder(S)) {
    for (v in neighbors(u)) {
      relax(u, v, dist[], parent[], cost[]);
    }
  }
  return dist[], parent[];
}
```

**Time complexity: **$O(m+n)$

*NOTE:* 

1. Do know how to generate the path
2. When the 'relax' method comes to u, the dist[u] must be the shortest distance.

#### Dijkstra's algorithm

```
Set Z = { };
while (Z.length != n) {
  pick vertex u with smallest dist[] value, where u isn't in Z
  Insert u into Z
  for (v in neighbors(u)) {
    relax(u, v);
  }
}
```

**Time: ** $O(n^2)$

*NOTE:*

1. When you select u as the vertex to insert, then dist[u] has already been the shortest distance. It's easy to prove. 

2. What if the graph is sparse? The algorithm can have better performance. 

   The process of this algorithm should remind us of **heap**

   If we use heap to represent the dist[], then the three steps in the while loop becomes: 

   1. get the smallest one in min heap. $O(1)$

   2. insert the 

      这个地方要去问一下