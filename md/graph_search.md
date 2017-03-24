- Graph representation
- BFS
- DFS
- Applications
  - Cycle Detection
  - Topological Sort
  - Strongly connected components
  - Word Ladders
  - Boggle
  - Puzzle 8

## Graph Representation

Definition: G = (V, E)

​	V = set of vertices

​	E = set of edges

Directed graph & Undirected graph

Two representations:

​	Adjacency matrix: edge test

​				space: $O(n^2)$, bad for sparse graph

​	Adjacency list: fast to traverse neighbors of a vertex

​				  space: $O(n + m)$

How to choose efficient representation:

​	The type of operation

​	Space complexity

## BFS

```
BFS(G, S) {
  Queue q;
  visited[1...n];
  q.enqueue(S);
  visited[S] = T;
  while (!q.isEmpty()) {
    v = q.dequeue();
    
    // do operation for different applications
    
    for (p in neighbor(v)) {
      if (!visited[p]) {
        q.enqueue(p);
        visited[p] = T;
      }
    }
  }
}
```

Time: $O(n+m)$

Space: $O(n+m)$

In this case it's better to use adjacency list, since we need to traverse all the neighbors of each vertex

```
BFS_ALL(G) {
  visited[1...n];
  for (s = 1...n) {
    if (!visited[s]) {
      BFS(G,s, visited);
    }
  }
}
```

Time: $O(n+m)$

**NOTE:** Pay attention to this time complexity.

## DFS

```
DFS(G, S, visited) {
  visited[S] = T;
  
  // processing vertex S
  
  for (v in neighbor(S)) {
    if (!visited[v]) {
      DFS(G, v, visited);
    }
  }
}
```

Time: $O(n+m)$

## Application

#### Cycle Detectoin

input: directed G = (V, E)

output: True if there is a cycle in the graph, false otherwise



**Thought process:** Obviously, dfs should be used. But basic dfs is not enough to solve the problem.

There is a cycle when during one bfs process, the neighbor of current vertex is visited but not finished (finished means dfs all neighbor).

Use color[1…n] to replace visited[1…n]. 

color[u] = N,	not visited

color[u] = V, 	visited but not finished

color[u] = F,	visited and finished

```
DFS(G, S) {
  color[S] = V;
  for (p in neighbor(S)) {
    if (color[p] = N) {
      DFS(G, p);
    } else if (color[p] = V) {
      exists cycle;
    }
  }
  color[S] = F;
}
```

#### Topological Sort

The definition of ***topological sort*** is a linear ordering of its vertices such that for every directed edge uv from vertex u to vertex v, u comes before v in the ordering.

The purpose of this algorithm is to produce a list of vertices in topological ordering.

###### Approach 1: Non-DFS

The idea is, u must comes before v in the ordering if there is a edge uv. Therefore, vertices without incoming edge must come before other vertices. We use arary indegree[n] to record the incoming edges of every vertices.

```
// O(n+m)
for (u = 1...n) {
  for (v in neighbors(u)) {
    indegree[v]++;
  }
}
// O(n)
for (u = 1...n) {
  if (indegree[u] == 0) {
    q.enqueue(u);
  }
}
// O(n+m)
while(!q.isEmpty()) {
  u = q.dequeue();
  // add u to the list
  for (p in neighbor(u)) {
    indegree[p]--;
    if (indegree[p] == 0) {
      q.enqueue(p);
    }
  }
}
```

###### Approach 2: DFS

For edge uv from u to v, the finish time of dfs(u) must be later than the finish time of dfs(v). Therefore, we can add the vertex to the list after its dfs process. Finally, reverse the whole list.

```
DFS(G, S, L) {
  visited[S] = T;
  for (u in neighbor(S)) {
    DFS(G, u, L);
  }
  L.append(S);
}

DFS_ALL(G) {
  L = [];
  for (s = 1...n) {
    if (!visited[s]) {
      DFS(G, s, L);
    }
  }
  reverse(L);
  return L;
}
```

#### Strongly connected components

**Definition:** The strongly connected components of a directed graph form a partition into subgraphs that are themselves strongly connected.

The algorithm to find the sets of strongly connected components is:

