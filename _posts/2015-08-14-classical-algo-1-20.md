---
title: 重要算法题合集(1-20)
time: 2015.08.14
layout: post
tags:
- 算法竞赛
excerpt: 希望本集合少一点水题 :)  ｜  希望本集合的题目能一直延续一下 :) ｜ 喵咪咪～
---

希望本集合少一点水题 :)  ｜  希望本集合的题目能一直延续一下 :) | 喵咪咪～


```
//迷宫问题
//经典bfs问题， 用string记录路径
//值得记录的是， string的操作问题， sprintf的使用

#include<cstdio>
#include<string>
#include<queue>
#include<iostream>
using namespace std;
int fx[4] = {0, 1, -1, 0};
int fy[4] = {1, 0, 0, -1};
int maze[6][6];

typedef struct node{
    int x, y;
    string op;
    node(int x_, int y_, string op_):x(x_), y(y_), op(op_){}
}node;

queue<node > q;

void bfs(int x, int y){
    while(!q.empty()) q.pop();
    maze[x][y] = 1;
    node tmp(x, y, "");
    q.push(tmp);

    char buf[3];
    string buf_;
    while(!q.empty()){
        node p = q.front();
        q.pop();
        for(int i = 0; i < 4; ++i){
            int dx = p.x + fx[i];
            int dy = p.y + fy[i];
            if(dx >= 0 && dx < 5  && dy >= 0 && dy < 5 && maze[dx][dy] == 0){
                sprintf(buf, "%d", i);
                buf_ = buf;
                if(dx == 4 && dy == 4){
                    printf("(0, 0)\n");
                    int sx = 0, sy = 0;
                    for(int j = 0; j < p.op.size(); ++j){
                        int ops = p.op[j] - 48;
                        sx = sx + fx[ops];
                        sy = sy + fy[ops];
                        printf("(%d, %d)\n", sx, sy);
                    }
                    printf("(4, 4)\n");
                    return ;
                }
                tmp.x = dx, tmp.y = dy, tmp.op = p.op+buf_;
                q.push(tmp);
            }
        }
    }
}

int main(){
    for(int i = 0; i < 5; ++i){
        for(int j = 0; j < 5; ++j){
            scanf("%d", &maze[i][j]);
        }
    }
    bfs(0, 0);
    return 0;
}
```

---
```

/*
  贪心题， 每次比较左端跟右端的字符， 取小的加入输出队列
  注意输出的格式
*/

#include<cstdio>
#include<cstring>
#include<string>
using namespace std;
const int N = 2002;
char s[N];

int main(){
    int n;
    scanf("%d", &n);
    getchar();
    for(int i = 0; i < n; ++i){
        scanf("%c", &s[i]);
        getchar();
    }

    int l = 0, r = n-1;
    int cnt = 1;
    while(l <= r){
        bool flag = false;
        for(int i = 0; l + i <= r; ++i){
            if(s[l+i] < s[r-i]){
                flag = true;
                break;
            }
            else if(s[l+i] > s[r-i]){
                flag = false;
                break;
            }
        }
        if(flag){
            putchar(s[l++]);
        }
        else{
            putchar(s[r--]);
        }
        if(cnt % 80 == 0){
            puts("");
        }
        ++cnt;
    }
    puts("");
    return 0;
}
```

---
```
//经典的反转开关问题（见挑战程序设计竞赛3.2）
//枚举k
//第一头牛只被一个区间包含， 所以在确定第一头牛的是否反转，就可以确定第二头牛是否反转， 同样可以继续确定下去， 每次缩小一个区间
//所以每头牛当前是否需要翻转， 是由前面的k-1头牛翻转状态决定的， 用r[i]来记录区间[i, i+k-1]是否进行了翻转，
//如果单纯的在枚举之后， 然后暴力翻转每头牛， 然后对当前牛之前的k-1头牛的翻转状态进行统计的话， 最坏的时间复杂度将达到O(n^3)
//所以要进行优化， 显然在计算k-1头牛翻转状态的时候， 可以利用之前的结果进行递推。 这样可以省去一维而将时间复杂度将至O(n^2)


#include<cstdio>
#include<cstring>
using namespace std;
const int N = 5010;

int r[N], dir[N];
int n;

int solve(int k){
    memset(r, 0, sizeof(r));
    int ans = 0, sum = 0;
    for(int i = 0; i+k <= n; ++i){
        if((sum + dir[i]) & 1){
            ++ans;
            r[i] = 1;
        }

        sum += r[i];
        if(i-k+1 >= 0){
            sum -= r[i-k+1];
        }
    }

    for(int i = n-k+1; i < n; ++i){
        if((sum + dir[i]) & 1){
            return -1;
        }
        if(i-k+1 >= 0){
            sum -= r[i-k+1];
        }
    }
    return ans;
}

int main(){
    scanf("%d", &n);
    char op[2];
    for(int i = 0; i < n; ++i){
        scanf("%s", op);
        if(op[0] == 'B'){
            dir[i] = 1;
        }
        else{
            dir[i] = 0;
        }
    }

    int K, M = n;
    for(int k = 1; k <= n; ++k){
        int tmp = solve(k);
        if(tmp > 0 && tmp < M){
            M = tmp;
            K = k;
        }
    }
    printf("%d %d\n", K, M);
    return 0;
}
```

