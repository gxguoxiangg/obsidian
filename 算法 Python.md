## 数据结构

### 并查集

并查集支持的核心操作：合并、查询


```python
class DSU{
    # m 为虚节点预留的空间
	def __init__(self, size, m):
    	self.id = size * 2
    	self.pa = list(range(size, size * 2)) + list(range(size, size * 2 + m))
    	self.size = [1] * (size * 2 + m)
    
    def find(self, x):
    	if self.pa[x] != x:
    		self.pa[x] = self.find(self.pa[x])
    	return self.pa[x]
    
    def unite(self, x, y):
    	x, y = self.find(x), self.find(y)
    	if x == y:
    		return
    	if self.size[x] < self.size[y]:
    		x, y = y, x
   		self.pa[y] = x
    	self.size[x] += self.size[y]
    
    def erase(self, x):
    	y = self.find(x)
    	self.size[y] -= 1
    	self.pa[x] = slef.id	# 删除后，这个点的根需要是一个新的虚节点
    	self.id += 1
}
```



## 图

### 建图

#### 直接存边

使用数组来存储边，每个元素都包含一条边的起点和终点，带边权的图还包含边权。

```python
class Edge:
    def __init__(self, u=0, v=0)
    	self.u = u
        self.v = v

# map(function, iterable) 返回一个将function应用于iterable的每个项目的迭代器
# 这里则是将输入的字符串转换为 int 类型
# n = 点数, m = 边数
n, m = map(int, input().split())

e = [Edge() for _ in range(m)]
vis = [False] * n

# 读取存储边
for i in range(m):
    e[i].u, e[i].v = map(int, input().split())

def find_edge(u, v):
    for i in range(m):
        if e[i].u == u and e[i].v == v:
            return True
    return False

def dfs(u):
    if vis[u]:
        return
   	vis[u] = True
    for i in range(m):
        if e[i].u == u
        	dfs(e[i].v)
            
```

复杂度：

- 查询是否存在某条边：On

- 遍历一个点的出边：Om

- 遍历整个图：Onm

  

#### 邻接矩阵

使用一个二维数组来存边

```python
vis = [False] * (n + 1)
adj = [[0] * (n + 1) for _ in range(n + 1)]

for i in range(1, m + 1):
    u, v = map(int, input().split())
    # 有向图
    adj[u][v] = 1

def find_edge(u, v):
    return adj[u][v]

def dfs(u):
    if vis[u]:
        return
   	vis[u] = True
   	for v in range(1, n + 1):
        if adj[u][v]:
            dfs(v)
```

复杂度：

- 查询是否存在一条边：O1
- 遍历一个点的所有出边：On
- 遍历整张图：On^2^



#### 领接表

使用一个支持动态增加元素的数据结构构成的数组来存边

```python
vis = [False] * (n + 1)
adj = [[] for _ in range(n + 1)]

for i in range(1, m + 1):
    u, v = map(int, input().split())
    adj[u].append(v)

def find_edge(u, v):
    for i in range(0, len(adj[u])):
        if adj[u][i] == v:
            return True
    return False

def dfs(u):
    if vis[u]:
        return
   	vis[u] = True
    for v in adj[u]:
        dfs(v)
        
```



复杂度：

