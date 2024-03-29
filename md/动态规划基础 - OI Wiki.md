> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [oi-wiki.org](https://oi-wiki.org/dp/basic/)

> 卷死他们

[](https://oi-wiki.org/edit-landing/?ref=/dp/basic.md "编辑此页")

本页面主要介绍了动态规划的基本思想，以及动态规划中状态及状态转移方程的设计思路，帮助各位初学者对动态规划有一个初步的了解。

本部分的其他页面，将介绍各种类型问题中动态规划模型的建立方法，以及一些动态规划的优化技巧。

引入[](#引入 "Permanent link")
--------------------------

[[IOI1994] 数字三角形](https://www.luogu.com.cn/problem/P1216)

给定一个 行的数字三角形（），需要找到一条从最高点到底部任意处结束的路径，使路径经过数字的和最大。每一步可以走到当前点左下方的点或右下方的点。

<table><tbody><tr><td></td><td><pre>        7 
      3   8 
    8   1   0 
  2   7   4   4 
4   5   2   6   5 

</pre></td></tr></tbody></table>

在上面这个例子中，最优路径是 。

最简单粗暴的思路是尝试所有的路径。因为路径条数是 级别的，这样的做法无法接受。

注意到这样一个事实，一条最优的路径，它的每一步决策都是最优的。

以例题里提到的最优路径为例，只考虑前四步 ，不存在一条从最顶端到 行第 个数的权值更大的路径。

而对于每一个点，它的下一步决策只有两种：往左下角或者往右下角（如果存在）。因此只需要记录当前点的最大权值，用这个最大权值执行下一步决策，来更新后续点的最大权值。

这样做还有一个好处：我们成功缩小了问题的规模，将一个问题分成了多个规模更小的问题。要想得到从顶端到第 行的最优方案，只需要知道从顶端到第 行的最优方案的信息就可以了。

这时候还存在一个问题：子问题间重叠的部分会有很多，同一个子问题可能会被重复访问多次，效率还是不高。解决这个问题的方法是把每个子问题的解存储下来，通过记忆化的方式限制访问顺序，确保每个子问题只被访问一次。

上面就是动态规划的一些基本思路。下面将会更系统地介绍动态规划的思想。

动态规划原理[](#动态规划原理 "Permanent link")
----------------------------------

能用动态规划解决的问题，需要满足三个条件：最优子结构，无后效性和子问题重叠。

### 最优子结构[](#最优子结构 "Permanent link")

具有最优子结构也可能注意是也可能！！！ 是适合用贪心的方法求解。

注意要确保我们考察了最优解中用到的所有子问题。

1.  证明问题最优解的第一个组成部分是做出一个选择；
2.  对于一个给定问题，在其可能的第一步选择中，假定你已经知道哪种选择才会得到最优解。你现在并不关心这种选择具体是如何得到的，只是假定已经知道了这种选择；
3.  给定可获得的最优解的选择后，确定这次选择会产生哪些子问题，以及如何最好地刻画子问题空间；
4.  证明作为构成原问题最优解的组成部分，每个子问题的解就是它本身的最优解。方法是反证法，考虑加入某个子问题的解不是其自身的最优解，那么就可以从原问题的解中用该子问题的最优解替换掉当前的非最优解，从而得到原问题的一个更优的解，从而与原问题最优解的假设矛盾。

要保持子问题空间尽量简单，只在必要时扩展。

最优子结构的不同体现在两个方面：

1.  原问题的最优解中涉及多少个子问题；
2.  确定最优解使用哪些子问题时，需要考察多少种选择。

子问题图中每个定点对应一个子问题，而需要考察的选择对应关联至子问题顶点的边。

### 无后效性[](#无后效性 "Permanent link")

已经求解的子问题，不会再受到后续决策的影响。

### 子问题重叠[](#子问题重叠 "Permanent link")

如果有大量的重叠子问题，我们可以用空间将这些子问题的解存储下来，避免重复求解相同的子问题，从而提升效率。

### 基本思路[](#基本思路 "Permanent link")

对于一个能用动态规划解决的问题，一般采用如下思路解决：

1.  将原问题划分为若干 **阶段**，每个阶段对应若干个子问题，提取这些子问题的特征（称之为 **状态**）；。。。
2.  寻找每一个状态的可能 **决策**，或者说是各状态间的相互转移方式（用数学的语言描述就是 **状态转移方程**）。
3.  按顺序求解每一个阶段的问题。

如果用图论的思想理解，我们建立一个 [有向无环图](https://oi-wiki.org/graph/dag/)，每个状态对应图上一个节点，决策对应节点间的连边。这样问题就转变为了一个在 DAG 上寻找最长（短）路的问题。

最长公共子序列[](#最长公共子序列 "Permanent link")
------------------------------------

最长公共子序列问题

给定一个长度为 的序列 和一个 长度为 的序列 （），求出一个最长的序列，使得该序列既是 的子序列，也是 的子序列。

子序列的定义可以参考 [子序列](https://oi-wiki.org/string/basic/)。一个简要的例子：字符串 `abcde` 与字符串 `acde` 的公共子序列有 `a`、`c`、`d`、`e`、`ac`、`ad`、`ae`、`cd`、`ce`、`de`、`ade`、`ace`、`cde`、`acde`，最长公共子序列的长度是 4。

设 表示只考虑 的前 个元素， 的前 个元素时的最长公共子序列的长度，求这时的最长公共子序列的长度就是 **子问题**。 就是我们所说的 **状态**，则 是最终要达到的状态，即为所求结果。

对于每个 ，存在三种决策：如果 ，则可以将它接到公共子序列的末尾；另外两种决策分别是跳过 或者 。状态转移方程如下：

可参考 [SourceForge 的 LCS 交互网页](http://lcs-demo.sourceforge.net/) 来更好地理解 LCS 的实现过程。

该做法的时间复杂度为 。

另外，本题存在 的算法 [1](#fn:ref1)。有兴趣的同学可以自行探索。

```
// C++ Version
int a[MAXN], b[MAXM], f[MAXN][MAXM];

int dp() {
  for (int i = 1; i <= n; i++)
    for (int j = 1; j <= m; j++)
      if (a[i] == b[j])
        f[i][j] = f[i - 1][j - 1] + 1;
      else
        f[i][j] = std::max(f[i - 1][j], f[i][j - 1]);
  return f[n][m];
}
```

最长不下降子序列[](#最长不下降子序列 "Permanent link")
--------------------------------------

最长不下降子序列问题

给定一个长度为 的序列 （），求出一个最长的 的子序列，满足该子序列的后一个元素不小于前一个元素。

### 算法一[](#算法一 "Permanent link")

设 表示以 为结尾的最长不下降子序列的长度，则所求为 。

计算 时，尝试将 接到其他的最长不下降子序列后面，以更新答案。于是可以写出这样的状态转移方程：。

容易发现该算法的时间复杂度为 。

```
// C++ Version
int a[MAXN], d[MAXN];

int dp() {
  d[1] = 1;
  int ans = 1;
  for (int i = 2; i <= n; i++) {
    for (int j = 1; j < i; j++)
      if (a[j] <= a[i]) {
        d[i] = max(d[i], d[j] + 1);
        ans = max(ans, d[i]);
      }
  }
  return ans;
}
```

```
# Python Version
a = [0] * MAXN
d = [0] * MAXN
def dp():
    d[1] = 1
    ans = 1
    for i in range(2, n + 1):
        for j in range(1, i):
            if a[j] <= a[i]:
                d[i] = max(d[i], d[j] + 1)
                ans = max(ans, d[i])
    return ans
```

### 算法二 [2](#fn:ref2)[](#算法二2 "Permanent link")

当 的范围扩大到 时，第一种做法就不够快了，下面给出了一个 的做法。

首先，定义 为原始序列， 为当前的不下降子序列， 为子序列的长度，那么 就是长度为 的不下降子序列末尾元素。

初始化：。

现在我们已知最长的不下降子序列长度为 1，那么我们让 从 2 到 循环，依次求出前 个元素的最长不下降子序列的长度，循环的时候我们只需要维护好 这个数组还有 就可以了。**关键在于如何维护。**

考虑进来一个元素 ：

1.  元素大于等于 ，直接将该元素插入到 序列的末尾。
2.  元素小于 ，找到 **第一个** 大于它的元素，插入进去，丢弃在它之后的全部元素。

参考代码如下：

<table><tbody><tr><td></td><td><pre>// C++ Version
for (int i = 0; i &lt; n; ++i) scanf("%d", a + i);
memset(dp, 0x1f, sizeof dp);
mx = dp[0];
for (int i = 0; i &lt; n; ++i) {
  *std::upper_bound(dp, dp + n, a[i]) = a[i];
}
ans = 0;
while (dp[ans] != mx) ++ans;

</pre></td></tr></tbody></table><table><tbody><tr><td></td><td><pre># Python Version
dp = [0x1f1f1f1f] * MAXN
mx = dp[0]
for i in range(0, n):
    bisect.insort_left(dp, a[i], 0, len(dp))
ans = 0
while dp[ans] != mx:
    ans += 1

</pre></td></tr></tbody></table>

参考资料与注释[](#参考资料与注释 "Permanent link")
------------------------------------

* * *

> _build_本页面最近更新：2022/8/7 15:23:18，[更新历史](https://github.com/OI-wiki/OI-wiki/commits/master/docs/dp/basic.md)  
> _edit_发现错误？想一起完善？ [在 GitHub 上编辑此页！](https://oi-wiki.org/edit-landing/?ref=/dp/basic.md "编辑此页")  
> _people_本页面贡献者：[Chrogeek](https://github.com/Chrogeek), [ChungZH](https://github.com/ChungZH), [CBW2007](https://github.com/CBW2007), [greyqz](https://github.com/greyqz), [HeRaNO](https://github.com/HeRaNO), [hsfzLZH1](https://github.com/hsfzLZH1), [Ir1d](https://github.com/Ir1d), [Marcythm](https://github.com/Marcythm), [ouuan](https://github.com/ouuan), [partychicken](https://github.com/partychicken), [Persdre](https://github.com/Persdre), [StudyingFather](https://github.com/StudyingFather), [tptpp](https://github.com/tptpp), [Xeonacid](https://github.com/Xeonacid), [xyf007](https://github.com/xyf007), [zhb2000](https://github.com/zhb2000), [Enter-tainer](https://github.com/Enter-tainer), [iamtwz](https://github.com/iamtwz), [InFaNg](https://github.com/InFaNg), [ksyx](https://github.com/ksyx), [LincolnYe](https://github.com/LincolnYe), [shuzhouliu](https://github.com/shuzhouliu), [TrisolarisHD](mailto:orzcyand1317@gmail.com), [WAAutoMaton](https://github.com/WAAutoMaton), [wilfred-chan](https://github.com/wilfred-chan), [xhn16729](mailto:xiong_haonan@163.com), [xhn16729](https://github.com/xhn16729), [yusancky](https://github.com/yusancky)  
> _copyright_本页面的全部内容在 **[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 和 [SATA](https://github.com/zTrix/sata-license)** 协议之条款下提供，附加条款亦可能应用