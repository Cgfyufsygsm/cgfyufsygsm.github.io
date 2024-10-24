---
title: 程序设计实习（实验班）期末复习笔记
date: 2024-06-09 19:22:11
tags: 
  - 本科课程
  - 现代算法
---

## 前言

要死掉了

## Random

### 前置知识

随机算法的流程：无偏估计->P[ALG is correct] >= Constant -> 多次重复降低失败率

算法 1：最大割的 2 - 近似，每次随机选点加入 $S$，重复若干次

Median Trick：若一个黑盒能以 $p>0.5$ 概率正确回答某个 Yes/No 问题答案，那么可以通过重复 $T = O(\log 1/\delta)$ 来达到 $1- \delta$ 的正确率（Chernoff bound）

C++ 的随机库使用方法：

```cpp
random_device rd;
mt19937 gen(rd());
uniform_int_distribution<> distrib(1, 6); // 生成 [1,6]

x = distrib(gen);
```

不要直接取模，可能导致随机分布被破坏

### 快速测试矩阵相乘 (hw3) (1/3)

$3$ 个 $n$ 阶矩阵 $A,B,C$，判断 $AB=C$ 是否成立。

$O(n^2)$ 随机算法：

- 随机选取一个 $n$ 维 $\{0,1\}$ 向量 $x$
- 判断 $ABx=Cx$ 是否成立（注意到挨个从右往左乘可以 $O(n^2)$）
- 事实上，重复 $3$ 次可以通过。

### Rejection Sampling (hw4)

抛均匀硬币生成 $p=1/4$ 的两点分布是容易的（扔两次）

但有限次是没法弄出 $p=1/3$ 的。

但是，我们扔两次，可能出现的情况有 HH TH HT TT，我们把 TT 给 reject 掉，自然就有了。

对于一个一般的 $p$，考虑其二进制表示，在这里重复个 16 次就可以通过了

```cpp
class Generator{
    private:
        minstd_rand gen;
    public:
        int rand(){ return gen() % 2; }
};
int bern(double p, Generator *G){
for (int i = 0; i < 16; ++i) {
    p *= 2;
    int cur = 0;
    if (p > 1) cur = 1, p -= 1;
    if (G->rand() == cur) return cur;
  }
  return 0;
}
```

### 亚线性求中位数 (hw5)

非常简单粗暴，均匀独立采样 $O(1/\epsilon ^2)$ 次，找这个采样上的中位数并返回就可以了，以大常数概率结果在 $(0.5\pm 2\epsilon) N$ 位上。

### d-median 的数据摘要 (hw6)

均匀采样有其局限性。

> 例题（1-median 的数据摘要）：
>
> $n$ 个整数 $A$，要求预处理后高效查询，给定 $q$，返回 $\displaystyle\sum_{i=1}^n|q-a_i|$

我们需要一个 $S\subseteq A$，以及对应的权重 $w(x)$，使得 $\forall q$ 有 $\displaystyle\sum_{a\in S}w(a)|q-a|\in (1\pm \epsilon)\operatorname{cost}(A,q)$。这个时候就不能使用均匀采样了。

**我们需要考虑一种均匀采样适用的子集，然后在那上面随机采样**。

分割方法就是选一个 $c$，然后按 $[r_i,2r_i]$ 分割成环。

