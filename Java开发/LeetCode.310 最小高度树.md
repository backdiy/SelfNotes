## 方法1
```java
public List<Integer> findMinHeightTrees(int n, int[][] edges) {  
    if (n == 1) {  
        return Collections.singletonList(0);  
    }  
    Graph graph = new Graph(n, edges);  
    Node a = graph.getFarNode(0);  
    Node b = graph.getFarNode(a.farNode);  
    List<Integer> route = new ArrayList<>();  
    for (int i = b.farNode; i != a.farNode; i = b.parent[i]) {  
        route.add(i);  
    }  
    route.add(a.farNode);  
    if (route.size() % 2 != 0) {  
        return Collections.singletonList(route.get(route.size() / 2));  
    } else {  
        return Arrays.asList(route.get(route.size() / 2 - 1), route.get(route.size() / 2));  
    }  
}  
  
public static class Graph {  
    int size;  
  
    Map<Integer, List<Integer>> map;  
  
    public Graph(int size, int[][] edges) {  
        this.size = size;  
        this.map = new HashMap<>();  
        for (int[] edge : edges) {  
            map.computeIfAbsent(edge[0], k -> new ArrayList<>()).add(edge[1]);  
            map.computeIfAbsent(edge[1], k -> new ArrayList<>()).add(edge[0]);  
        }  
    }  
  
    public Node getFarNode(int start) {  
        boolean[] vis = new boolean[size];  
        vis[start] = true;  
        Queue<Integer> queue = new ArrayDeque<>();  
        int[] parent = new int[size];  
        int farNode = start;  
        int maxDis = 0;  
        queue.add(start);  
        while (!queue.isEmpty()) {  
            Integer poll = queue.poll();  
            for (Integer next : map.get(poll)) {  
                if (!vis[next]) {  
                    vis[next] = true;  
                    parent[next] = poll;  
                    queue.add(next);  
                    int dis = getNodeDis(start, next, parent);  
                    if (dis > maxDis) {  
                        farNode = next;  
                        maxDis = dis;  
                    }  
                }  
            }  
        }  
        return new Node(farNode, parent);  
    }  
  
    private int getNodeDis(Integer start, Integer end, int[] parent) {  
        int dis = 0;  
        for (int i = end; i != start; i = parent[i]) {  
            dis++;  
        }  
        return dis;  
    }  
}  
  
public static class Node {  
    int farNode;  
    int[] parent;  
  
    public Node(int farNode, int[] parent) {  
        this.farNode = farNode;  
        this.parent = parent;  
    }  
}
```
## 方法二
```java
public List<Integer> findMinHeightTrees(int n, int[][] edges) {  
    if (n == 1) {  
        return List.of(0);  
    }  
    List<Integer>[] g = new List[n];  
    Arrays.setAll(g, k -> new ArrayList<>());  
    int[] degree = new int[n];  
    for (int[] e : edges) {  
        int a = e[0], b = e[1];  
        g[a].add(b);  
        g[b].add(a);  
        ++degree[a];  
        ++degree[b];  
    }  
    Deque<Integer> q = new ArrayDeque<>();  
    for (int i = 0; i < n; ++i) {  
        if (degree[i] == 1) {  
            q.offer(i);  
        }  
    }  
    List<Integer> ans = new ArrayList<>();  
    while (!q.isEmpty()) {  
        ans.clear();  
        for (int i = q.size(); i > 0; --i) {  
            int a = q.poll();  
            ans.add(a);  
            for (int b : g[a]) {  
                if (--degree[b] == 1) {  
                    q.offer(b);  
                }  
            }  
        }  
    }  
    return ans;  
}
```