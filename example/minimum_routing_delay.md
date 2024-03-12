最小路由时延和  题目描述

某网络中有 n 台路由器，编号从 1 … n，即：R1 … Rn，某两个路由器之间最多只有一条网线双向连通，其通信时延为 t（连通双向，R1到R2的时延，与R2到R1的时延是相等的）

现给定 m 个连通关系和时延，以及指定的两个路由器 Ri 和 Rj，请计算 Ri 和 Rj 之间的最小时延（各时延之和）。如果两个路由器之间未连通，则返回 -1。

解答要求
时间限制：1000ms, 内存限制：256MB

输入

第一行包含两个整数：分别表示路由器个数 n、连通关系个数 m , 其取值范围： 1 <= n, m <= 10000
第二行开始的 m 行代表连通关系，每行 3 个整数：路由器a 路由器b 通信时延t，1 <= t <= 10000，a 和 b 在 [1,n] 范围内。
最后一行包括两个整数，为指定的两个路由器 Ri 和 Rj，i 和 j 在 [1,n] 范围内

输出

一个整数，表示两个路由器之间的最小时延

样例

  输入样例 1 

    6 4
    1 2 2
    1 3 4
    2 5 3
    5 6 2
    2 6
  输出样例 1

    5
    
  提示样例 1
  
  一共6台路由器，4个连通关系，2号路由器和6号路由器之间的最小时延为5：2->5->6,其时延为2+3。

很典型的一道Dijkstra  

python题解：

    from collections import defaultdict
    import heapq
    
    class Solution:
        def get_min_delay(self, router_num, link_delays, ri, rj):
            # 在此添加你的代码
            graph = defaultdict(dict)
            for u, v, w in link_delays:
                graph[u][v] = w
                graph[v][u] = w
    
            path = defaultdict(lambda: float("inf"))
            path[ri] = 0
            q = [ri]
            used = [False] * router_num
            while q:
                curr = q.pop(0)
                if curr == rj:
                    return path[curr]
                if used[curr-1]:
                    continue
                used[curr-1] = True
                for v in graph[curr]:
                    length = graph[curr][v]
                    if path[curr] + length < path[v]:
                        path[v] = path[curr] + length
                        q.append(v)
    
            return -1
    
    
    if __name__ == "__main__":
        router_num, link_num = map(int, input().strip().split())
        link_delays = [list(map(int, input().strip().split())) for _ in range(link_num)]
        router1, router2 = map(int, input().strip().split())
        function = Solution()
        results = function.get_min_delay(router_num, link_delays, router1, router2)
        print(results)

C++题解：

    #include <climits>
    #include <queue>
    #include <unordered_map>
    using namespace std;
    struct State {
        int id;
        int distFromStart;
        State (int id, int distFromStart) : id(id), distFromStart(distFromStart) {}
    };
    vector<vector<vector<int>>> buildGraph(const int &routerNum, const vector<vector<int>> &linkDelays) {
        vector<vector<vector<int>>> graph(routerNum + 1, vector<vector<int>>());
        for (auto &link : linkDelays) {
            graph[link[0]].push_back({link[1], link[2]});
            graph[link[1]].push_back({link[0], link[2]});
        }
        return graph;
    }
    int GetMinDelay(int routerNum, const vector<vector<int>>& linkDelays, int routerRi, int routerRj)
    {
        int minDelay = INT_MAX;
        auto graph = buildGraph(routerNum, linkDelays);
        State *start = new State(routerRi, 0);
        State *end = new State(routerRj, INT_MAX);
        queue<State *> q;
        unordered_map<int, int> idToDistFromStart;
        q.push(start);
        idToDistFromStart[routerRi] = 0;
        while (!q.empty()) {
            auto sz = q.size();
            for (auto i = 0; i < sz; ++i) {
                State *cur = q.front();
                q.pop();
                if (cur->id == end->id) {
                    minDelay = min(minDelay, cur->distFromStart);
                    continue;
                }
                for (auto &neighbor : graph[cur->id]) {
                    int nextId = neighbor[0];
                    int nextDelay = neighbor[1];
                    int nextDistFromStart = cur->distFromStart + nextDelay;
                    if (idToDistFromStart.count(nextId) && nextDistFromStart >= idToDistFromStart[nextId]) {
                        continue;
                    }
                    q.push(new State(nextId, nextDistFromStart));
                    idToDistFromStart[nextId] = nextDistFromStart;
                }
            }
        }
        return minDelay == INT_MAX ? -1 : minDelay;
    }