```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;
using db = double;
const int maxn = 1e5 + 5;
const db eps = 0.2;

int n, q;

struct Point {
  int x, y, z;
  Point() {}
  Point(int _x, int _y, int _z) : x(_x), y(_y), z(_z) {}
  db dis;
} p0[maxn];

struct Po1nt : Point {
  Po1nt(Point p, db w) {
    x = p.x, y = p.y, z = p.z;
    weight = w;
  }
  db weight;
};

vector<Point> vec[33];
vector<Po1nt> S;

db dist(Point a, Point b) {
  auto sq = [](db x) {return x * x;};
  return sqrt(sq(a.x - b.x) + sq(a.y - b.y) + sq(a.z - b.z));
}

int main() {
  scanf("%d %d", &n, &q);
  for (int i = 1; i <= n; ++i) {
    scanf("%d %d %d", &p0[i].x, &p0[i].y, &p0[i].z);
  }
  minstd_rand gen;
  int center = ceil(1.0 * gen() / gen.max() * n);
  db costC = 0;
  for (int i = 1; i <= n; ++i) {
    costC += (p0[i].dis = dist(p0[i], p0[center]));
  }
  sort(p0 + 1, p0 + n + 1, [](const auto &a, const auto &b) {return a.dis < b.dis;});
  db r = eps * costC / n;
  int curRing = 0;
  for (int i = 1; i <= n; ++i) {
    while (p0[i].dis > r) {
      ++curRing;
      r *= 2;
    }
    vec[curRing].push_back(p0[i]);
  }
  int m = 1 / eps / eps * log(log(n / 0.2));
  for (int i = 0; i <= curRing; ++i) {
    int mcur = min(m, (int)vec[i].size());
    for (int j = 1; j <= mcur; ++j) {
      int idx = floor(1.0 * gen() / gen.max() * vec[i].size());
      S.push_back(Po1nt(vec[i][idx], 1.0 * vec[i].size() / mcur));
    }
  }
  while (q--) {
    int x, y, z;
    scanf("%d %d %d", &x, &y, &z);
    Point p(x, y, z);
    db ans = 0;
    for (auto x : S) {
      ans += dist(p, x) * x.weight;
    }
    printf("%lf\n", ans);
  }
  return 0;
}
```

## Hashing

### Count-min Sketch 求 k-Heavy Hitter (hw7)

Count-min Sketch 是一个数据结构，支持对于误差参数 $\epsilon$：

- 插入 $N$ 个 $[n]$ 上的元素后，给定一个 $x\in[n]$，能估计 $x$ 出现了多少次
- 以大概率满足 $C\le \hat{C} \le C + \epsilon N$
- 总使用空间 $O(\text{poly}\log n / \epsilon)$，单次插入/查询也是。

大致的算法：

- 构造一个随机哈希 $h:[n]\to [m]$
- 每个元素加到对应的 $h(x)$ 上
- 发现只会造成对结果的高估（哈希冲突）
- 开多个 $h$，取 $\min$ 即可

应用：求 k-HH，即出现次数 $\ge N/k$ 的元素，是数据流算法

流程：

- 初始化 $M=0$，开一个 count-min sketch，维护一个集合 $L$
- 插入 $x\in[n]$ 时：
  - $M$++，将 $x$ 插入，若次数 $\ge M/k$，插入 $L$
  - 遍历 $L$，从 $L$ 中删掉次数 $<M/k$ 的元素
- 数据流结束的时候，$L$ 就是答案

### 其他应用

频数向量近似 Inner Product：直接套 count-min sketch 就可以了，当然做内积的时候需要用同一组哈希，最后再取最小值。

Bloom Filter：

- 维护一个集合 $S$，支持 $O(1)$ 插删，$O(n)$ 空间（$n$ 是 $S$ 中最大元素个数），$O(1)$ 回答某个元素是否在 $S$ 里。
- 在的话一定回答 Yes，不在的话回答 Yes 的概率是 $1-\delta$
- 一样是开 $T$ 个哈希，对应 $T$ 个 $O(n)$ 的 0-1 桶
- 有 $0$ 就返回 No

## Distance

### MinHash 近似 Jaccard Similarity (hw8) (1/3)

对于两个集合 $A,B\subseteq [n]$，定义其 Jaccard 相似度为：
$$
J(A,B):=\begin{cases}
\frac{|A\cap B|}{|A\cup B|} &A\cup B \ne \varnothing\\
0&\text{else}
\end{cases}
$$
好像就是 IoU 啊，whatever

需求：有很多集合 $A_i$，多次查询 $J(A_i,A_j)$（类比文本相似度之类的）

对于每个集合 $A_i$，可以预处理一个哈希值 $h(A_i)$，然后 $O(1)$ 回答单次询问

设 $h:[n]\to [0,1]$ 为一随机哈希，对每个 $A_i$ 计算 $h(A_i) = \min_{x\in A_i}\{h(x)\}$

我们有 Claim：$\operatorname{Pr}[h_\min(A) = h_\min(B)] = J(A,B)$。

