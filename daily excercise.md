## 5月28日 统计作战单位数

--------

 n 名士兵站成一排。每个士兵都有一个 独一无二 的评分 rating 。

每 3 个士兵可以组成一个作战单位，分组规则如下：


	从队伍中选出下标分别为 i、j、k 的 3 名士兵，他们的评分分别为 rating[i]、rating[j]、rating[k]
	作战单位需满足： rating[i] < rating[j] < rating[k] 或者 rating[i] > rating[j] > rating[k] ，其中  0 <= i < j < k < n


请你返回按上述条件可以组建的作战单位数量。每个士兵都可以是多个作战单位的一部分。

 

示例 1：

输入：rating = [2,5,3,4,1]
输出：3
解释：我们可以组建三个作战单位 (2,3,4)、(5,4,1)、(5,3,1) 。


示例 2：

输入：rating = [2,1,3]
输出：0
解释：根据题目条件，我们无法组建作战单位。

难度：==中等==

代码演示：

```[c]
int numTeams(int* rating, int ratingSize)
{
    int total = 0;
    for (int i = 0; i < ratingSize; i++) 
    {
        int first = rating[i];
        for (int j = i+1; j < ratingSize; j++)
         {
            if (first < rating[j])
             {
                int second = rating[j];
                for (int k = j+1; k < ratingSize; k++)
                {
                    if (second < rating[k]) total++;
                }
            }
            if (first > rating[j]) 
            {
                int second = rating[j];
                for (int k = j+1; k < ratingSize; k++)
                {
                    if (second > rating[k])
                        total++;
                }
            }
        }
    }
    return total;
}
```



执行结果:通过

执行用时 :24 ms, 在所有 C 提交中击败了78.27% 的用户

内存消耗 :5.5 MB, 在所有 C 提交中击败了100.00%的用户

- **这种暴力解法利用三个循环效能相对较低但是思路容易想**



代码演示：

```[c]
int numTeams(int* rating, int ratingSize){
    int total = 0;
    for (int j = 1; j < ratingSize - 1; j++) {
        int second = rating[j];
        int i_min = 0;
        int i_max = 0;
        for (int i = 0; i < j; i++){
            if (rating[i] < second)
                i_min++;
            if (rating[i] > second)
                i_max++;
        }

        int k_min = 0;
        int k_max = 0;
        for (int k = j+1; k < ratingSize; k++){
            if (rating[k] < second)
                k_min++;
            if (rating[k] > second)
                k_max++;
        }
        total += i_min * k_max + i_max * k_min;
    }
    return total;
}

```

执行结果：通过

执行用时 :0 ms, 在所有 C 提交中击败了100.00% 的用户

内存消耗 :5.5 MB, 在所有 C 提交中击败了100.00%的用户

- **这种方法利用最中间的元素进行遍历只需要两次循环效率高很多**

- **颇有一点二分查找的意思**

## 5月29日 查询带键的排列

--------

给你一个待查数组 `queries` ，数组中的元素为 `1`到 `m` 之间的正整数。 请你根据以下规则处理所有待查项 `queries[i]`（从 i=0 到 i=queries.length-1）：


	一开始，排列 P=[1,2,3,...,m]。
	对于当前的 i ，请你找出待查项 queries[i] 在排列 P 中的位置（下标从 0 开始），然后将其从原位置移动到排列 P 的起始位置（即下标为 0 处）。注意， queries[i] 在 P 中的位置就是 queries[i] 的查询结果。


请你以数组形式返回待查数组 ` queries `的查询结果。

 

示例 1：

输入：`queries = [3,1,2,1], m = 5`
输出：`[2,1,2,1] `
解释：`待查数组 queries 处理如下：`
`对于 i=0: queries[i]=3, P=[1,2,3,4,5], 3 在 P 中的位置是 2，接着我们把 3 移动到 P 的起始位置，得到 P=[3,1,2,4,5] 。
对于 i=1: queries[i]=1, P=[3,1,2,4,5], 1 在 P 中的位置是 1，接着我们把 1 移动到 P 的起始位置，得到 P=[1,3,2,4,5] 。 
对于 i=2: queries[i]=2, P=[1,3,2,4,5], 2 在 P 中的位置是 2，接着我们把 2 移动到 P 的起始位置，得到 P=[2,1,3,4,5] 。
对于 i=3: queries[i]=1, P=[2,1,3,4,5], 1 在 P 中的位置是 1，接着我们把 1 移动到 P 的起始位置，得到 P=[1,2,3,4,5] 。 `
因此，返回的结果数组为 [2,1,2,1] 。  


示例 2：

