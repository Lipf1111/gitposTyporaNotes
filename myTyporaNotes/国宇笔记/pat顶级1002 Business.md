### pat顶级1002 Business

As the manager of your company, you have to carefully consider, for each project, the time taken to finish it, the deadline, and the profit you can gain, in order to decide if your group should take this project. For example, given 3 projects as the following:

- Project[1] takes 3 days, it must be finished in 3 days in order to gain 6 units of profit.
- Project[2] takes 2 days, it must be finished in 2 days in order to gain 3 units of profit.
- Project[3] takes 1 day only, it must be finished in 3 days in order to gain 4 units of profit.

You may take Project[1] to gain 6 units of profit. But if you take Project[2] first, then you will have 1 day left to complete Project[3] just in time, and hence gain 7 units of profit in total. Notice that once you decide to work on a project, you have to do it from beginning to the end without any interruption.

### Input Specification:

Each input file contains one test case. For each case, the first line gives a positive integer *N* (≤50), and then followed by *N* lines of projects, each contains three numbers *P*, *L*, and *D* where *P* is the profit, *L* the lasting days of the project, and *D* the deadline. It is guaranteed that *L* is never more than *D*, and all the numbers are non-negative integers.

### Output Specification:

For each test case, output in a line the maximum profit you can gain.

### Sample Input:

```in
4
7 1 3
10 2 3
6 1 2
5 1 1
```

### Sample Output:

```out
18
```

### 算法设计

这是一道0-1背包问题。设P~i~、L~i~、D~i~分别表示第ii*i*个任务的收益、持续时间、截止日期，d(i,j)表示在第1,2,…,*i*个任务中，任选一些能够在*j*天内完成的任务，所得到的最大收益。假设下标从1开始。我们首先按截止日期从小到达对这些任务进行排序。我们要注意，如果在第*j*天要完成第*i*个任务，那么这个任务的最晚开始时间应该是K=min(D~i~,j)−L~i~，显然当K<0时，这个任务不可能在第*j*天完成。那么我们可以得到状态转移方程：

1. K<0，d(i,j)=d(i−1,j)//表示第j天无法完成第i个任务，实际已经完成的任务收益为到第i-1个任务的

2. K≥0，d(i,j)=max{d(i−1,j),d(i−1,k)+P~i~)

   //若是加上第i个任务，比较未加时的收益，还有第k天完成未加时的收益加上当前i任务的收益。因为从k开始要进行任务i处理

#### C++代码

```c++
#include <bits/stdc++.h>
using namespace std;
struct Pro {
    int p, l, d;
};
int main() {
    int n;
    cin >> n;
    vector<Pro> pros(n + 1);
    for (int i = 1; i <= n; ++i) {
        cin >> pros[i].p >> pros[i].l >> pros[i].d;
    }
    sort(pros.begin(), pros.end(), [](const Pro& p1, const Pro& p2) {
        return p1.d < p2.d;
    });
    int t = pros.back().d;
    vector<vector<int>> dp(n + 1, vector<int>(t + 1));
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= t; ++j) {
            dp[i][j] = dp[i - 1][j];
            int k = min(j, pros[i].d) - pros[i].l;
            if (k >= 0)
                dp[i][j] = max(dp[i][j], dp[i - 1][k] + pros[i].p);
        }
    }
    cout << dp[n][t];
    return 0;
}
```