所以我们有算法，用 $T$ 个独立的随机哈希，然后看两个集合的 MinHash 相等的概率。其实实现起来挺简单的。

```cpp
#include <bits/stdc++.h>

using namespace std;

const int maxn = 1e3 + 5;
const int T = 50;
double h[maxn][T];

typedef unsigned long long u64;

#define mix(h)                                                                 \
    ({                                                                         \
        (h) ^= (h) >> 23;                                                      \
        (h) *= 0x2127599bf4325c37ULL;                                          \
        (h) ^= (h) >> 47;                                                      \
    })

u64 fasthash64(u64 v, u64 seed) {
    const uint64_t m = 0x880355f21e6d1965ULL;
    u64 h = seed;
    h ^= mix(v);
    h *= m;
    return mix(h);
}

double hashing(int x, int k) {
  return 1.0 * fasthash64(x, fasthash64(k, k)) / ULLONG_MAX;
}

int main() {
  int n, m, q;
  scanf("%d %d %d", &n, &m, &q);
  for (int i = 1; i <= n; ++i) {
    for (int k = 0; k < T; ++k)
        h[i][k] = 1;
    for (int j = 1; j <= m; ++j) {
      int x; scanf("%d", &x);
      for (int k = 0; k < T; ++k)
        h[i][k] = min(h[i][k], hashing(x, k));
    }
  }
  while (q--) {
    int x, y; scanf("%d %d", &x, &y);
    int cnt = 0;
    for (int k = 0; k < T; ++k) cnt += (abs(h[x][k] - h[y][k]) <= 1e-8);
    printf("%lf\n", 1.0 * cnt / T);
  }
  return 0;
}
```

### Cosine Similarity 与  SimHash

对于两个向量 $x,y\in\mathbb R^d$，定义 $\sigma(x, y) :=\displaystyle\frac{\langle x, y\rangle}{||x||_2||y||_2}$，对于一篇文档，做成一个单词为下标，值为 TF-IDF 值的向量。可以用 Cosine Similarity 来衡量相似度。

我们可以用 SimHash。

算法：

- 生成一个 $d$ 维随机高斯 $w$（生成 $d$ 个独立的标准正态变量然后归一化，即在 $d$ 维球面选一个随机点）
- 定义 $h(x):=\displaystyle \operatorname{sgn}(\langle w, x\rangle)$
- Claim：$\forall x,y$，$\operatorname{Pr}[h(x)\ne h(y)] = \displaystyle\frac{\theta(x,y)}{\pi}$
- 类似 MinHash，做 $T$ 次独立的 SimHash 然后取平均
- 针对 $x,y$ 的估计量为 $\displaystyle\frac 1T\sum_{t=1}^T[h^{(t)}(x)\ne h^{(t)}(y)]$
- 将 $\mathbb R^d$ 上的点映射到了 $T$ 维的 Hamming 空间 $f$。上述估计量就是 $\operatorname{dist}(f(x),f(y)) / T\approx \theta(x,y) / \pi$

### Hamming 空间近似最近邻查询 (hw9)

目标：

- $n$ 个 $\mathbb{H}^d$ 上的点
- 要求 $O(dn+n^{1+1/c})$ 的预处理时间
- $\forall q \in\mathbb H^d$ 在 $O(n^{1/c})$ 时间内找到 $c-$最近邻（$c\ge 1$）

思路：

- 设查询点 $q$ 的真正最近邻为 $q^*$，距离为 $r$
- Observation：将所有坐标随机重排，则 $\exists k$：
  - $q$ 和 $q^*$ 前 $k$ 位完全相等的概率较大
  - 对于任何距离 $q$ 超过 $cr$ 的 $q'$，前 $k$ 位完全相等的概率比较小
- 所以，将坐标随机排列后找 $q$​ 的 LCP
- 算了开摆了

## 欧式距离

现在假设有 $n$ 个 $d$ 维，坐标 $\in[\Delta]$ 范围的数据点。

近似的核心在于**离散化/找代表点**

### 格点离散化：线性时间 (1+eps)-近似直径 (hw10)

- $O(n)$ 时间找一个 2-近似值 $T$

