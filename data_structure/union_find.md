# 并查集
 [并查集解释] (https://zhuanlan.zhihu.com/p/93647900/)

用于处理不相交集合 (disjoint sets) 合并及查找的问题，典型应用有连通分量检测，环路检测等。原理和复杂度分析等可以参考[维基百科](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)。

### [redundant-connection](https://leetcode-cn.com/problems/redundant-connection/)

```Python   # 路径压缩和按秩合并如果一起使用，时间复杂度接近O(n) 
class Solution:
    def findRedundantConnection(self, edges: List[List[int]]) -> List[int]:
        # 对于没有环的树来说，有n条边，则有n+1个节点
        parent = list(range(len(edges) + 1))  # 初始时每个结点的父节点都是自己，+1 表示第0个节点是空的，
        rank = [1] * (len(edges) + 1)  # 用一个数组rank[]记录每个根节点对应的树的深度（如果不是根节点，其rank相当于以它作为根节点的子树的深度）。
                                       # 一开始，把所有元素的rank（秩）设为1。合并时比较两个根节点，把rank较小者往较大者上合并。
        
        def find(x):
            if parent[parent[x]] != parent[x]:   #
                parent[x] = find(parent[x]) # path compression 
            return parent[x]   # 返回父节点
        
        def union(x, y):
            px, py = find(x), find(y)
            if px == py:
                return False
            # union by rank
            if rank[px] > rank[py]:
                parent[py] = px
            elif rank[px] < rank[py]:  #把秩小的加到 秩多的里面，不会增加深度，只会增加广度
                parent[px] = py
            else:
                parent[px] = py
                rank[py] += 1   # 如果秩相同，则深度加1
            return True
        
        for edge in edges:
            if not union(edge[0], edge[1]):
                return edge
```

```Python
# 如果对路径压缩要求不高的话可使用
def find(x):
            if parent[x] != X:  #  节点x的父节点为parent[x]
                parent[x] = find(parent[x])    #  父节点设为根节点
            return parent[x]
```

### [accounts-merge](https://leetcode-cn.com/problems/accounts-merge/)

```Python
class Solution:
    def accountsMerge(self, accounts: List[List[str]]) -> List[List[str]]:
        
        parent = []
        rank = []
        
        def find(x):
            if parent[parent[x]] != parent[x]:
                parent[x] = find(parent[x])
            return parent[x]
        
        def union(x, y):
            px, py = find(x), find(y)
            if px == py:
                return
            if rank[px] > rank[py]:
                parent[py] = px
            elif rank[px] < rank[py]:
                parent[px] = py
            else:
                parent[px] = py
                rank[py] += 1
            return
        
        email2name = {}
        email2idx = {}
        i = 0
        for acc in accounts:
            for email in acc[1:]:
                email2name[email] = acc[0]    # email2name字典中，对每个email都加上name
                if email not in email2idx:
                    parent.append(i)
                    rank.append(1)
                    email2idx[email] = i
                    i += 1
                union(email2idx[acc[1]], email2idx[email])   # union(edge[0], edge[1])  acc[1]:邮箱
        
        result = collections.defaultdict(list)
        for email in email2name:
            result[find(email2idx[email])].append(email)
        
        return [[email2name[s[0]]] + sorted(s) for s in result.values()]
```

### [longest-consecutive-sequence](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

```Python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        
        parent = {num: num for num in nums}
        length = {num: 1 for num in nums}
        
        def find(x):
            if parent[parent[x]] != parent[x]:
                parent[x] = find(parent[x])
            return parent[x]
        
        def union(x, y):
            px, py = find(x), find(y)
            if px == py:
                return
            # union by size
            if length[px] > length[py]:
                parent[py] = px
                length[px] += length[py]
            else:
                parent[px] = py
                length[py] += length[px]
            return
        
        max_length = 0
        for num in nums:
            if num + 1 in parent:
                union(num + 1, num)
            if num - 1 in parent:
                union(num - 1, num)
            
            max_length = max(max_length, length[parent[num]])
        
        return max_length
```

### Kruskal's algorithm

### [minimum-risk-path](https://www.lintcode.com/problem/minimum-risk-path/description)

> 地图上有 m 条无向边，每条边 (x, y, w) 表示位置 m 到位置 y 的权值为 w。从位置 0 到 位置 n 可能有多条路径。我们定义一条路径的危险值为这条路径中所有的边的最大权值。请问从位置 0 到 位置 n 所有路径中最小的危险值为多少？

- 最小危险值为最小生成树中 0 到 n 路径上的最大边权。

```Python
# Kruskal's algorithm
class Solution:
    def getMinRiskValue(self, N, M, X, Y, W):
        
        # Kruskal's algorithm with union-find
        parent = list(range(N + 1))
        rank = [1] * (N + 1)
        
        def find(x):
            if parent[parent[x]] != parent[x]:
                parent[x] = find(parent[x])
            return parent[x]
        
        def union(x, y):
            px, py = find(x), find(y)
            if px == py:
                return False
            
            if rank[px] > rank[py]:
                parent[py] = px
            elif rank[px] < rank[py]:
                parent[px] = py
            else:
                parent[px] = py
                rank[py] += 1
            
            return True
        
        edges = sorted(zip(W, X, Y))
        
        for w, x, y in edges:
            if union(x, y) and find(0) == find(N): # early return without constructing MST
                return w
```
