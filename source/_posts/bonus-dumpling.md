title: bonus dumpling
date: 2014-12-28 17:58:51
categories: 算法
tags: DP
---
一道有意思的数学题
<!--more-->
### 题目描述
过年了，妈妈做了100只饺子，其中有10只饺子里面有1块的硬币。
小明依次吃这100只饺子，如果小明连续吃到k个硬币，那么小明得到k-1个硬币。

> e.g.  110111表示6只饺子，1表示有硬币，0表示没有。11表示连续吃到2个饺子，那么小明得1个硬币；111连续迟到3个，小明得2个硬币；故，小明共得到3个硬币。

问小明得到的硬币的期望值是多少？

### 分析
期望定义： $E(X) = \sum_iX_iP(X_i)$

在本题中，随机事件X即为小明最终得到的硬币数目，$x \in [0, 9]$
+ 计算$P(X_i) = \frac{cases(x=i)}{all cases}$
+ 计算期望

那么原问题就简化为小明得到硬币k,所有可能的cases的数目。

采用动态规划，子问题定义如下
+ f[i][j][k]----前i个饺子，含j个有硬币的饺子，得分是k，且第i个饺子不含硬币，所有可能的情况的总数。
+ g[i][j][k]----前i个饺子，含j个有硬币的饺子，得分是k，且第i个饺子含硬币，所有可能的情况的总数。

那么递推公式如下,
+ f[i][j][k]---第i个饺子不包含硬币，所以不用第i个饺子
  + f[i][j][k] = f[i-1][j][k] + g[i-1][j][k]
+ g[i][j][k]---第i个饺子包含硬币，那么这个硬币能够得到，取决于第i-1个饺子是否包含硬币
  + g[i][j][k] = f[i-1][j-1][k] + g[i-1][j-1][k-1]

### 代码
#### 1.DP
```py
#!/usr/bin/env python
import sys

def compute(m,n,f,g):
    if n > m: return -1
    for i in range(m+1):
        f[i][0][0] = 1
    for j in range(n+1):
        f[0][j][0] = 0
        g[0][j][0] = 0
    f[0][0][0] = 1
    g[0][0][0] = 1

    for i in range(1,m+1):
        for j in range(1,min(i,n)+1):
            for k in range(0,j):
                f[i][j][k] = f[i-1][j][k] + g[i-1][j][k]

                if k != 0: g[i][j][k] = f[i-1][j-1][k] + g[i-1][j-1][k-1]
                else: g[i][j][k] = f[i-1][j-1][k]

    cnt = [0 for i in range(n)]
    for k in range(0,n):
        cnt[k] = f[100][10][k] + g[100][10][k]
    print cnt[1:]


    #(100, 10)
    allSum = 1.0
    for i in range(91,101):  allSum = allSum * i
    for i in range(1,11): allSum /= i

    allSum2 = 0
    for i in range(1,n): allSum2 += (i * cnt[i])
    print allSum2/float(allSum)

if __name__ == "__main__":
    m = 100
    n = 10
    k = n-1
    f = [[[0 for k in range(k+1)] for j in range(n+1)] for i in range(m+1)]
    g = [[[0 for k in range(k+1)] for j in range(n+1)] for i in range(m+1)]

    compute(m,n,f,g)
```

#### 2.模拟
可以用计算机模拟下，
```py
#!/usr/bin/python
import random

def helper(vec):
    flag = False
    count = 0
    for i in range(len(vec)):
        if vec[i]:
            if flag: count +=1
            else: flag = True
        else:
            flag = False
    return count

N = 100000
score = 0
cnt = [0 for i in range(100)]
for i in range(N):
    count = 0
    for i in range(len(cnt)): cnt[i] = 0
    while count < 10:
        index = random.randrange(0,100)
        if cnt[index]: continue
        count += 1
        cnt[index] = 1
    score += helper(cnt)
print float(score) / N
```
结果大约是0.9左右。

### 后话
其实，这题的结果在某种程度上有一点违背直觉。期望在0.9左右，也就是说，平均情况下，会有两个连续的1.

其实跟这个题目类似，数学上，有一个有名的悖论：生日悖论。

生日悖论，指如果一个房间里有23个或23个以上的人，那么至少有两个人的生日相同的概率要大于50%。
![](http://ljshou.qiniudn.com/birth.jpg)
