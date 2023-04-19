# Breadth First Search (BFS)

A common problem in graph theory is to check whether two nodes of a graph are connected, and to find a path between the two nodes, if there exists one. Breadth first search (BFS) is the first of two classical algorithms that we use to identify whether two nodes on a graph are connected.

### Algorithm Details

The problem statement for both BFS and DFS are the same.

**Problem:** given a graph `G`, we want to check if two nodes `s` and `e` are connected.

**Algorithm Input:** a graph `G`, a starting node `s`, an ending node `e`.

**Algorithm Output:** a boolean, denoting whether it is possible to reach `s` from `e`.

### Algorithm Idea

We can try and implement a slightly more general solution first - a way to produce a list of nodes that can be visited given the starting node `s`. We start by creating a boolean array `visited`, which we will use to store whether the nodes have been visited yet. This array is initialized with `visited[s] = true`.

We then add every neighbour of `s` to a queue, and start to run through the queue. For every node `n` in the queue, we will check if it has been visited, i.e., if `visited[n] = true`. If it has been visited, we will do nothing and move on to the next node in the queue. For nodes that have yet to be visited, we will mark them as visited, check if it is our target node `e`, and add all of its neighbours to the queue if it is not `e`. If it is `e`, our algorithm can terminate.

<figure><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/33/Breadth-first-tree.svg/1200px-Breadth-first-tree.svg.png" alt=""><figcaption><p>The order in which a BFS algorithm searches a graph, given the starting node <code>1</code>. Source: <a href="https://upload.wikimedia.org/wikipedia/commons/thumb/3/33/Breadth-first-tree.svg/1200px-Breadth-first-tree.svg.png">Wikipedia</a>.</p></figcaption></figure>

As you can see, a BFS algorithm will eventually visit every node `n` that is visitable from the starting node `s`. If our target node `e` is never visited, this means that it is impossible to visit `e` when starting from `s`, and our algorithm will terminate.

### Algorithm Implementation

Suppose our graph `G` is being represented using an adjacency list. We can then write the following pseudocode for a BFS algorithm. Observe that we use a `Queue` data structure, which is a First-In-First-Out (FIFO) data structure to store our list of places to visit. This ensures that we will visit the nodes in the order that they were added to the queue.

{% code lineNumbers="true" %}
```java
boolean BFS(Node[] nodeList, int s, int e) {
    int size = nodeList.length;
    
    boolean[] visited = new boolean[size];
    for (int i = 0; i < size; i++) {
        visited[i] = false;
    }
    
    // Queue data structure
    Queue<Integer> q = new LinkedList<>();
    visited[s] = true;
    q.add(s);
    
    while (!q.isEmpty()) {
        int curr = q.poll();
        if (!visited[curr]) {
            visited[curr] = true;
            for (int neighbor : nodeList[curr].neighbors) {
                q.add(neighbor);
            }
        }
    }
    return visited[e];
}
```
{% endcode %}

The runtime of this algorithm will be $$O(V+E)$$, as in the worst case, every node and vertice will be checked once.

### Remarks

Observe that this algorithm is suited to finding the shortest path - when adding nodes to be visited, it chooses to add every neighbor in the next "layer", before proceeding to add nodes that will take a longer path. This means that if a path exists between the start `s` and the end `e`, the first path between them that the BFS algorithm finds will be the shortest path.

**Caution: although DFS explores every node and every edge, it does not explore every possible path between nodes. This is because once a node has been visited, it is never visited again.**
