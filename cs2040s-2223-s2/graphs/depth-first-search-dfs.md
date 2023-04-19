# Depth First Search (DFS)

Depth first search (DFS) is the second of two classical algorithms that we use to identify whether two nodes on a graph are connected, and opts for a very different approach. Instead of searching broadly, as BFS does, DFS chooses to traverse paths until the very end of the path, before proceeding to the next possible path.

### Algorithm Details

The problem statement for both BFS and DFS are the same.

**Problem:** given a graph `G`, we want to check if two nodes `s` and `e` are connected.

**Algorithm Input:** a graph `G`, a starting node `s`, an ending node `e`.

**Algorithm Output:** a boolean, denoting whether it is possible to reach `s` from `e`.

### Algorithm Idea

We start by creating a boolean array `visited`, which we will use to store whether the nodes have been visited yet. This array is initialized with `visited[s] = true`. Following that, we add every neighbour of `s` to a queue, and start to run through the queue.

For every node `n` in the queue, we will check if it has been visited, i.e., if `visited[n] = true`. If it has been visited, we will do nothing and move on to the next node in the queue. For nodes that have yet to be visited, we will mark them as visited, check if it is our target node `e`, and add all of its neighbours to the queue if it is not `e`. If it is `e`, our algorithm can terminate.

<figure><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/Depth-first-tree.svg/1200px-Depth-first-tree.svg.png" alt=""><figcaption><p>The order in which a DFS algorithm searches a graph, given the starting node <code>1</code>. Source: <a href="https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/Depth-first-tree.svg/1200px-Depth-first-tree.svg.png">Wikipedia</a>.</p></figcaption></figure>

As you can see, a DFS algorithm will eventually visit every node `n` that is visitable from the starting node `s`. If our target node `e` is never visited, this means that it is impossible to visit `e` when starting from `s`, and our algorithm will terminate.

### Algorithm Implementation

Suppose our graph `G` is being represented using an adjacency list. We can then write the following pseudocode for a DFS algorithm. Observe that we use a `Stack` data structure, which is a First-In-Last-Out (FILO) data structure to store our list of places to visit. This ensures that the latest nodes which are added to the queue will have the highest priority, representing the fact that the DFS will go to the end of a path, before returning upwards and traversing the next possible path all the way till it ends.

{% code lineNumbers="true" %}
```java
boolean BFS(Node[] nodeList, int s, int e) {
    int size = nodeList.length;
    
    boolean[] visited = new boolean[size];
    for (int i = 0; i < size; i++) {
        visited[i] = false;
    }
    
    // Stack data structure
    Stack<Integer> q = new Stack<>();
    visited[s] = true;
    q.push(s);
    
    while (!q.isEmpty()) {
        int curr = q.pop();
        if (!visited[curr]) {
            visited[curr] = true;
            for (int neighbor : nodeList[curr].neighbors) {
                q.push(neighbor);
            }
        }
    }
    return visited[e];
}
```
{% endcode %}

The runtime of this algorithm will be $$O(V+E)$$, as in the worst case, every node and vertice will be checked once.

### Remarks

Observe that this algorithm is suited to finding a possible path. This is useful, and better than a BFS when the search space is very large. For example, it might be better to use a DFS rather than a BFS when writing a maze finding algorithm, or we might be trapped in the maze for a very long while.

**Caution: although DFS explores every node and every edge, it does not explore every possible path between nodes. This is because once a node has been visited, it is never visited again.**