输入：`queries = [4,1,2,2], m = 4`
输出：`[3,1,2,0]`


示例 3：

输入：`queries = [7,5,5,8,3], m = 8`
输出：`[6,5,0,7,5]`

难度：==中等==

代码演示：

```[c]
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* processQueries(int* queries, int queriesSize, int m, int* returnSize)
{
    int* ret = (int*)calloc(queriesSize, sizeof(int));
    int* ans = (int*)calloc(m, sizeof(int));
    
    ret[0] = queries[0] - 1;
    
    for (int i = 0; i < m; i++) 
    {
        ans[i] = i + 1;
    }
    
    for (int i = 1; i < queriesSize; i++) 
    {
        int temp = ans[ret[i - 1]];
        for (int j = ret[i - 1]; j >= 1; j--) 
        {
            ans[j] = ans[j - 1];
        }
        ans[0] = temp;
        
        for (int index = 0; index < m; index++) 
        { 
            if (queries[i] == ans[index]) 
            {
                ret[i] = index;
                break;
            }
        }
    }   
    *returnSize = queriesSize;
    return ret;
}
```

执行结果：通过

执行用时 :8 ms, 在所有 C 提交中击败了98.28% 的用户

内存消耗 :5.8 MB, 在所有 C 提交中击败了100.00%的用户



- **暴力法直接解题效果还不错**



## 5月30日 反转数组

--------

给你一幅由` N × N `矩阵表示的图像，其中每个像素的大小为 `4 `字节。请你设计一种算法，将图像旋转 90 度。

不占用额外内存空间能否做到？

 

示例 1:

给定 matrix = 
`[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],`

原地旋转输入矩阵，使其变为:
`[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]`


示例 2:

给定 matrix =
`[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], `

原地旋转输入矩阵，使其变为:
`[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]`

难度：==中等==

代码演示：

```[c]
void rotate(int** matrix, int matrixSize, int* matrixColSize)
{
    int i,j;
    int len = * matrixColSize;
    int temp[len][len];
    for(i=0;i<len;i++)
    {
        for(j=0;j<len;j++)
        {
            temp[j][len-1-i] = matrix[i][j];
        }
    }
    for(i=0;i<len;i++)
    {
        for(j=0;j<len;j++)
        {
            matrix[i][j]=temp[i][j];
        }
    }
}

```



- **这道题最重要的便是找到反转前后数组的坐标之间的关系和规律**

附上两张题解中的图：

![](LeetCode习题.assets/旋转数组1.png)





![](LeetCode习题.assets/旋转数组2.png)



红色轴为反转后的的坐标轴，寻找规律一目了然

## 5月31日 除数博弈

---------

爱丽丝和鲍勃一起玩游戏，他们轮流行动。爱丽丝先手开局。

最初，黑板上有一个数字 N 。在每个玩家的回合，玩家需要执行以下操作：


	选出任一 x，满足 0 < x < N 且 N % x == 0 。
	用 N - x 替换黑板上的数字 N 。


如果玩家无法执行这些操作，就会输掉游戏。

只有在爱丽丝在游戏中取得胜利时才返回 True，否则返回 false。假设两个玩家都以最佳状态参与游戏。




示例 1：

输入：2
输出：true
解释：爱丽丝选择 1，鲍勃无法进行操作。


示例 2：

输入：3
输出：false
解释：爱丽丝选择 1，鲍勃也选择 1，然后爱丽丝无法进行操作。



难度：==简单==

代码演示：

```[c]
bool divisorGame(int N)
{
    if(N==1)
        return false;
    int flag=0;
    int i;
    while(1)
    {
        for(i=1;i<N;i++)
        {
            if(N%i==0)
            {
                N=N-i;    
                break;
            }
        }
        if(i==N)
        {
            if(flag==0)
                return true;
            else
                return false;
        }
        flag=(flag+1)%2;
    }
}
```

执行结果：通过

执行用时 :0 ms, 在所有 C 提交中击败了100.00% 的用户

内存消耗 :5.1 MB, 在所有 C 提交中击败了100.00%的用户


- **这是最初的想法也就是如果拿到的数是质数那么就输了**

代码演示：

```[c]
bool divisorGame(int N)
{
    bool b[1001];
    b[1]=false;
    b[2]=true;
    for(int i=3;i<=N;i++)
    {
        b[i]=false;
        for(int j=1;j<i;j++) 
        {     
            if((b[i-j]==false)&&(i%j==0))
            {
                b[i]=true;
                break;
            }
        }
    }             
    return b[N];            
}
```

执行结果：通过

