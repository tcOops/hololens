---
title: 一道题目引发的多角度思考
time: 2014.04.20 16:40:00
layout: post
post-img: /img/currey.png
more: yes
tags:
- 算法竞赛
excerpt: 无意中做到：<br>
[CF|Round207|DIV1|A](http://codeforces.com/contest/356/problem/A) 
一开始以为线段树可以秒， 果然数据是卡线段树的。后来经过考虑发现这题存在好几种方法， 特此总结一下。
---

&nbsp;&nbsp;无意中做到：[CF|Round207|DIV1|A](http://codeforces.com/contest/356/problem/A) , 一开始以为线段树可以秒， 果然数据是卡线段树的。
后来经过考虑发现这题存在好几种方法， 特此总结一下：

```
题意：输入a个人，b组数据
每行输入x，y，z三个数，表示x-y中z是获胜者（即打败了其他的人）
输出每个人被谁打败，最终获胜者输出0
```
---
方法一：线段树
最简单的成段更新。 评测结果： TLE 
可能数据卡的比较惨， 最极端的数据可能可能完美的跳过了成段更新， 成为完全的暴力。

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 3*1e5 + 10;
int col[N<<2];

void Pushdown(int rt){
    if(col[rt] != -1){
        col[rt<<1] = col[rt<<1|1] = col[rt];
        col[rt] = -1;
    }
}

void update(int L, int R, int l, int r, int rt, int c){
    if(col[rt] != -1){
        return ;
    }
    if(l == r){
        if(l != c) col[rt] = c;
        return ;
    }
    Pushdown(rt);
    int m = (l + r) >> 1;
    if(L <= m) update(L, R, l, m, rt<<1, c);
    if(R > m) update(L, R, m + 1, r, rt<<1|1, c);
}

void query(int l, int r, int rt){
    if(col[rt] != -1){
        for(int i = l; i <= r; ++i){
            printf("%d ", col[rt]);
        }
        return ;
    }
    if(l == r){
        printf("0 ");
        return ;
    }
  //  Pushdown(rt);
    int m = (l + r) >> 1;
    query(l, m, rt<<1);
    query(m + 1, r, rt<<1|1);
}

int main(){
    int n, m;
    scanf("%d%d", &m, &n);
    memset(col, -1, sizeof(col));
    for(int i = 0; i < n; ++i){
        int x, y, z;
        scanf("%d%d%d", &x, &y, &z);
        update(x, y, 1, m, 1, z);
    }
    query(1, m, 1);
    return 0;
}
```
---
方法二：树状数组
这题用树状数组可以较好的解决。
C[i] = 1, 表示还没有被打败， C[i] = 0表示已经被打败。每次二分区间， 判断当前区间是不是已经全部被击败了，如果是直接跳过。
这种方法， 无论数据怎么暴力极端， 因为存在二分，树状数组快速统计的优势都能够较好的发挥出来。 评测结果：AC | 592MS

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#define lowbit(x) ((x) & (-x))
using namespace std;
const int N = 3e5 + 10;
int C[N], ans[N];
int m, n;

void add(int rt, int d){
    while(rt <= m){
        C[rt] += d;
        rt += lowbit(rt);
    }
}

int sum(int rt){
    int ret = 0;
    while(rt > 0){
        ret += C[rt];
        rt -= lowbit(rt);
    }
    return ret;
}

void update(int l, int r, int d){
    int ls = sum(l - 1), rs = sum(r);
    if(ls == rs){
        return ;
    }
    if(l == r){
        add(l, -1);
        ans[l] = d;
        return ;
    }
    int m = (l + r) >> 1;
    update(l, m, d);
    update(m + 1, r, d);
}

int main(){
    scanf("%d%d", &m, &n);
    for(int i = 1; i <= m; ++i){
        add(i, 1);
    }
    for(int i = 0; i < n; ++i){
        int x, y, z;
        scanf("%d%d%d", &x, &y, &z);
        update(x, y, z);
        add(z, 1);
        ans[z] = 0;
    }

    for(int i = 1; i <= m; ++i){
        printf("%d ", ans[i]);
    }
    return 0;
}
```

---
方法三：这题最好的方法不是任何 区间统计类型的算法或者数据结构， 而是并查集。
并查集的fa[i]表示从i开始的第一个没有击败的人。 随着更新次数的增加这种算法的优势是很明显的， 完全跳过了那些不需要查询的点。
而前面的数据结构类型的方法， 由于是二分， 所以不能顺序跳过，只能在二分的过程中跳过某一部分。 这样看来， 并查集这种方法的确很适合这道题目的情况。
评测结果：AC | 202MS

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<set>
#define lowbit(x) ((x) & (-x))
using namespace std;
const int N = 3e5 + 10;
int ans[N], fa[N];

int find(int x){
    if(fa[x] != x){
        fa[x] = find(fa[x]);
    }
    return fa[x];
}

void gao(int x, int y, int d){
    if(x > y) return ;
    int fx = find(x);
    while(fx <= y){
        int next = find(fx);
        if(next == fx){
            ans[fx] = d;
            int fy = find(fx + 1);//care the order
            fa[fx] = fy;
            ++fx;
        }
        else{
            fx = next;
        }
    }
}

int main(){
    int n, m;
    scanf("%d%d", &m, &n);

    //init the set
    for(int i = 0; i <= m + 1; ++i){
        fa[i] = i;
    }

    for(int i = 0; i < n; ++i){
        int x, y, z;
        scanf("%d%d%d", &x, &y, &z);
        gao(x, z - 1, z);
        gao(z + 1, y, z);
    }

    for(int i = 1; i <= m; ++i){
        printf("%d ", ans[i]);
    }
    return 0;
}
```

---
第四种方法：
非主流的方法， 考虑到每个数字的唯一性， 我们用set这种集合。 评测结果：AC | 327MS

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<set>
#define lowbit(x) ((x) & (-x))
using namespace std;
const int N = 3e5 + 10;
set<int > st;
set<int >::iterator it;
int ans[N];

int main(){
    int m, n;
    scanf("%d%d", &m, &n);
    for(int i = 1; i <= m; ++i){
        st.insert(i);
    }
    st.insert(1e9);

    for(int i = 0; i < n; ++i){
        int x, y, z;
        scanf("%d %d %d", &x, &y, &z);
        it = st.lower_bound(x);
        while(*it <= y){
            ans[*it] = z;
            st.erase(it++);
        }
        ans[z] = 0;
        st.insert(z);
    }

    for(int i = 1; i <= m; ++i){
        printf("%d ", ans[i]);
    }
    return 0;
}
```
能力值get：近期算法题做的挺多的，  对于一道题目一定要多想， 多考虑时间复杂度。
要培养能够尽量优化算法好习惯。
