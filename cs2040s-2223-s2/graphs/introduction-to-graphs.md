# Introduction to Graphs

### Definitions

It is assumed that you have seen a graph before, and this section will provide a quick recap and refresher on some key terminologies.

**Definition.** A graph $$G$$ is an ordered pair $$\langle V, E\rangle$$ representing the set of nodes in the graph ($$V$$), and the set of edges ($$E$$) of the graph. A graph must have at least one node, i.e., $$|V|>0$$. The set $$E$$ is defined to be $$E:=\{(v, w):v,w\in V\}$$, and for all $$e_1,e2\in E$$, we have $$e_1\neq e_2$$.

**Definition.** The degree of a node is the number of adjacent edges, and the degree of a graph is the maximum of the degrees of the nodes in the graph.

**Definition.** The diameter of a graph is the maximum distance between two nodes, following the shortest path.

**Definition.** A bipartite graph is a graph that can partition its nodes into two sets, such that there are no edges between nodes of the same set.

### Graph Representations

There are two main ways we can store graphs: adjacency lists and adjacency matrices. Both methods are storing graphs are optimized for different use cases, as we shall see in a moment.

#### Adjacency Lists

In an adjacency list, we store our nodes are stored in an array, with each array position containing a linked list (storing the edges connected to that node).

<figure><img src="../.gitbook/assets/Screenshot 2023-04-19 at 5.14.38 PM.png" alt=""><figcaption><p>Taken from CS2040S Lecture 17.</p></figcaption></figure>

The following pseudocode shows what such a representation might entail.

{% code lineNumbers="true" %}
```java
class NeighborList extends LinkedList<Integer> {
}

class Node {
    int key;
    NeighborList neighbors;
}

class Graph {
    Node[] nodeList;
}
```
{% endcode %}

#### Adjacency Matrices

Another way of representing our nodes and edges is through the use of a matrix, with the row and column numbers denoting our nodes and the matrix entries denoting whether there exists an edge between the two vertices.

<figure><img src="../.gitbook/assets/Screenshot 2023-04-19 at 5.18.34 PM.png" alt=""><figcaption><p>Taken from CS2040S Lecture 17.</p></figcaption></figure>

One cool property of matrices is that multiplying them tells us information about the paths that we can take. For example, squaring the matrix gives us every node reachable with a path of length $$2$$, cubing the matrix gives us every node reachable with a path of length $$3$$, and so on. The following pseudocode shows what such a representation might entail.

{% code lineNumbers="true" %}
```java
class Graph {
    boolean[][] adjMatrix;
}
```
{% endcode %}

#### Remarks

When storing graphs using an adjacency list, we see that it has a space complexity of $$O(V+E)$$, with a space complexity of $$O(V)$$ for cycles. In the case of an adjacency matrix, it has a space complexity of $$O(V^2)$$ regardless. We therefore use adjacency lists for sparse matrices, and adjacency matrices for dense matrices.

| Adjacency List                       | Adjacency Matrix                     |
| ------------------------------------ | ------------------------------------ |
| Fast: find any neighbor of `a`       | Slow: find any neighbor of `a`       |
| Fast: enumerate all neighbors of `a` | Slow: enumerate all neighbors of `a` |
| Slower: are `a` and `b` neighbors?   | Fast: are `a` and `b` neighbors?     |

To summarize, we should think about the types of queries and operations to be performed on our graphs, as well as the density of the graph, before deciding between using an adjacency list and an adjacency matrix.