---
```
//经典的并查集问题， 程序虽然很短， 但是运用了不少的东西
//这道题目给出A，B，C三种类型的动物， A->B, B->C, C->A 循环食物链
//给出一些描述， 一共分两种： 1 X Y 表示X，Y同类，  2 X Y 表示X可以吃Y
//这些描述中如果与之前的状态冲突， 那么就是假话， 需要找出所有的假话
//
//很快就可以分析出应该用并查集来解决。 OK， fa数组维护是否属于一个集合
//但是普通意义上的并查集并不能完整表示状态， 因为仅仅能维护属于同一种生物的集合是不能判断谁能吃谁， 所以需要一些附加状态
//所以改变思维方式， 增加一个rank数组， 将fa数组的意义设置成： 当两个动物的关系确定的时候放在一个集合， 也就是有一个共同的fa. 而rank数组表示的是x与fa[x]之间
//的关系 依据题目的意思， rank数组的取值有三种， 将其设置为0,1,2(0表示x与fa[x]是同类生物， 1表示x被fa[x]吃， 2表示x吃fa[x]）
//所以在合并的时候， 既需要设置fa的关系， 肯定也要描述rank关系
//
//所以本质上来说， 判断x,y之间的关系， 实际上是以x,y的共同祖先fa来描述的， 通过fa这个中继， 以及x，y 与 fa的关系来推导出x,y的关系

//本题中有几个需要推导rank的地方：

//第一是op == 2的时候，也就是x吃y， 如果当前x,y之间的fa一样，也就是x,y的关系已经确定的时候， 需要推导当前x,y是什么关系才与x吃y矛盾：
//显然x,y为同种生物的时候，矛盾， 此时rank[x] = rank[y]
//或者y吃x， 这种时候可以分类出三种情况:(1)x与fa同类， y吃fa  (2)y与fa同类， fa吃x  (3)x吃fa fa吃y, 则y吃x， 可以用向量式表达
//
//第二 合并的时候(union_ function), 将fy的集合fa设置为fx, 那么要更新fy的rank， 将其设置为fy与fy的逻辑关系。 如何去联系， 当然是通过x,y这个渠道，
//以及x与fx, y与fy这个渠道， 这里有一种向量表达式的手段， 理清关系发现本质上， 吃与被吃可以用一个线性的关系表示， 这样比较容易写出表达式（很神奇）
//
//第三 合并的时候只是更新了fy的rank值， 也就是只更新了fy这一点与fa的关系， 那么fy的孩子们与新的fa之间的关系不需要更新吗？ 当然要， 不过这个过程可以留在find这个过程，
//这是一个很巧妙的设计， 在find中， 不仅更新fa, 同时也将rank保持同步更新， 而且显然可以用线性表达式来表示这个关系
//
//本题值得思考的地方在于， 如何用常见的算法， 加上特殊的状态转移， 而获取更多的信息量， 也能传递更多的信息量， 从而达到状态巧妙的转化

#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 50010;
int fa[N], rank[N];

int find(int x){
    int curFather = fa[x];
    if(fa[x] != x){
        fa[x] = find(fa[x]);
    }
    rank[x] = (rank[curFather] + rank[x])%3;
    return fa[x];
}

void union_(int x, int y, int fx, int fy, int d){
    fa[fy] = fx;
    rank[fy] = (rank[x] - rank[y] + 2 + d)%3;
}

int main(){
    int n, k;
   // while(~scanf("%d%d", &n, &k)){
        int op, x, y;
        int ans = 0;
        for(int i = 0; i <= n; ++i){
            fa[i] = i;
            rank[i] = 0;
        }

        for(int i = 0; i < k; ++i){
            scanf("%d%d%d", &op, &x, &y);
            if((op == 2 && x == y) || (x > n || y > n)){
                ++ans;
                continue;
            }
            int fx = find(x), fy = find(y);
            if(fx == fy){
                if(op == 1){
                    if(rank[x] != rank[y]){
                        ++ans;
                    }
                }
                else{
                    if(rank[x] != (rank[y] + 2)%3){
                        ++ans;
                    }
                }
            }
            else{
                union_(x, y, fx, fy, op);
            }
        }
        printf("%d\n", ans);
    //}
    return 0;
}
```

