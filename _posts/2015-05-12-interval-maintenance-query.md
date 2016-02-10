---
title: 数据结构：区间维护与查询
time: 2015.05.12
layout: post
tags:
- 算法竞赛
excerpt: 区间维护， 是指数据常常是动态变化的， 所以查询的结果也会动态发生变化， 所以简单的线性暴力查询的复杂度已经太高了， 所以需要一些新的办法或者减少冗余计算， 或者设计新的数据结构结合二分等常用算法来实现更低的复杂度
---

```
区间维护， 是指数据常常是动态变化的， 所以查询的结果也会动态发生变化， 所以简单的线性暴力查询的复杂度已经太高了， 所以需要一些新的办法或者减少冗余计算， 或者设计新的数据结构结合二分等常用算法来实现更低的复杂度*</font>
```

---
### 线段树

```
线段树作为一种重要信息统计数据结构， 对付一个较大区间的动态变化， 能够在复杂度很低的情况下给出快速的统计
```

```
一般情况下约定：
* PushUP(int rt)是把当前结点的信息更新到父结点
* PushDown(int rt)是把当前结点的信息更新给儿子结点
* rt表示当前子树的根(root),也就是当前所在的结点
* update表示对线段树更新的函数
* query表示对线段树进行查询的函数
```

这样重点被抽分出来很清晰，就是如何设计这几个函数。

```
(1)单点更新
> 最简单的线段树分类， 每次只更新一个叶子节点。
```

<br>

```
(2)成段更新
> 每次更新一个区间的值，这里有一种很重要的思想： Lazy标记，或者叫做延迟更新。
这种思想就是能够对某一段进行整体操作的时候就先进行一个整体的信息保存， 等到真正需要细分这一区间的信息的时候再根据标记去把信息传递下去，这里的操作很灵活，是设计的难点。
```

* POJ1436

一道简单的成段更新的题目。

题意：给一个二维坐标下有一些线段。 已知每条线段的y1, y2, x分贝代表线段的y轴的起始点 以及在X轴的位置。现在要求这样的三元组：（x,y,z）使得这三个点之间两两互为可见。 x, y两点互为可见是指; 存在一条平行于X州的的线段可以连接x,y且这条线段不会与任何其他的线段有交点。

分析：首先将所有的线段按照X轴的位置从小到大排序。从左到右扫描， 判断当前线段的区间，在之间的线段覆盖之后各段都是由哪条线段覆盖的。 好比从当前的线段发出一束光（平行光）， 射向左边， 看哪些线段被光照到了。同时Y能照到X， X肯定可以照到Y。
注意的是在处理过程中要考虑这样一种情况：比如区间[1，2] [3，5]可能直接按线段树处理的话区间[1,5]会被处理， 但实际上[2,3]这一段是空的。处理的办法是：将所有的点的纵坐标的值都*2, 这样就可以将线段的端点之间区分开。

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 8010;
bool vis[N][N];
int cov[N<<3];
struct P{
    int x, y1, y2;
    P() {};
}a[N];

bool cmp(P a, P b){
    return a.x < b.x;
}

void PushDown(int rt){
    if(cov[rt] != -1){
        cov[rt<<1] = cov[rt<<1|1] = cov[rt];
        cov[rt] = -1;
    }
}

void update(int L, int R, int rt, int l, int r, int c){
    if(L <= l && R >= r){
        cov[rt] = c;
        return ;
    }
    PushDown(rt);
    int m = (l + r) >> 1;
    if(L <= m) update(L, R, rt<<1, l, m, c);
    if(R > m) update(L, R, rt<<1|1, m + 1, r, c);
}

void query(int L, int R, int rt, int l, int r, int c){
    if(cov[rt] != -1){
        vis[cov[rt]][c] = 1;
        return ;
    }
    if(l == r) return ;
    int m = (l + r) >> 1;
    if(L <= m) query(L, R, rt<<1, l, m, c);
    if(R > m) query(L, R, rt<<1|1, m + 1, r, c);
}

