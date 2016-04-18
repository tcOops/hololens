---
layout: post
title: 近期做题汇总
---

---
牢骚三句。

* 第一句： 虽然我现在弱， 但是以后不一定弱啊！
* 第二句： 希望2016年能去个靠谱的厂实习下。（暑期之后）
* 第三句： 到明年百度之星之前， 不做完leetcode || 不做完USACO || 还是现在这么弱 || 一件靠谱的衣服都没有 || 一场onsite都没有， 我直播吃键盘。。。（不一定是巧克力键盘）

---

```
近期一波题， 还是以复习之前学过的算法为主。
很多题目， 静下心来想还是不会那么没有头绪的。
```
<br>
2016/4/6
<br><br>
[http://acm.hust.edu.cn/vjudge/problem/viewProblem.action?id=18851](http://acm.hust.edu.cn/vjudge/problem/viewProblem.action?id=18851)<br>
`数位DP`的入门题， 这题直接DP比较方便，而不便使用记忆化（因为dp过程中其实发现状态跟具体数字没啥关系， 都是可以预处理出来的）
<br>

[http://hihocoder.com/problemset/problem/1033](http://hihocoder.com/problemset/problem/1033)<br>
交错和， `数位DP`， 采用记忆化搜索的方法比较方便
<br>

[http://acm.hdu.edu.cn/showproblem.php?pid=1505](http://acm.hdu.edu.cn/showproblem.php?pid=1505)<br>
`经典DP题`， 类似求`最大子矩阵`的题。 这里是给出0,1矩阵， 求出最大的全1子矩阵。 这里可以先有个弱化版的HDOJ-1006题， 然后在此基础上， 可以拓展到二维空间。 主要的DP思想，就是对于a[i][j]这个元素， 保存l[j],r[j]这两个状态， 而求l[j], r[j]的过程又可以进行DP状态转移（l[l[j]-1]）
对于求最大子矩阵和的问题， 弱化版的问题就是求最大连续子段和， 然后在此基础上进行预处理， 然后枚举上下界， 对从左到右的列采用一维情况下的最大连续字段和的做法， 可以降到O(n^3)
<br>

[http://acm.hdu.edu.cn/showproblem.php?pid=4547](http://acm.hdu.edu.cn/showproblem.php?pid=1505)<br>
经典`LCA问题的tarjan解法`， 并进行`离线处理`， 可以作为模板题来搞。
<br>

[http://acm.hust.edu.cn/vjudge/problem/viewProblem.action?id=16451](http://acm.hust.edu.cn/vjudge/problem/viewProblem.action?id=16451)<br>
训练指南上的经典题， 先dfs一次将无根树转有根树， 后贪心找到最远需要覆盖服务器的点，然后在此基础上再dfs一次将相关的点覆盖。 
<br>

[http://hihocoder.com/problemset/problem/1237](http://hihocoder.com/problemset/problem/1237)<br>
一道偏简单的`微软在线笔试题`。 算出x的范围之后， 枚举x, 然后得到两个可取最值的候选y，进行比较。 注意ceil与floor
<br>

[http://hihocoder.com/problemset/problem/1143](http://hihocoder.com/problemset/problem/1143?sid=698090)<br>
「棋盘覆盖」问题第一弹， 发现问题的本质是求fibonacci队列， 然后由于N很大，所以需要用`矩阵快速幂`进行加速， 注意取模。 模板题。
<br>

[http://hihocoder.com/problemset/problem/1151](http://hihocoder.com/problemset/problem/1151)<br>
「棋盘覆盖」问题第二弹， 官方给的提示非常详尽，可以去上面的地址去check。 大概思路：一行一行放置， 当前行的状态其实只跟上一行有关。 那么：

* 第i行不放置，则前一行必须有放置的骨牌。x对应二进制位为0，y对应二进制位为1。
* 第i行竖放骨牌，则前一行必须为空。x对应二进制位为1，y对应二进制位为0。
* 第i行横向骨牌，则前一行必须两个位置均有骨牌，否则会产生空位。x对应二进制位为1，y对应二进制位为1。

这三点是整个状态推导的关键（而且这个转移是完备的）。
这样可以得到一个状态转移的矩阵(规模是：(2^k-1)*(2^k-1))， 这样问题又转化为求矩阵快速幂
<br>

[http://hihocoder.com/problemset/problem/1162](http://hihocoder.com/problemset/problem/1162)<br>
「棋盘覆盖」问题第三弹，将第二弹的题目更加一般化处理。 所以第一步要先得到状态转移矩阵，由于K比较小， 可以先dfs得到所有的可转移的状态。然后下面问题跟第二弹一致。

---
2016/4/18
<br><br>
[http://hihocoder.com/problemset/problem/1170](http://hihocoder.com/problemset/problem/1170)<br>
`编程之美2015复赛`题。 经典状态压缩DP题。
第一步先预处理， 本题的机器人交换本质上就是求逆序数。 这里可以通过暴力枚举 || 树状数组 求解出交换两中颜色机器人需要的代价。 然后就是状态压缩，` dp[i] = min(dp[i^(1<<(j - 1))] + sum, dp[i])`;  这里的sum是将第j种颜色的机器人放到i这个状态最前面（或者最后面）的代价。
<br>


[http://hihocoder.com/problemset/problem/1189](http://hihocoder.com/problemset/problem/1189)<br>
一道`微软笔试题`， 非常好的题目， 经典的`DP优化题`。这道题目可以分为 确定顺序->多重背包->单调队列(优化降维)。<br>
最详细的题解：[http://hihocoder.com/discuss/question/2986](http://hihocoder.com/discuss/question/2986)
首先， n个events如果不确定Play的顺序， 那复杂度就是阶乘级别。 通过对两个events交换顺序之后与之前的比较， 发现可以将events先按某种顺序排序， 之后就转化为背包问题。
但是直接背包复杂度为O(n*m*k)， 其中最后一维k就是我们要优化的目标。 利用状态转移的等差性以及单调性，可以通过维护`单调队列`在O(1)的时间进行转移，具体细节参考上述题解。
<br>

[http://hihocoder.com/problemset/problem/1187](http://hihocoder.com/problemset/problem/1187)<br>
`数论题`。 首先：`假设正整数n质因子分解为：n = p1^n1 * p2^n2 * p3^n3 * ... * pk^nk
其中pi表示质因子，ni表示该质因子的数量，则有n的因子个数为：D = (n1+1)*(n2+1)*(n3+1)* ... * (nk+1)`。 如果暴力dfs， 会严重超时。 正确做法： 先分析n1,n2,n3之间的关系， 发现n1>n2>n3。。。。
这样的话， 枚举的范围会大大缩小。
具体题解：[http://hihocoder.com/discuss/question/2857](http://hihocoder.com/discuss/question/2857)<br>

[http://hihocoder.com/problemset/problem/1295](http://hihocoder.com/problemset/problem/1295)<br>
`线性筛法`的模板题。 为什么线性筛法可以这么写， 请参考：[http://blog.csdn.net/dinosoft/article/details/5829550](http://blog.csdn.net/dinosoft/article/details/5829550). <br>

[http://hihocoder.com/problemset/problem/1288](http://hihocoder.com/problemset/problem/1288)<br>
`微软在线笔试题`。 简单题， 因为满足`单调性(w越大，需要的page也就越多)`，所以可以二分+贪心。<br>

[http://hihocoder.com/problemset/problem/1289](http://hihocoder.com/problemset/problem/1289)<br>
`微软在线笔试题`。 一般暴力做法可能会超时。 这题显然可以使用`Trie`进行加速。 维护两棵trie树:allow deny. 对于每个查询， 分别找出两种情况下的最先遇到的匹配字符串， 然后进行大小比较。 这题有个需要注意点（也是坑点）： 同一个ip串可能会出现多次， 如果都更新那就WA了， 所以要判断是否已经出现了， 如果出现了某个串直接跳出。
<br>

[http://hihocoder.com/problemset/problem/1290](http://hihocoder.com/problemset/problem/1290)<br>
`微软在线笔试题`。 DP题。 dp[i][j][2]中的dp[i][j][0]表示计算到(i,j)时候， robot向右的方式总数，dp[i][j][1]则为向下的方向总数。 DP转移方程也比较好想：

```
  dp[i][j][0] = minab(dp[i][j-1][0],  dp[i-1][j][1]+(i+1<n && maze[i+1][j] != 'b')) + (maze[i][j] == 'b');
  dp[i][j][1] = minab(dp[i-1][j][1], dp[i][j-1][0]+(j+1<m && maze[i][j+1] != 'b')) + (maze[i][j] == 'b');
```

<br>