---
```
//经典搜索题， 虽然数据不大， 但是暴力dfs搜索的复杂度将是阶乘级别(o(n!))
//所以如果搜索过程中， 没有强力剪枝， 肯定是不行的
//所谓剪枝， 是指在搜索的过程中， 在进行解答树拓展的时候， 提前做一些计算进行判断， 如果按某条路径搜下去肯定没有结果， 那么就不要做无用功
//设计剪枝的方法需要总结， 从各种状态入手， 考虑数据的极限情况。

//这道题目有几个经典的剪枝：
//1： dfs到某一个stick的时候， 如果当前这一块的stick长度和加上这个stick刚好为val(设定的stick拼接长度)， 如果这种情况无法拼接的话， 那肯定不能拼接
//因为， 换成其他的效果也一样， 无非就是用几个小的来组合， 那肯定也是不行的（case1）
//
//2. 一个很重要的剪枝， 将stick按长度从大到小排序， 先搜大的。 这样做的好处是， 将越灵活的组合放在后面， 这样获得解的机会更大点。 就可以提前获得结果
//因为， 比如6， 或者2+4, 显然2+4比6更灵活， 将2+4放到后面使用， 具备更多的构造解的可能性。 这是一个关键的剪枝
//
//3. 如果是case2， 也就是当前的和加起来还不够设定值val， 在不选该stick的时候， 可以提前做一些判断： 比如当前的和为0， 那么显然这根stick是无用的，
//所以放到后面也是同样的局面， 这个解空间就是无效的。 或者：如果剩下的stick的总长度都不及这一组所需要的长度， 那肯定也是无效的

#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 65;
int l[N];
bool vis[N];
int n, cnt;

bool cmp(const int &x, int const &y){
    return x > y;
}

bool solve(int k, int s, int p, int sum, int val){
    if(k == cnt) return true;

    for(int i = p; i < n; ++i){
        if(vis[i]) continue;

        //case1
        if(s + l[i] == val){
            vis[i] = 1;
            if(solve(k+1, 0, 0, sum-l[i], val))
                return true;
            vis[i] = 0;
                return false;
        }

        //case2
        else{
            if(s + l[i] < val){
                vis[i] = 1;
                if(solve(k, s+l[i], i+1, sum-l[i], val)){
                    return true;
                }
                vis[i] = 0;
                if(s == 0 || sum-l[i] < val-s)
                    return false;
                while(l[i] == l[i+1]){
                    ++i;
                }
            }
        }
    }
    return false;
}

int main(){
    while(~scanf("%d", &n)){
        if(n == 0) break;
        memset(vis, 0, sizeof(vis));
        int sum = 0;
        for(int i = 0; i < n; ++i){
            scanf("%d", &l[i]);
            sum += l[i];
        }

        sort(l, l+n, cmp);
        for(int i = l[0]; i <= sum; ++i){
            if(sum % i == 0){
        //        printf("%d %d\n", i, sum/i);
                cnt = sum / i;
                if(solve(0, 0, 0, sum, i)){
                    printf("%d\n", i);
                    break;
                }
            }
        }
    }
    return 0;
}
```

---
```
//二分图匹配简单题
//题目首先要求判断图是否为二分图
//这里有经典的dfs染色算法可以用来进行判断， 算法具体流程很像对图进行染色（0表示未染色， 1表示染成白色， -1表示染成黑色）
//得到一个二分图之后， 可以用经典的hungary算法对二分图求最大匹配
//hungary算法的核心部分也是用dfs找增广路， 当找到增广路之后就可以将之前匹配好的路径进行求反， 从而获得更大的一个匹配
//既然这里都用到了求增广路的思想， 所以二分图也可以用网络流算法进行求解， 后面会涉及到


#include<cstdio>
#include<cstring>
#include<algorithm>
#include<vector>
using namespace std;
const int N = 201;
vector<int > g[N];
int col[N];
bool vis[N];
int n, m;
int link[N];


bool dfs(int u, int c){
    col[u] = c;
    for(int i = 0; i < g[u].size(); ++i){
        int v = g[u][i];
        if(col[v] == c)return false;
        if(col[v] == 0 && !dfs(v, -c)) return false;
    }
    return true;
}

bool isBiGraph(){
    for(int i = 1; i <= n; ++i){
        if(col[i] == 0){
            if(!dfs(i, 1)){
                return false;
            }
        }
    }
    return true;
}

bool find(int u){
    for(int i = 0; i < g[u].size(); ++i){
        int v = g[u][i];
        if(vis[v] == 0){
            vis[v] = 1;
            if(link[v] == -1 || find(link[v])){
                link[v] = u;
                return true;
            }
        }
    }
    return false;
}

void hungary(){
    int ans = 0;
    for(int i = 1; i <= n; ++i){
        memset(vis, 0, sizeof(vis));
        if(find(i)){
            ++ans;
        }
    }
    printf("%d\n", ans>>1);
}

int main(){
    while(~scanf("%d%d", &n, &m)){
        for(int i = 1; i <= n; ++i){
            g[i].clear();
        }
        for(int i = 0; i < m; ++i){
            int x, y;
            scanf("%d%d", &x, &y);
            g[x].push_back(y);
            g[y].push_back(x);
        }

        memset(link, -1, sizeof(link));
        memset(col, 0, sizeof(col));

        if(!isBiGraph()){
            printf("No\n");
        }
        else{
            hungary();
        }
    }
    return 0;
}
```