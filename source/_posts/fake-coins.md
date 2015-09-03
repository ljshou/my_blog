title: fake coins
date: 2015-02-10 09:00:21
categories: 算法
tags: 分治
---
一道有趣的称硬币的问题
<!--more-->
###DESCRIPTION：
有2n个硬币和一个天平，其中有一个质量是m+1, 另一个硬币质量为m-1, 其余的硬币质量都是m。
要求：O(lgn)时间找出两枚假币  
注意： n不一定是2的幂次方  

###ANALYSIS：
####算法1：O(n)算法
将2n个硬币分成n组（每组2个）进行称量：
结果只有两种：
+ 仅有一组出现天平不平衡： 导致天平不平衡的两个硬币一定就是假币
+ 出现两组天平不平衡： 这四个硬币中必定存在两个假币。将重的硬币称量，轻的两个硬币称量得到结果。

####算法2： O（lgn)算法 分治  
首先**假设n是2的幂次方**（如果不是，则可以加入新的真币，使得硬币数目是2的幂次方）

第一步： 对所有的硬币编号， 从0到2n-1

第二步： 由于2n是2的幂次方，假设2n的二进制表示有lgn位
```
for i=0, lgn;
    根据二进制表示第i位是否为0,或1
    将 2n个硬币分成两个数目为n的集合，放到天平的两边；
    if 天平不平衡， break;
end for;
```
注意： 由于两个假币的编号不同，所以它们的二进制表示必定某一位不同，不妨设为第k位

第三步： 我们得到了两堆数目都是n的硬币集合, 注意此时两个假币已经分开
               用O(lgn)的分治算法在两堆硬币中分别求出假币

如果n不是2的幂次方，注入真币，使得数目为2的幂次方。  
下面证明注入的真币数目一定不超过2n  
> 假设 2^k < 2n < 2^(k+1)
加入的真币数目= 2^(k+1) - 2n < 2^(k+1) - 2^k = 2^k < 2n
结论， 加入硬币后，总硬币数不超过4n，所以复杂度仍旧是O(lgn)

####算法3： 并不需要通过增加硬币， 也能O（lgn）找出假币
n不是2的幂次方的问题在于： 根据2进制数某一位1，0分成2堆硬币，可能出现数目不等的情况。  
第1位： 该二进制位为1的堆 和 为0的堆 数目至多相差1（编号是奇数和偶数的硬币数至多相差1个）  
做法： 从多的那堆中 丢掉 编号最大的硬币!!!
1. 因为丢了以后，天平若平衡：则丢掉的一定是真币；反之，则不能确定。   这样最终我们得到3堆硬币，两堆就是天平上不平衡的两堆，第三堆就是算法过程中被我们丢掉的一堆。两枚假币必定在三堆硬币中，而且已经被分开。O(lgn)找出3堆硬币中各自的假币（注意，虽然有一堆没有假币，但是算法还是会得到一个结果，当然它是真币）。然后3枚硬币天平称两次，最重的和最轻的就是假币
2. 为什么丢编号最大的硬币呢？ 其实为了保证下一步时 两堆硬币数同样具有最多相差1的性质

因此，可以像算法2一样，用O(lgn)时间把 两枚假币 分开。。再用O(lgn)找出假币即可。。。