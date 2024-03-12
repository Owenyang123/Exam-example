程序互斥  题目描述

有些计算机程序存在互斥，两个不能一起运行；我们把一组可随意组合运行（即任意两个程序都不互斥）的程序集合，称为「和谐程序组」。

现给定一组计算机程序信息（程序编号及运行时间），以及互斥清单，请找出符合以下规则的一个和谐程序组，并输出该和谐程序组的所有程序的运行时间之和：

规则1：首先和谐程序组的程序数量最多；
规则2：如果规则1多解，则找出和谐程序组中所有程序运行时间之和最小的。

输入

  首行是一个整数 appNum ，表示计算机程序的数量，程序编号从 1 到 appNum，取值范围 [1,20]
  第二行 appNum 个整数，依次表示每个计算机程序的运行时间，运行时间取值范围 [1,100]
  第三行是一个整数 mutexNum ，表示互斥的程序对的数量，取值范围 [0, appNum*(appNum-1)/2]
  接下来 mutexNum 行，每行一对互斥的程序编号，格式如NO1 NO2，程序编号的范围 [1, appNum]
  用例保证 mutexNum 行输入中不存在重复的互斥程序对
  
输出

  一个整数，表示满足条件的和谐程序组的程序运行时间之和

样例
  输入样例 1 

    3
    1 2 10
    1
    1 2
    
  输出样例 1

    11
    
  提示样例 1
  
  程序 1 与 2 互斥，不能同时存在。可行的组合：
  1）程序数量最多为2：分别为 “程序1 + 程序3” ，或 “程序2 + 程序3”
  2）两个组合的运行时间之和分别为 11 和 12
  因此选择运行之和小的组合为 “程序1 + 程序3”，并且返回其运行时间之和 11


  输入样例 2 

    1
    1
    0
    
  输出样例 2

    1
    
  提示样例 2
  
  只有一个程序，无互斥对，返回这个程序的运行时间

经典DFS+回溯,算困难题了，按bolg上的排列组合求解思路的总结一套自己的范式，很多要考虑的点都是固定的，比如在什么时候停止，什么时候开始回溯，怎样避免重复，怎样剪枝等。

1.根据 mutexs 对，构建每个ID的所有互斥列表，map结构存放，用于快速索引。
2.使用dfs 算法，探索每种组合，具体：
  2.1 传入 和谐程序列表 和 加入程序ID，判断后者是否可以加入列表；如果可以，加入列表，继续检查下一个ID。穷举该路径。
  2.2 继续检查下一个ID，是否可以加入列表，并穷举该路径。
  2.3 如果已经穷举所有ID，即已经完成一种组合；计算 和谐程序列表 的长度：
  2.3.1 如果比上一个结果大，则保存该 长度结果和所需时间；
  2.3.2 如果和上一个结果一样，则计算所需时间并和上一个比较，二者取小的保存
  2.3.3 如果长度小于上一个结果，则忽略该路径。
  
python代码（很早之前写的，我自己看得也略难懂，悟吧，觉得太难可以放弃这道）

    from itertools import combinations
    from typing import List
    
    
    class Solution:
        def calc_run_time(self, mutexs: List[List[int]], app_run_times: List[int]) -> int:
            app_l = [(i + 1) for i in range(len(app_run_times))]
            res = []
            for i in range(len(app_l)):
                for j in combinations(app_l, len(app_l) - i):
                    flag = 1
                    for k in mutexs:
                        if set(k)<= set(j):
                            flag = 0
                            break
                    if flag == 1:
                        res.append(j)
                if res != []:
                    break
            min_time = sum(app_run_times)
            for i in res:
                tmp = 0
                for j in i:
                    tmp += app_run_times[j - 1]
                min_time = min(min_time, tmp)
            return min_time
    
    
    if __name__ == "__main__":
        app_num = int(input().strip())
        app_run_times = list(map(int, input().strip().split()))
        mutex_num = int(input().strip())
        mutexs = [list(map(int, input().strip().split())) for _ in range(mutex_num)]
        function = Solution()
        results = function.calc_run_time(mutexs, app_run_times)
        print(results)

C++无脑回溯

    using namespace std;
    class Solution {
    public:
        // 待实现函数，在此函数中填入答题代码
        int maxNum = 0;
        int minRunTime = INT32_MAX;
        int CalcRunTime(const vector<int> &appRunTimes, const vector<pair<int, int>> &mutexs)
        {
            map<int, set<int>> mutexMap; // key is 程序编号， value是与该程序编号互斥的程序编号的集合
            for (const auto &item: mutexs) {
                mutexMap[item.first].insert(item.second);
                mutexMap[item.second].insert(item.first);
            }
            vector<int> curChosen;
            dfs(0, appRunTimes, mutexMap, curChosen, 0, 0);
            return minRunTime;
        }
        bool isMutexs(const set<int> &curMutexs, const vector<int> &curChosen)
        {
            for (const auto &mutex: curChosen) {
                if (curMutexs.count(mutex) != 0) {
                    return true;
                }
            }
            return false;
        }
        void dfs(int curIndex, const vector<int> &appRunTimes, const map<int, set<int>> &mutexMap, vector<int> &curChosen,
                 int curNum, int curTime)
        {
            if (curNum > maxNum) {
                maxNum = curNum;
                minRunTime = curTime;
            }
            if (curNum == maxNum && minRunTime > curTime) {
                minRunTime = curTime;
            }
            if (curIndex == appRunTimes.size()) {
                return;
            }
            for (int i = curIndex; i < appRunTimes.size(); ++i) {
                auto iter = mutexMap.find(i + 1);
                if (iter == mutexMap.end() || !isMutexs(iter->second, curChosen)) {
                    curNum++;
                    curTime += appRunTimes[i];
                    curChosen.push_back(i + 1);
                    dfs(i + 1, appRunTimes, mutexMap, curChosen, curNum, curTime);
                    curNum--;
                    curTime -= appRunTimes[i];
                    curChosen.pop_back();
                }
            }
        }
    };

C++二进制暴力枚举

    class Solution {
    private:
        unordered_map<int, vector<int>> mp;
    public:
        // 判断是否存在互斥程序
        int IsMutex(int num)
        {
            for (auto [k, v] : mp)
                for (auto a : v)
                    if (((num >> k) & 1) && ((num >> a) & 1))
                        return 0;
            return __builtin_popcount(num);
        }
        // 待实现函数，在此函数中填入答题代码
        int CalcRunTime(const vector<int>& appRunTimes, const vector<pair<int, int>>& mutexs)
        {
            int tmp = 0, ans = INT_MAX;
            for (auto [a, b] : mutexs)
                mp[a - 1].push_back(b - 1);
            auto calc = [&](int x) -> int {
                int num = 0;
                for (int i = 0; i < appRunTimes.size(); i++)
                    if ((x >> i) & 1)
                        num += appRunTimes[i];
                return num;
            };
            for (int i = 1; i < (1 << appRunTimes.size()); i++) {
                int cc = IsMutex(i);
                if (cc > tmp) {
                    tmp = cc;
                    ans = calc(i);
                }
                else if (cc == tmp) {
                    ans = min(ans, calc(i));
                }
            }
            return ans;
        }
    };
