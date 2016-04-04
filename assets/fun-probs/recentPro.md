---
layout: post
title: 近期做题汇总
---

---
牢骚三句。
第一句： 虽然我现在弱， 但是以后不一定弱啊！
第二句： 希望2016年能去个靠谱的厂实习下。（暑期之后）
第三句： 到明年百度之星之前， 不做完leetcode || 不做完USACO || 还是现在这么弱 || 一件靠谱的衣服都没有 & 一场onsite都没有， 我直播吃键盘。。。（不一定是巧克力键盘）

---

```
近期一波题， 还是以复习之前学过的算法为主。
很多题目， 静下心来想还是不会那么没有头绪的。
```

[http://acm.hust.edu.cn/vjudge/problem/viewProblem.action?id=18851](http://acm.hust.edu.cn/vjudge/problem/viewProblem.action?id=18851)<br>
数位DP的入门题， 这题直接DP比较方便，而不便使用记忆化（因为dp过程中其实发现状态跟具体数字没啥关系， 都是可以预处理出来的）
<br>

[http://hihocoder.com/problemset/problem/1033](http://hihocoder.com/problemset/problem/1033)<br>
交错和， 数位DP， 采用记忆化搜索的方法比较方便
<br>

[http://acm.hdu.edu.cn/showproblem.php?pid=1505](http://acm.hdu.edu.cn/showproblem.php?pid=1505)<br>
经典DP题， 类似求最大子矩阵的题。 这里是给出0,1矩阵， 求出最大的全1子矩阵。 这里可以先有个弱化版的HDOJ-1006题， 然后在此基础上， 可以拓展到二维空间。 主要的DP思想，就是对于a[i][j]这个元素， 保存l[j],r[j]这两个状态， 而求l[j], r[j]的过程又可以进行DP状态转移（l[l[j]-1]）
对于求最大子矩阵和的问题， 弱化版的问题就是求最大连续子段和， 然后在此基础上进行预处理， 然后枚举上下界， 对从左到右的列采用一维情况下的最大连续字段和的做法， 可以降到O(n^3)
<br>

[http://acm.hdu.edu.cn/showproblem.php?pid=4547](http://acm.hdu.edu.cn/showproblem.php?pid=1505)<br>
经典LCA问题的tarjan解法， 并进行离线处理， 可以作为模板题来搞。
<br>

[http://acm.hust.edu.cn/vjudge/problem/viewProblem.action?id=16451](http://acm.hust.edu.cn/vjudge/problem/viewProblem.action?id=16451)<br>
训练指南上的经典题， 先dfs一次将无根树转有根树， 后贪心找到最远需要覆盖服务器的点，然后在此基础上再dfs一次将相关的点覆盖。 
<br>

[http://hihocoder.com/problemset/problem/1237](http://hihocoder.com/problemset/problem/1237)<br>
一道偏简单的微软在线笔试题。 算出x的范围之后， 枚举x, 然后得到两个可取最值的候选y，进行比较。 注意ceil与floor
<br>

[http://hihocoder.com/problemset/problem/1143](http://hihocoder.com/problemset/problem/1143?sid=698090)<br>
「棋盘覆盖」问题第一弹， 发现问题的本质是求fibonacci队列， 然后由于N很大，所以需要用矩阵快速幂进行加速， 注意取模。 模板题。
<br>

[http://hihocoder.com/problemset/problem/1151](http://hihocoder.com/problemset/problem/1151)<br>
「棋盘覆盖」问题第二弹， 官方给的提示非常详尽，可以去上面的地址去check。 大概思路：一行一行放置， 当前行的状态其实只跟上一行有关。 那么：
第i行不放置，则前一行必须有放置的骨牌。x对应二进制位为0，y对应二进制位为1。
第i行竖放骨牌，则前一行必须为空。x对应二进制位为1，y对应二进制位为0。
第i行横向骨牌，则前一行必须两个位置均有骨牌，否则会产生空位。x对应二进制位为1，y对应二进制位为1。
这三点是整个状态推导的关键（而且这个转移是完备的）。
这样可以得到一个状态转移的矩阵(规模是：(2^k-1)*(2^k-1))， 这样问题又转化为求矩阵快速幂
<br>

[http://hihocoder.com/problemset/problem/1162](http://hihocoder.com/problemset/problem/1162)<br>
「棋盘覆盖」问题第三弹，将第二弹的题目更加一般化处理。 所以第一步要先得到状态转移矩阵，由于K比较小， 可以先dfs得到所有的可转移的状态。然后下面问题跟第二弹一致。
