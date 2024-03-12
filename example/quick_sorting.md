快速排序   题目描述

著名的快速排序算法里有一个经典的划分过程：通常采用某种方法取一个元素作为主元，通过交换，把比主元小的元素放到它的左边，比主元大的元素放到它的右边。

给定一个划分后的正整数序列，请问有多少个元素可能是划分过程中选取的主元？ 并按升序输出这些主元。

解答要求
时间限制：1000ms, 内存限制：256MB

输入
  第 1 行一个正整数 N，表示正整数序列的长度，取值范围 [1, 10^5]
  第 2 行 N 个互不不同的正整数，每个数的取值范围[1, 10^9]

输出
  按升序排列的可能主元的列表，以单个空格分隔；或者空列表。

样例
  输入样例 1 

    5
    1 3 2 4 5
    
  输出样例 1

    [1 4 5]
    
  提示样例 1
  
  1 的左边没有元素，右边的元素都比它大，所以它可能是主元。
  尽管 3 的左边元素都比它小，但其右边的 2 比它小，所以它不能是主元； 尽管 2 的右边元素都比它大，但其左边的 3 比它大，所以它不能是主元。
  同理，4 和 5 都可能是主元。
  因此，1、4、5 是可能的主元，按升序输出为 [1 4 5] 。

  python题解：

    from typing import List
    
    
    class Solution:
        def quick_sort(self, numbers: List[int]) -> List[int]:
            # 在此添加你的代码
            n = len(numbers)
            end = n - 1
            min_mun = float("inf")
            max_num = 0
            used = [1] * n
            for index in range(n):
                if numbers[index] < max_num:
                    used[index] = 0
                else:
                    max_num = numbers[index]
                if numbers[end] > min_mun:
                    used[end] = 0
                else:
                    min_mun = numbers[end]
                end -= 1
            result = []
            for index in range(n):
                if used[index] == 1:
                    result.append(numbers[index])
    
            return result
    
    
    if __name__ == "__main__":
        num0 = int(input().strip())
        numbers = list(map(int, input().strip().split()))
        function = Solution()
        results = function.quick_sort(numbers)
        print("[" + str(str.join(" ", map(str, results))) + "]")

C++题解

    vector<int> QuickSort(const vector<int>& numbers)
    {
        /**
        2 7 6 5 9 12 11  value 
        1 2 3 4 5 6  7   key
        ------------------------
        2 5 6 7 9 11 12  
        1 2 3 4 5 6  7   key2
        2       9      
        q= map1[0]
        for i : length()
        if map2[i] != map1[i]
            bad
            q = max(|map2[i],map1[i]|, q)
            i+= 1
            continue
        if map2[i] >= q
            good 
            push into res
        */ 
        vector<int> result;
        int len = numbers.size();
        vector<int>num(numbers);
        map<int, int> map1, map2;
        sort(num.begin(), num.end());
        for (int i = 0; i < len; i++) {
            map1[i] = numbers[i];
            map2[i] = num[i];
        }
        int q = map1[0];
        for (int ii = 0; ii < len; ii++) {
            if (map2[ii] != map1[ii]) {
                // q= maxnumFun();
                q = map2[ii] > map1[ii] ?(map2[ii] > q ? map2[ii] : q) : (map1[ii] > q ? map1[ii] : q);
                continue;
            }
            if (map2[ii] >= q) {
                result.push_back(map2[ii]);
            }
        }
        return result;
    }


java，不够优雅但是比较容易理解的代码

    import java.nio.charset.StandardCharsets;
    import java.util.ArrayList;
    import java.util.Arrays;
    import java.util.Comparator;
    import java.util.Scanner;
    public class Main {
        private static int[] quickSort(int[] numbers) {
            if (numbers.length == 0) return new int[0];
            int len = numbers.length;
            ArrayList<Integer> res = new ArrayList<>();
            for (int i = 0; i < len; i++) {
                if (judgeLeft(i, numbers) && judgeRight(i, numbers)) {
                    res.add(numbers[i]);
                }
            }
            res.sort(Comparator.naturalOrder());
            int[] ans = new int[res.size()];
            for (int i = 0; i < res.size(); i++) {
                ans[i] = res.get(i);
            }
            return ans;
        }
        private static boolean judgeRight(int i, int[] numbers) {
            for (int k = i + 1; k < numbers.length; k++) {
                if (numbers[k] < numbers[i]) {
                    return false;
                }
            }
            return true;
        }
        private static boolean judgeLeft(int i, int[] numbers) {
            for (int j = 0; j < i; j++) {
                if (numbers[j] > numbers[i]) {
                    return false;
                }
            }
            return true;
        }
        // main入口由OJ平台调用
        public static void main(String[] args) {
            Scanner cin = new Scanner(System.in, StandardCharsets.UTF_8.name());
            int row = cin.nextInt();
            int[] numbers = new int[row];
            for (int i = 0; i < row; i++) {
                numbers[i] = cin.nextInt();
            }
            cin.close();
            int[] results = quickSort(numbers);
            String[] strResult = Arrays.stream(results).mapToObj(String::valueOf).toArray(String[]::new);
            System.out.print("[" + String.join(" ", strResult) + "]");
        }
    }