- 查询是否存在 𝑢![u](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 到 𝑣![v](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的边：𝑂(𝑑+(𝑢))![O(d^+(u))](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)（如果事先进行了排序就可以使用 [二分查找](https://oi-wiki.org/basic/binary/) 做到 𝑂(log⁡(𝑑+(𝑢)))![O(\log(d^+(u)))](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7))．
- 遍历点u的所有出边：𝑂(𝑑+(𝑢))![O(d^+(u))](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)
- 遍历整张图：O(n + m)



### 遍历

#### DFS

需要使用 vis 数组标记，哪些遍历过哪些需要被遍历，否则会陷入死循环。



#### BFS

使用队列

```python
def bfs(u):
    q = deque(u)
    vis[u] = True
    dis[u] = 0
    path[u] = -1
    while len(q) != 0
    	u = q.popleft()
        
    
```



### 最短路



#### Dijkstra

**解决不带负权的单源最短路径问题**

原理：

- 找最近：从所有“暂定”的点里，挑一个距离值最小的，这个值从此就确定下来了（因为走其他路绕过来只会更远）。
- 借路更新：看看从这个刚确定的点出发，能否把它的邻居们的“暂定距离”更新得更小。
- 重复：直到所有点的距离都确定。

步骤：

将节点分为两个集合：

- 已经确定最短路长度的点集 S
- 未确定最短路径长度的点集 T

一开始所有的点都属于 T 集合。初始化 dis(s) = 0，其他点的 dis 均为 +∞![+\infty](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

然后重复以下步骤：

1. 从 T 集合中，选取一个最短路长度最小的节点，移到 S 集合中。
2. 对那些刚刚被加入 S 集合的节点的所有出边执行松弛操作。

**$ 朴素版本时间复杂度 O (n^2)$**

```python
INF = float("inf")
g = [[0] * n for _ in range(n)]
dis, vis = [INF] * n, [False] * n

def dijkstra(n, s):
    dis[s] = 0
    for i in range(n):
        u, minDis = INF
        # 暴力找当前未访问过且距离最小的节点
        for j in range(n):
            if vis[j] != True and dis[j] < minDis:
                u, minDis = j, dis[j]
		vis[u] = True
        # 松弛
        for v in range(n):
            if dis[v] > dis[u] + g[u][v]:
                dis[v] = dis[u] + g[u][v]
```



**$ 堆优化时间复杂度O(nlogn)$**

```python
g = [[0] * n for _ in range(n)]
dis = defaultdict(lambda: float("inf"))
vis = set()

def dijkstra(n, s):
    q = [(0, s)]
    # 堆的压入和弹出操作，时间复杂度是 logn
	while q:
    	_, u = heapq.heappop(q)
        if u in vis:
            continue
		vis.add(u)
        for v in range(n):
            if dis[v] > dis[u] + g[u][v]:
                dis[v] = dis[u] + g[u][v]
                heapq.heappush(q, (dis[v], v))        
```





#### Bellman-Ford

处理存在负权的单源最短路

**$ 朴素版本时间复杂度 O (nm)$**

```python
INF = float("inf")
g = [[0] * n for _ in range(n)]
dis = [INF] * n

bool bellman_ford(n, s):
    dis[s] = 0
    bool flag
    for i in range(目标边数):
        flag = False
        for u in range(n):
            if dis[u] == INF:
                continue
            for v in range(n):
                if dis[v] > div[u] + g[u][v]:
                    dis[v] = dis[u] + g[u][v]
                    flag = True
   		if blag == False:
        	break
	# 第 n 轮循环仍然可以松弛, 则说明 s 点可以抵达一个负环
  	return flag
        
```



**$ SPFA队列优化版时间复杂度一般O (m) 最差O (nm)$**

```python
INF = float("inf")
g = [[0] * n for _ in range(n)]
dis = [INF] * n
vis = [False] * n
cnt = [0] * n

bool bellman_ford(n, s):
    dis[s] = 0
    q = deque()
    q.push(s)
    while len(q) != 0:
		u = q.popleft()
        vis[u] = False
        for v in range(n):
            if dis[v] > dis[u] + g[u][v]:
                dis[v] = dis[u] + g[u][v]
                cnt[v] = cnt[u] + 1		# 记录最短路经过的边数
                if cnt[v] >= n:
                    return False
                # 在不经过负环的情况下，最短路至多经过 n - 1 条边
                # 因此如果经过了多于 n 条边，一定说明经过了负环
               	if not vis[v]:		# 避免重复入队
                    q.append(v)
                    vis[v] = True
```





### 最小生成树



#### Prim

以节点为主，每次要选择距离最小的一个节点，以及用新的边更新其他节点的距离。

其实和Dijkstra一样，每次找到距离最小的一个点，可以暴力也可以用堆维护。

**$ 朴素版本时间复杂度 O (n^2 + m)$**

```python
INF = float("inf")
g = [[0] * n for _ in range(n)]
vis = [False] * n
dis = [INF] * n

def prim(s):
    res = 0
    dis[s] = 0
    # prim 必须要遍历 n 次，否则 MST权值记录缺少一条边
    for i in range(n):
    	minVal, u = INF, 0
        for j in range(n):	if not vis[j] and dis[j] < minVal:
            u, minval = j, dis[j]
        if dis[u] == INF	return INF
    	res += dis[u]
        vis[u] = True
        for j in range(n):	dis[j] = min(dis[j], g[u][j])
	return res
```

**$ 堆优化版本时间复杂度 O (mlogm)$**

```python
INF = float("inf")
g = [[0] * n for _ in range(n)]
vis = [False] * n
dis = [INF] * n
# cnt 的作用是早停，一旦 cnt == n 说明已经有 n-1条边了，最小生成树已经形成，此时堆里可能还有很多条边，及时退出
cnt = 0

def prim(s):
	q = [(0, s)]
    while len(q) != 0:
        if cnt >= n:	break
        w, u = heapq.heappop(q)
        if vis[u]:		continue
        cnt += 1
        res += dis[u]
        for v in range(n):	if not vis[v] and dis[v] > g[u][v]:
            dis[v] = g[u][v]
            heapq.heappush([dis[v], v])
	return res
```



## 数论

### 最大公约数

如果我们已知两个数a和b，如何求出二者的最大公约数？

不妨设 a > b，我们发现如果 b 是 a 的约数，那么 b 就是二者的最大公约数。下面讨论不能整除的情况，

如果 a = b * q + r，其中 r < b。通过证明可得到 gcd(a, b) = gcd(b, a mod b)。

这里两个数的大小是不会增大的，那么我们也就得到了关于两个数的最大公约数的一个递归求法。

递归法：

```python
def gcd(a, b):
    if b == 0:
        areturn a
    return gcd(b, a % b)
```

迭代法：

```python
def gcd(a, b):
    while b != 0:
        a, b = b, a % b
    return a
```

上述都可被称作欧几里得算法