执行用时 :4 ms, 在所有 C 提交中击败了49.69% 的用户

内存消耗 :5.1 MB, 在所有 C 提交中击败了100.00%的用户

- **这是题解中利用动态规划解决问题的思路，十分巧妙**
- **动态规划中的思想内核就是利用局部的最优解来找到全局最优解**



## 6月1日 移动0

---------

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

示例:

输入: [0,1,0,3,12]
输出: [1,3,12,0,0]

说明:


	必须在原数组上操作，不能拷贝额外的数组。
	尽量减少操作次数。

难度：==简单==

代码演示：

```[c]
void moveZeroes(int* nums, int numsSize)
{
    if ((nums == NULL) || (numsSize <= 0)) 
    {
        return ;
    }
    int index=0;
    for(int i=0; i<numsSize; i++) 
    {
        if (nums[i]!=0) 
        {
            nums[index]=nums[i];
            index++;
        } 
    }
    while(index<numsSize) 
    {
        nums[index]=0;
        index++;
    }
}
```



- **这道题虽然类型是双指针，但是明显大材小用**
- **这种解法依然是原地算法但是更加直接**



## 6月2日 交换数字

--------

编写一个函数，不用临时变量，直接交换numbers = [a, b]中a与b的值。
示例：
输入: numbers = [1,2]
输出: [2,1]

提示：

numbers.length == 2

难度：==中等==

代码演示：

 ```[c]
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */
int* swapNumbers(int* numbers, int numbersSize, int* returnSize)
{
    numbers[0] ^= numbers[1];
    numbers[1] ^= numbers[0];
    numbers[0] ^= numbers[1];
    *returnSize = numbersSize;
    return numbers;
}
 ```



执行结果：通过

执行用时 :0 ms, 在所有 C 提交中击败了100.00% 的用户

内存消耗 :5.1 MB, 在所有 C 提交中击败了100.00%的用户

- **这道题主演考察了异或的妙用**

- 以3和4为例，如图所示：

![](LeetCode习题.assets/异或运算.png)

## 6月3日 斐波那契数

--------

斐波那契数，通常用 F(n) 表示，形成的序列称为斐波那契数列。该数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：

F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.


给定 N，计算 F(N)。

 

示例 1：

输入：2
输出：1
解释：F(2) = F(1) + F(0) = 1 + 0 = 1.


示例 2：

输入：3
输出：2
解释：F(3) = F(2) + F(1) = 1 + 1 = 2.


示例 3：

输入：4
输出：3
解释：F(4) = F(3) + F(2) = 2 + 1 = 3.


提示：


	0 ≤ N ≤ 30

难度：==简单==

代码演示：

```[c]
int fib(int N)
{
    if(N==0)
    {
        return 0;
    }
    if(N==1||N==2)
    {
        return 1;
    }
    else 
    {
        return fib(N-1)+fib(N-2);
    }
   
}
```

执行结果：通过

执行用时 :8 ms, 在所有 C 提交中击败了44.67% 的用户

内存消耗 :5.1 MB, 在所有 C 提交中击败了100.00%的用户



- **重温这道题目的主要原因是它和动态规划的题目有许多相同之处**

- **递归方法效率不高的主要原因是它进行了许多重复的运算，时间复杂度为O(2^n)**

代码演示：

```[c]
int fib(int N)
{
    int i,j,k;
    k=j=1;
    if(N==0)
    {
        return 0;
    }
    if(N==1||N==2)
    {
        return 1;
    }
    else 
    {
        for(i=3;i<=N;i++)
        {
            int sum=j+k;
            j=k;
            k=sum;

        }
    }
    return k;
}
```



执行结果：通过

执行用时 :0 ms, 在所有 C 提交中击败了100.00% 的用户

内存消耗 :5.1 MB, 在所有 C 提交中击败了100.00%的用户

- **这种解法就是有了一点备忘录或者dp数组的雏形**



## 6月4日 硬币兑换

--------

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

 

示例 1:

输入: coins = [1, 2, 5], amount = 11
输出: 3 
解释: 11 = 5 + 5 + 1

示例 2:

输入: coins = [2], amount = 3
输出: -1

 

说明:
你可以认为每种硬币的数量是无限的。

难度：==中等==

代码演示：

```[c]
int min(int a,int b)
{
    if(a>b) return b;
    else return a;
}
int coinChange(int* coins, int coinsSize, int amount)
{
	int *dp = (int *)malloc(sizeof(int) * (amount + 1));
	for (int i=0; i<amount+1; i ++) {
        dp[i]=amount + 1;
    }
	dp[0]=0;
	for (int i=1; i<=amount; i++) {
		for (int j=0;j<coinsSize;j++) {
			if (i>=coins[j]) {
				dp[i]=min(dp[i-coins[j]]+1, dp[i]);
			}
		}
	}
    int ret=dp[amount]>amount?-1:dp[amount];
    free(dp);
	return ret;
}

```