1. Run dfs on the reverse graph $G^R$, i.e., the transpose of the adjacency matrix.
2. Run the dfs in the decreasing order of finish time to add vertex to list. Once call dfs in dfs_all, create a new list to add vertex.

**NOTE:** The proof of this algorithm is in *Algorithms_Papadimitriou, P103*.

#### World Ladders

Input: Dictionary D, D = {w1,…,wn}

​	    word1, word2

Output: Shortest sequence to go from word1 to word2



Thought process: First construct an map, then use bfs to find the shortest path in this map.

**这个题值得借鉴的地方是，把信息转化为一个更容易处理的数据结构**

```
map: String -> List<String>
map g;
for (u in D) {
  for (v in D) {
    if (u & v diif by 1 char) {
      g[u].append(v);
    }
  }
}
```

```
bfs(G, w1, w2) {
  Queue q;
  Set<String> visited;
  map<String, int> dist;
  map<String, String> parent;
  q.enqueue(w1);
  visited.insert(w1);
  while (!q.empty()) {
    u = q.dequeue();
    for (v in G[u]) {
      if (visited.has(v)) {
        continue;
      }
      q.enqueue(v);
      visited.insert(v);
      dist[v] = dist[u] + 1;
      parent[v] = u;
    }
  }
}
if (dist.has(w2)) {
  shortest_dist = dist[w2];
  sp = [];
  while (w2 != w1) {
    sp.append(w2);
    w2 = parent[w2];
  }
}
```

**Time Complexity:** O(n+m)

**Space Complexity:** O(n+m)

 Addtion question: What if there are many shortest path? how to get all of them?

#### Boggle

input: A board with letters in it, and a dictionary D

​	   Example for board:  

![Screen Shot 2017-03-17 at 3.10.51 PM](/Users/Chensii/Desktop/Screen Shot 2017-03-17 at 3.10.51 PM.png)

​	   D.word(String) -> T/F

​	   D.prefix(String) -> T/F

output: list of all words in this board. 

​              The word can start at any point in this board

​	      can go 4 directions

​	      disallow reusing same cell in same word

D.prefix("hel") -> T

D.prefix("hello") -> F

D.word("hel") -> F

D.word("hello") -> T



board: char\[4][4]

D: word(), prefix()

visited: bool\[4][4]

```
// example: dfs(visited, 1, 3, "", [])
dfs(visited, r, c, String cur, word0list) {
  visited[r][c] = T;
  cur-word = cur + board[r][c];
  if (D.word(cur_word)) {
    word-list.insert(cur_word);
  }
  if (D.prefix(cur-word) == F) {
    return;
  }
  if (r > 1 && !visited[r-1][c]) {
    dfs(visited, r-1, c, cur-word, word-list); 
  } 
  if (c > 1 && !visited[r][c-1]) {
    dfs(visited, r, c-1, cur-word, word-list);
  } 
  if (c < 4 && !visited[r][c+1]) {
    dfs(visited, r, c+1, cur-word, word-list);
  }
  if (r < 4 && !visited[r+1][c]) {
    dfs(visited, r+1, c, cur-word, word-list);
  }
  visited[r][c] = F;
}
```

Outer Function:

```
word-list[];
for (r = 1...4) {
  for (c = 1...4) {
    dfs(visited, r, c, "", word-list);
  }
}
```

n*n boggle

running time:

\#vertex: $n^2$

\#edge ~ $4n^2$

So the running time is $O(n^2)$ (no prefix use)



running time (word length l)

word(String)      O(l): calculate hashcode

prefix(String)     use Trie (n words, length l)  -> O(nl)      size of Dictionary

​			    x -> O(nl^2), prefixj size of prefix map, only cost O(nl) memory

#### Puzzel 8

Input: a square with 3*3 slots, 8 of 9 slots have different number 1-8

Output: sequence of moves that change these numbers into correct order



\# of vertices: n = 9!

\# of edges: m < 4n



queue\<String>

visited\<String>

dist\<String, int>

parent\<String, vertex>



serilize to string "1 2 3 4 5 6 7 8 space"

map\<int, \<int, int>>

1: 0, 0

2: 0, 1

...

8: 2, 1

recursion