- 作 $\ell :=\epsilon T / \sqrt 2$ 的网格，所有点移动到网格中心（实操时移动到左上角）

  每个点移动了 $\le \sqrt2 \ell \le \epsilon \cdot \text{diam}$

- 在新的点集里面 $O(d|P'|^2)$ 暴力求直径

### 递归格点离散化：四分树求解 (1+eps)-近似最近邻查询 (hw11)

四分树的建树过程：

```cpp
Node* build(db x1, db x2, db y1, db y2, vector<Point> P) {
  Node *u = new Node();
  u->x1 = x1;
  u->x2 = x2;
  u->y1 = y1;
  u->y2 = y2;
  u->diam = sqrt((x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 - y1));
  if (P.size() == 1) {
    u->rep = P[0];
    return u;
  }
  db xMid = (x1 + x2) / 2, yMid = (y1 + y2) / 2;
  vector<Point> Ps[4];
  for (auto p : P) {
    if (p.x <= xMid && p.y <= yMid) Ps[0].push_back(p);
    else if (p.x > xMid && p.y <= yMid) Ps[1].push_back(p);
    else if (p.x <= xMid && p.y > yMid) Ps[2].push_back(p);
    else if (p.x > xMid && p.y > yMid) Ps[3].push_back(p);
  }
  if (!Ps[0].empty()) u->ch[0] = build(x1, xMid, y1, yMid, Ps[0]);
  if (!Ps[1].empty()) u->ch[1] = build(xMid, x2, y1, yMid, Ps[1]);
  if (!Ps[2].empty()) u->ch[2] = build(x1, xMid, yMid, y2, Ps[2]);
  if (!Ps[3].empty()) u->ch[3] = build(xMid, x2, yMid, y2, Ps[3]);
  u->rep = P[gen() % P.size()];
  return u;
}
```

完整算法：

- 设 $A_i$ 为需要检查的四分树第 $i$ 层格子的集合

- 初始 $A_0$ 放入最大格子，其余 $A_i$ 初始化为空，$r=\infin$，$\hat{q} =\text{NULL}$

- for 层数 $i$：

  - 设 $S$ 为所有 $A_{i-1}$ 格子的 $2^d$ 个子格子的集合
  - 对每个 $\square_w\in S$，若 $||q - \mathrm{rep}_w||<r$，更新 $r$ 和 $\hat{q}$
  - 对每个 $\square_w\in S$，若 $r>(1+\epsilon)(||q  - \mathrm{rep}_w|| - \rm{diam}(\square_w))$，将 $\square_w$ 放入 $A_i$

- 最后返回 $\hat q$​

根本要求是我们不能 miss 掉 $q^*$ 的所有 $(1+\epsilon)$ 近似，如果一个格子里面还是有希望成为 $(1+\epsilon)$ 近似的话，我们不能错过他。

```cpp
#include <bits/stdc++.h>

using namespace std;
using db = double;
const db eps = 0.15;
minstd_rand gen;
struct Point {
  db x, y;
  int id;
};

struct Node {
  Node* ch[4];
  Point rep;
  db x1, x2, y1, y2, diam;
};

db dist(db x1, db y1, db x2, db y2) {
  return sqrt((x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2));
}

db dist(Point p, db x, db y) { return dist(p.x, p.y, x, y); }

Node* build(db x1, db x2, db y1, db y2, vector<Point> P);

int main() {
  int n, q;
  scanf("%d %d", &n, &q);
  vector<Point> P;
  for (int i = 1; i <= n; ++i) {
    db x, y; scanf("%lf %lf", &x, &y);
    P.push_back({x, y, i});
  }
  Node* root = build(0, 1e6, 0, 1e6, P);
  while (q--) {
    db qx, qy;
    scanf("%lf %lf", &qx, &qy);
    db mind = 1e18;
    int ans = -1;
    queue<Node*> A;
    A.push(root);
    while (!A.empty()) {
      Node* cur = A.front(); A.pop();
      for (int i = 0; i < 4; ++i) if (cur->ch[i] != nullptr) {
        db dis = dist(cur->ch[i]->rep, qx, qy);
        if (dis < mind) {
          mind = dis;
          ans = cur->ch[i]->rep.id;
        }
        if ((1 + eps) * (dis - cur->ch[i]->diam) < mind) A.push(cur->ch[i]);
      }
    }
    printf("%d\n", ans);
  }
}
```

### 点对离散化：WSPD 求解精确最经典对和低维 (1+eps) MST (hw12, 13)

WSPD 是对点对的离散化。直觉就是将很远的两个点集直接缩成两个点。

定义：一个 $\epsilon$-WSPD 是指一系列 $\{\{A_i,B_i\}\}_i$ s. t.

- $\forall i$ 有 $A_i,B_i\subseteq P$ 且 $\max(\mathrm{diam}(A_i),\mathrm{diam}(B_i))\le \epsilon\cdot \mathrm{dist}(A_i,B_i)$
- $\bigcup_i A_i\times B_i = P \times P$

利用四分树：

```cpp
void WSPD(Node* u, Node* v) {
  if (u == nullptr || v == nullptr) return;
  if (u == v && u->size == 1) return;
  if (u->diam() < v->diam()) swap(u, v);
  if (u->diam() <= eps * dist(u, v)) {
    wspd.push_back({u, v});
  }
  for (int i = 0; i < 8; ++i) WSPD(u->ch[i], v);
  return;
}
```

应用：精确求最近点对。

- 构造一个 $\epsilon < 1$ 的 WSPD
- 对所有 $\{A_i,B_i\}$，若 $|A_i| = |B_i| = 1$，记录 $A_i$ 和 $B_i$ 中唯一点的距离
- 所有这样的距离中最短的就是答案

原因：最近点对必然是分居在一对的两个里面的，否则显然矛盾。

应用：求 $(1+\epsilon)$-spanner。

$t$-spanner 是数据集的欧式图的子图 $H$。要求对于任何 $x,y\in P$ 有 $|x-y|\le \mathrm{dist}_H(x,y)\le t|x-y|$（t-stretch），且边数尽量少。

算法：求 $\epsilon/4$​-WSPD，然后对每对 $\{A_i,B_i\}$ 连接其代表点。

在 $(1+\epsilon)$-spanner 上求 MST 即得到原图的 $(1+\epsilon)$-近似 MST。

```cpp
#include <bits/stdc++.h>

using namespace std;
const int maxn = 5e4 + 5;
using db = double;
int n;
mt19937 rnd;

struct Point {
  db coord[3];
  int id;
  Point(int i) {
    id = i;
    for (int i = 0; i < 3; ++i) {
      int x; scanf("%d", &x);
      coord[i] = x;
    }
  }
  Point () {}
};

struct Node {
  int ch[8];
  int size;
  db diam;
  Point rep;
  vector<db> l, r;
  db delta() {
    return size < 2 ? 0 : diam;
  }
} t[(int)2e5];
int tot, root;

struct Edge {
  int i, j; db d;
};
vector<Edge> E;

int build(vector<Point> P, vector<db> l, vector<db> r) {
  int u = ++tot;
  // printf("building %d\n", u);
  t[u].size = P.size();
  t[u].rep = P[rnd() % P.size()];
  t[u].l = l, t[u].r = r;
  auto calcDiam = [](vector<db> l, vector<db> r) {
    db res = 0;
    for (int i = 0; i < 3; ++i) res += (l[i] - r[i]) * (l[i] - r[i]);
    return sqrt(res);
  };
  t[u].diam = calcDiam(l, r);
  if (P.size() == 1) return u;
  vector<db> mid(3);
  vector<Point> Ps[8];
  for (int i = 0; i < 3; ++i) mid[i] = (l[i] + r[i]) / 2;
  for (auto p : P) {
    int id = 0;
    for (int i = 0; i < 3; ++i) {
      if (p.coord[i] > mid[i])
        id |= (1 << i);
    }
    Ps[id].push_back(p);
  }
  for (int id = 0; id < 8; ++id) {
    vector<db> ll, rr;
    for (int i = 0; i < 3; ++i) {
      if ((id >> i) & 1) {
        ll.push_back(mid[i]);
        rr.push_back(r[i]);
      } else {
        ll.push_back(l[i]);
        rr.push_back(mid[i]);
      }
    }
    if (!Ps[id].empty()) t[u].ch[id] = build(Ps[id], ll, rr);
  }
  return u;
}

int fa[maxn];
int find(int x) {return x == fa[x] ? x : fa[x] = find(fa[x]);}

db dist(const Point &a, const Point &b) {
  db res = 0;
  for (int i = 0; i < 3; ++i) res += (a.coord[i] - b.coord[i]) * (a.coord[i] - b.coord[i]);
  return sqrt(res);
}

db dist(int x, int y) {
  db res = 0;
  for (int i = 0; i < 3; ++i) {
    db d = 0;
    d = max(d, t[x].l[i] - t[y].r[i]);
    d = max(d, t[y].l[i] - t[x].r[i]);
    res += d * d;
  }
  return sqrt(res);
}

void WSPD(int x, int y) {
  // printf("wspd %d %d\n", x, y);
  if (x == y && t[x].size < 2) {
    // puts("r1");
    return;}
  if (t[x].delta() < t[y].delta()) swap(x, y);
  if (t[x].delta() <= 0.5 * dist(x, y)) {
    E.push_back({t[x].rep.id, t[y].rep.id, dist(t[x].rep, t[y].rep)});
    return;
  }
  for (int i = 0; i < 8; ++i) if (t[x].ch[i]) WSPD(t[x].ch[i], y);
}

int main() {
  // freopen("1.in", "r", stdin);
  scanf("%d", &n);
  vector<Point> P;
  for (int i = 1; i <= n; ++i) {
    P.push_back(Point(i));
  }
  vector<db> l, r;
  for (int i = 0; i < 3; ++i) {
    l.push_back(0);
    r.push_back((1 << 30));
  }
  root = build(P, l, r);
  // puts("built");
  for (int i = 1; i <= n; ++i) fa[i] = i;
  WSPD(root, root);
  sort(E.begin(), E.end(), [](const Edge &a, const Edge &b) {return a.d < b.d;});
  int linked = 0;
  for (auto &e : E) {
    int u = find(e.i), v = find(e.j);
    if (u != v) {
      fa[u] = v;
      printf("%d %d\n", e.i, e.j);
      if (++linked == n - 1) break;
    }
  }
  return 0;
}
```

### 随机平移四分树与 Tree Embedding 求解高维 MST (hw14)

Tree embedding：将数据集 $P$ 从 $\mathbb R^d$ 映射到 $T(V,E)$ 上，其中 $P\subseteq V$。**用树上的距离来近似欧氏距离**

主要衡量 metric: distortion，定义为
$$
\max_{x\ne y\in P}\frac{\mathrm{dist}_T(x,y)}{|x-y|}\ge 1
$$
构造算法：

- 随机平移 $P$​ 的所有向量（不平移也没事）
- 构造四分树
- 定义树 $T$，每个四分树上的节点对应一个树节点，数据点**都放在叶子上**
- 定义边：每个四分树上的节点连接的所有直接儿子，赋予 $\sqrt d\cdot $ 边长 的权值

可以在这上面直接近似求 MST：

```cpp
#include <bits/stdc++.h>

using namespace std;
using db = double;
const int maxn = 5e4 + 5;
vector<int> point[maxn];
int n, D, last = -1;

void dfs(vector<int> P, vector<int> l, vector<int> r) {
  if (P.size() == 1) {
    if (last != -1) printf("%d %d\n", last, P[0]);
    last = P[0];
    return;
  }
  map<int, vector<int>> mp;
  vector<int> mid(D);
  for (int i = 0; i < D; ++i) {
    mid[i] = (l[i] + r[i]) >> 1;
  }
  for (auto idx : P) {
    vector<int>&p = point[idx];
    int id = 0;
    for (int i = 0; i < D; ++i) {
      if (p[i] > mid[i]) id |= (1 << i);
    }
    mp[id].push_back(idx);
  }
  for (auto pAir : mp) {
    vector<int> ll, rr;
    int id = pAir.first;
    for (int i = 0; i < D; ++i) if ((id >> i) & 1) {
      ll.push_back(mid[i]);
      rr.push_back(r[i]);
    } else {
      ll.push_back(l[i]);
      rr.push_back(mid[i]);
    }
    dfs(pAir.second, ll, rr);
  }
  return;
}

int main() {
  scanf("%d %d", &n, &D);
  vector<int> P;
  for (int i = 1; i <= n; ++i) {
    point[i].resize(D);
    for (int j = 0; j < D; ++j) scanf("%d", &point[i][j]);
    P.push_back(i);
  }
  sort(P.begin(), P.end(), [](int i, int j) {
    return point[i] < point[j];
  });
  vector<int> PP;
  for (int i = 0; i < (int)P.size(); ++i) {
    if (i && point[P[i]] == point[P[i - 1]]) {
      printf("%d %d\n", P[i], P[i - 1]);
    } else PP.push_back(P[i]);
  }
  vector<int> l, r;
  for (int j = 0; j < D; ++j) {
    l.push_back(0);
    r.push_back(1e8);
  }
  dfs(PP, l, r);
  return 0;
}
```

不需要显示建出四分树。在树上我们直接按照 dfs 序挨个输出就可以了。

注意需要先去重，同时把点先排好序。

## Dimension Reduction

挑战：curse of dimensionality。维度越高越稀疏。

### Johnson Lindenstrauss Transform (hw16)

JL 方法保距离。

$f:\mathbb R^n\to \mathbb R^m$，其中 $m=O(\epsilon^{-2}\log n)$​。

算法：生成一个 $n\times m$ 的矩阵，每个元素都 $\sim N(0,1)$。然后就用这个矩阵乘上 $v$，最后除以 $\sqrt m$。

应用：最小二乘法。

不难发现 $w^* = (X^TX)^{-1}X^Ty$。希望用一个 JL 矩阵处理 $X$ 和 $y$，解 $AXw = Ay$。

注意到 $A$ 是 $d\times n$，$X$ 是 $n\times d$（我们取 $m=O(d)$），乘法要 $O(nd^2)$，不好。但是 $X$ 很稀疏，记 $\mathrm{nnz}(X)$ 为 $X$ 非零元的个数，$AX$ 可在 $O(\mathrm{nnz}(X))$ 的时间内计算。

不过这里需要用另一种 $m\times n$ 的 $S$ 矩阵。这个矩阵**每列**只有一个随机元素非零，且 $\in\{-1,1\}$，这里 $m$ 需要 $O(d^2)$。

利用 $S$ 的稀疏性，设 $S_{i,\pi(i)}\ne0$，那么 $X_{ij}$ 只影响 $(\pi(i),j)$。

算出 $\hat X = SX$ 和 $\hat y = Sy$ 后就 $w = (\hat X^T\hat X)^{-1}\hat X^T\hat y$ 计算答案即可。时间 $O(\rm{nnz}(X) + d^4)$。

```cpp
#include <bits/stdc++.h>

using namespace std;

using db = double;

int n, d, m;
db s[60005];
int pi[60005], X0[60005][605];
db X[2005][2005], Y0[60005], Y[2005], XTX[2005][2005], XTXI[2005][2005], tmp[2005][2005], XTY[2005], haty[60005];
db w[2005];
default_random_engine eng(1);


int main() {
  scanf("%d %d", &n, &d);
  m = 1650;
  uniform_int_distribution<> dis(0, m - 1);
  int choices[2] = {1, -1};
  uniform_int_distribution<> dis2(0, 1);
  uniform_real_distribution<> dis3(-1, 1);
  for (int i = 0; i < n; ++i) {
    pi[i] = dis(eng);
    s[i] = choices[dis2(eng)];
  }
  for (int i = 0; i < n; ++i) {
    int l; scanf("%d", &l);
    while (l--) {
      int j, v; scanf("%d %d", &j, &v);
      --j;
      // X[i][j] = v
      X[pi[i]][j] += v * s[i];
      X0[i][j] = v;
      // X[pi[i][j]] += S[pi[i]][i] * X[i][j]
    }
    int tmpy; scanf("%d", &tmpy);
    Y0[i] = tmpy;
    // Y: n * 1
    // S: m * n
    // X: n * d
    // X = SX: m * d
    // SY: m * 1
    // X^TX: d * d
    // (X^TX)^{-1}X^TSY: d*d * d*m * m*1 = d * 1
    Y[pi[i]] += s[i] * tmpy;
  for (int k = 0; k < m; ++k) {
    for (int i = 0; i < d; ++i) {
      for (int j = 0; j < d; ++j) {
        XTX[i][j] += X[k][i] * X[k][j];
      }
    }
  }
  for (int i = 0; i < d; ++i) XTX[i][i + d] = 1;
  // gauss
  for (int i = 0; i < d; ++i) {
    for (int j = i; j < d; ++j) {
      if (abs(XTX[j][i]) > 1e-8) {
        swap(XTX[i], XTX[j]);
        break;
      }
    }
    for (int j = 0; j < d; ++j) {
      if (j == i) continue;
      db r = XTX[j][i] / XTX[i][i];
      for (int k = 0; k < 2 * d; ++k) {
        XTX[j][k] -= r * XTX[i][k];
      }
    }
  }
  for (int i = 0; i < d; ++i) {
    for (int j = 0; j < d; ++j) {
      XTXI[i][j] = XTX[i][d + j] / XTX[i][i];
    }
  }
  for (int i = 0; i < d; ++i) {
    for (int j = 0; j < m; ++j) {
      XTY[i] += X[j][i] * Y[j];
    }
  }
  for (int i = 0; i < d; ++i) {
    for (int j = 0; j < d; ++j) {
      w[i] += XTXI[i][j] * XTY[j];
    }
    printf("%lf ", w[i]);
  }
  return 0;
}
```

### Principal Component Analysis (hw17) (1/3)

PCA 就是降维找 basis。

近似求 top principal component 的方法：Power Iteration。

椭球的最长轴。$A = X^TX = QDQ^T$。

任取一个向量 $u$，只要其不与 $Qe_1$ 垂直，那么反复作用 $A$ 后其必然在 $Qe_1$ 方向上反复拉伸。

算法流程：

- $A=X^TX$
- 选一个随机单位向量（每一维高斯然后归一化）
- for i：
  - $u_i = A^i u_0$
  - 若 $u_i / ||u_i||\approx u_{i-1} / ||u_{i-1}||$，停止并返回 $u_i/||u_i||$，这个判断可以用 L2-loss

一般而言 $X$ 很稀疏，算时候先算 $Xu_{i-1}$，然后用 $X^T$ 乘他，从右往左算，复杂度 $O(\rm{nnz}(X))$

```cpp
#include <bits/stdc++.h>

using namespace std;
using db = double;

const int maxd = 1005, maxn = 1e5 + 5;
int n, d, m;
struct Pos {
  int x, y;
  db w;
} X[maxn];
db avg[maxd];

random_device rd;
default_random_engine eng(rd());
normal_distribution<db> dis(0, 1);

vector<db> normalize(vector<db> u) {
  db norm = 0;
  for (int i = 0; i < u.size(); ++i) {
    norm += u[i] * u[i];
  }
  norm = sqrt(norm);
  for (int i = 0; i < u.size(); ++i) {
    u[i] /= norm;
  }
  return u;
}

int main() {
  scanf("%d %d %d", &n, &d, &m);
  for (int i = 1; i <= m; ++i) {
    scanf("%d %d %lf", &X[i].x, &X[i].y, &X[i].w);
    --X[i].x, --X[i].y;
  }
  vector<db> u0;
  for (int i = 1; i <= d; ++i) {
    u0.push_back(dis(eng));
  }
  u0 = normalize(u0);
  while (true) {
    vector<db> tmp(n, 0), u1(d, 0);
    // u1 = X^TX u0
    // 先算 X u0
    for (int i = 1; i <= m; ++i) {
      tmp[X[i].x] += X[i].w * u0[X[i].y];
    }
    // tmp[i] += X[i][k] + u0[k]
    // u1 = X^T X u0
    for (int i = 1; i <= m; ++i) {
      u1[X[i].y] += X[i].w * tmp[X[i].x];
    }
    u1 = normalize(u1);
    db diff = 0;
    for (int i = 0; i < d; ++i) {
      diff += (u1[i] - u0[i]) * (u1[i] - u0[i]);
    }
    diff = sqrt(diff);
    if (diff < 1e-6) break;
    u0 = u1;
  }
  for (int i = 0; i < d; ++i) {
    printf("%lf\n", u0[i]);
  }
  return 0;
}
```