int main(){
    int T;
    scanf("%d", &T);
    while(T--){
        int n;
        scanf("%d", &n);
        for(int i = 0; i < n; ++i){
            scanf("%d%d%d", &a[i].y1, &a[i].y2, &a[i].x);
        }
        sort(a, a + n, cmp);
        memset(cov, -1, sizeof(cov));
        memset(vis, 0, sizeof(vis));
        for(int i = 0; i < n; ++i){
            query(a[i].y1<<1, a[i].y2<<1, 1, 1, N<<1, i);
            update(a[i].y1<<1, a[i].y2<<1, 1, 1, N<<1, i);
        }

        int ans = 0;
        for(int i = 0; i < n; ++i){
            for(int j = 0; j < n; ++j){
                if(vis[i][j]){
                    for(int k = 0; k < n; ++k){
                        if(vis[i][k] && vis[j][k])
                            ++ans;
                    }
                }
            }
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

* POJ3225

这是一道非常好的成段更新的题目， 用到了延迟标记， 而且具有一定的逻辑性。我参考了HH神牛的代码才完成（悲剧的是几乎变成写的一样的代码）

题意： 给出几个常见的操作的定义， 求初始为空的区间经过给定的操作之后区间的情况。

分析： 首先了解几种操作的含义：

```
    U:把区间[l,r]覆盖成1
    I:把[-∞,l)(r,∞]覆盖成0
    D:把区间[l,r]覆盖成0
    C:把[-∞,l)(r,∞]覆盖成0 , 且[l,r]区间0/1互换
    S:[l,r]区间0/1互换
    cov（1,0,-1），其中0，1表示覆盖0或者1， -1表示没有lazy标记，   xor（0,1）表示异或操作。 
 ```
 比较简单的操作是线段覆盖（cov），一旦遇到覆盖肯定就被覆盖了， xor此时一定为0（没有异或关系）。
如果是xor操作， 那么如果此时cov值不等于-1即这一段的值统一标记，那我可以直接改变这一段区间的值取反即可， 否则就只能去改变它的xor标记了。

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int N = 65536*2 + 10;
int Xor[N << 2];
int cov[N << 2];
bool vis[N];

void PXor(int rt){
    if(cov[rt] != -1){
        cov[rt] ^= 1;
    }
    else{
        Xor[rt] ^= 1;
    }
}

void PushDown(int rt){
    if(cov[rt] != -1){
        cov[rt<<1] = cov[rt<<1|1] = cov[rt];
        Xor[rt<<1] = Xor[rt<<1|1] = 0;
        cov[rt] = -1;
    }
    if(Xor[rt]){
        PXor(rt<<1);
        PXor(rt<<1|1);
        Xor[rt] = 0;
    }
}

void update(char op, int L, int R, int l, int r, int rt){
    if(L <= l && R >= r){
        if(op == 'U'){
            cov[rt] = 1;
            Xor[rt] = 0;
        }
        else if(op == 'D'){
            cov[rt] = 0;
            Xor[rt] = 0;
        }
        else if(op == 'C' || op == 'S'){
            PXor(rt);
        }
        return ;
    }

    PushDown(rt);
    int m = (l + r) >> 1;
    if(L <= m) update(op, L, R, l, m, rt<<1);
    else if(op == 'I' || op == 'C'){
        cov[rt<<1] = Xor[rt<<1] = 0;
    }
    if(m < R) update(op, L, R, m + 1, r, rt<<1|1);
    else if(op == 'I' || op == 'C'){
        cov[rt<<1|1] = Xor[rt<<1|1] = 0;
    }
}

void query(int l, int r, int rt){
    if(cov[rt] == 1){
        for(int i = l; i <= r; ++i){
            vis[i] = 1;
        }
        return;
    }
    else if(cov[rt] == 0){
        return ;
    }
    PushDown(rt);
    int m = (l + r) >> 1;
    query(l, m, rt<<1);
    query(m + 1, r, rt <<1|1);
}

int main(){
    char op , l , r;
	int a , b;
	while ( ~scanf("%c %c%d,%d%c\n",&op , &l , &a , &b , &r) ) {
		a <<= 1 , b <<= 1;
		if (l == '(') a ++;
		if (r == ')') b --;
		if (a > b) {
			if (op == 'C' || op == 'I') {
				cov[1] = Xor[1] = 0;
			}
		} else update(op, a, b, 0, N - 1, 1);
	}

    query(0, N - 1, 1);
    bool flag = 0;
    int s = -1, e;
    for(int i = 0; i < N; ++i){
        if(vis[i]){
            if(s == -1){
                s = i;
            }
            e = i;
        }
        else{
            if(s != -1){
                if(flag) printf(" ");
                flag = 1;
                printf("%c%d,%d%c", s&1 ? '(' : '[', s >> 1, (e + 1) >> 1, e&1 ? ')' : ']');
                s = -1;
            }
        }
    }
    if (!flag) printf("empty set");
    printf("\n");
    return 0;
}
```

<br>

```
(3)区间合并
> 区间合并经常会要求在update的时候， 要向上更新PushUp.
```

* POJ3667
这是一道典型的区间合并的题目， 同时也用到了成段更新。

题意：给出一个区间出事为空， 有两种操作：1 是给出长度为a的线段，问有没有区间可以放得下， 如果有的话 尽量往左放。 2 是给出区间[a, b]将这一段区间清空。

分析：因为有可以将区间清空或者填满的操作， 在操作过程中会涉及到区间的合并。
设定三个数组分别lsum[rt], rsum[rt], msum[rt]分别表示区间中以左端点开始的最长连续空间， 以右端点开始的最长连续空间， 区间最长的连续空间。 其中lsum, rsum 是为了区间合并时提供足够的信息。

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define maxab(a,b) ((a) > (b) ? (a) : (b))
using namespace std;
const int N = 50010;
int lsum[N<<2], rsum[N<<2], msum[N<<2];
int cov[N<<2];

void build(int rt, int l, int r){
    lsum[rt] = rsum[rt] = msum[rt] = (r - l + 1);
    cov[rt] = -1;
    if(l != r){
        int m = (l + r) >> 1;
        build(rt<<1, l, m);
        build(rt<<1|1, m + 1, r);
    }
}

void PushDown(int rt, int m){
    if(cov[rt] != -1){
        cov[rt<<1] = cov[rt<<1|1] = cov[rt];
        if(cov[rt] == 0){
            lsum[rt<<1] = rsum[rt<<1]= msum[rt<<1] = m - (m>>1);
            lsum[rt<<1|1] = rsum[rt<<1|1] = msum[rt<<1|1] = m>>1;
        }
        else{
            lsum[rt<<1] = rsum[rt<<1] = 0;
            lsum[rt<<1|1] = rsum[rt<<1|1] = 0;
            msum[rt<<1|1] = msum[rt<<1] = 0;
        }
        cov[rt] = -1;
    }
}

void PushUp(int rt, int m){
    lsum[rt] = lsum[rt<<1];
    rsum[rt] = rsum[rt<<1|1];
    if(lsum[rt] == m - (m>>1)) lsum[rt] += lsum[rt<<1|1];
    if(rsum[rt] == m>>1) rsum[rt] += rsum[rt<<1];
    msum[rt] = maxab(rsum[rt<<1] + lsum[rt<<1|1], maxab(msum[rt<<1], msum[rt<<1|1]));
}

void update(int L, int R, int rt, int l, int r, int c){
    if(L <= l && R >= r){
        lsum[rt] = rsum[rt] = msum[rt] = c ? 0: (r - l + 1);
        cov[rt] = c;
        return ;
    }
    PushDown(rt, r - l + 1);
    int m = (l + r) >> 1;
    if(L <= m) update(L, R, rt<<1, l, m, c);
    if(R > m) update(L, R, rt<<1|1, m + 1, r, c);
    PushUp(rt, r - l + 1);
}

int query(int rt, int l, int r, int x){
    if(l == r) return l;
    PushDown(rt, r - l + 1);
    int m = (l + r) >> 1;
    if(msum[rt<<1] >= x) return query(rt<<1, l, m, x);
    else if(rsum[rt<<1] + lsum[rt<<1|1] >= x) return m - rsum[rt<<1] + 1;
    else return query(rt<<1|1, m + 1, r, x);
}

int main(){
    int n, m;
    scanf("%d%d", &m, &n);
    build(1, 1, m);
    while(n--){
        int op, a, b;
        scanf("%d", &op);
        if(op == 1){
            scanf("%d", &a);
            if(a > msum[1]) printf("0\n");
            else{
                int p = query(1, 1, m, a);
                printf("%d\n", p);
                update(p, p + a - 1, 1, 1, m, 1);
            }
        }
        else{
            scanf("%d%d", &a, &b);
            update(a, a + b - 1, 1, 1, m, 0);
        }
    }
    return 0;
}
````
<br>