执行结果：通过

执行用时 :36 ms, 在所有 C 提交中击败了74.70% 的用户

内存消耗 :9.5 MB, 在所有 C 提交中击败了80.00%的用户

- **初学动态规划对这个dp数组的原理理解尚可，但是在代码实现上总会出现一些问题**



## 6月5日 6和9组成的最大数字

--------

给你一个仅由数字 6 和 9 组成的正整数 num。

你最多只能翻转一位数字，将 6 变成 9，或者把 9 变成 6 。

请返回你可以得到的最大数字。

 

示例 1：

输入：num = 9669
输出：9969
解释：
改变第一位数字可以得到 6669 。
改变第二位数字可以得到 9969 。
改变第三位数字可以得到 9699 。
改变第四位数字可以得到 9666 。
其中最大的数字是 9969 。


示例 2：

输入：num = 9996
输出：9999
解释：将最后一位从 6 变到 9，其结果 9999 是最大的数。

难度：==简单==

代码演示：

```[c]
#include <Math.h>
int maximum69Number (int num){
    int count=0,th=0;        
    int re=num;
    while(re){
        count++;
        if(re%10==6)
           th=count;
        re/=10;
    }
    return num+3*pow(10,th-1);
}
```



执行结果：通过

执行用时 :0 ms, 在所有 C 提交中击败了100.00% 的用户

内存消耗 :5.4 MB, 在所有 C 提交中击败了100.00%的用户



- **这几天忙于课程设计只能做一些简单题了**

- **这道题的这种解法还是十分巧妙的，一次循环解决了所有问题，比我一开始的利用数组的解法方便了很多**

## 6月6日 买入股票的最佳时机

--------

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。

注意：你不能在买入股票前卖出股票。

 

示例 1:

输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。


示例 2:

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

难度：==简单==
代码演示：

```[c]
int max(int a,int b)
{
    if(a>b) return a;
    else return b;
}
int maxProfit(int* prices, int pricesSize)
{
    
    if(prices==NULL||pricesSize==0)
    {
        return NULL;
    }
    int dp[pricesSize];
    int i,j;
    for(i=0;i<pricesSize;i++)
    {
        dp[i]=0;
    }
    for(i=0;i<pricesSize;i++)
    {
        for(j=0;j<i;j++)
        {
            if(prices[i]>prices[j])
            {
                dp[i]=max(dp[i],prices[i]-prices[j]);
            }
        }
    }
    int temp=0;
    for(i=0;i<pricesSize;i++)
    {
        temp=max(dp[i],temp);
    }
    return temp;
}
```

执行结果：通过

执行用时 :1204 ms, 在所有 C 提交中击败了14.50% 的用户

内存消耗 :6.6 MB, 在所有 C 提交中击败了100.00%的用户

- **完全照猫画虎完成的一道简单题**
- **利用dp数组的解法在简单题中可能也不太适用**



## 6月7日 最长递增子序列

----------

给定一个无序的整数数组，找到其中最长上升子序列的长度。

示例:

输入: [10,9,2,5,3,7,101,18]
输出: 4 
解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。

说明:


	可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。
	你算法的时间复杂度应该为 O(n2) 。


进阶: 你能将算法的时间复杂度降低到 O(n log n) 吗?

难度：==中等==

代码演示：

```[c]
int max(int a,int b)
{
    if(a>b) return a;
    else return b;
}
int lengthOfLIS(int* nums, int numsSize)
{
    int *dp=(int *)malloc(sizeof(int)*(numsSize+1));
    int i,j,k,temp;
    i=j=k=temp=0;
    for(i=0;i<numsSize;i++)
    {
        dp[i]=1;
    }
    for(j=0;j<numsSize;j++)
    {
        for(k=0;k<j;k++)
        {
            if(nums[j]>nums[k])
            {
                dp[j]=max(dp[j],dp[k]+1);
            }
        }
    }
    for(i=0;i<numsSize;i++)
    {
        temp=max(temp,dp[i]);
    }
    return temp;
}
```

执行结果：通过

执行用时 :36 ms, 在所有 C 提交中击败了67.66% 的用户

内存消耗 :5.7 MB, 在所有 C 提交中击败了100.00%的用户

- **这道题也算是动态规划的进阶，主要的问题在于——利用dp数字来存储什么**




