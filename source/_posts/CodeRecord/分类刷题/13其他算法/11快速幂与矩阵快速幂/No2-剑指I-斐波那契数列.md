---
title: '剑指Offer I.斐波那契数列：「状态DP」&「矩阵快速幂」'
reward: true
declare: true
gitalkenable: true
categories: CodeRecord
toc: true
tags:
  - 状态机
  - 动态规划
  -	递归
  -	矩阵快速幂
date: 2022-01-17 20:17:35
top:
---
---

* 剑指Offer I：第N个泰波那契数列
  * 时间：2022-01-17
  * 力扣难度：Easy
  * 个人难度：Easy
  * 数据结构：数组
  * 算法：状态机、动态规划、矩阵快速幂、递归


---

<!-- more -->

# 剑指Offer I.斐波那契数列：「状态DP」&「矩阵快速幂」

## 1. 题目描述

* 题目：[原题链接](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

  * 写一个函数，输入 `n` ，求斐波那契（Fibonacci）数列的第 `n` 项（即 `F(N)`）
  * 斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。
  * 答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1
  * `0 <= n <= 100`
* 输入输出规范：
  * 输入：整数 n 
  * 输出：第 n 个斐波那契数
* 输入输出示例
  * 输入：n = 5
  * 输出：5

---

## 2. 方法一：递归 & 记忆化

* 思路

  * 本题存在很明显的递推关系，最简单的思路就是直接根据公式递归，递归的终止条件也非常明显，就是 n <= 2 的时候
  * 但是递归会不断的调用方法，n 特别大的时候，一方面时间复杂度较高，另一方面方法会占用大量栈空间，在空间上面也有隐患
  * 实际上，递归的过程中，会出现大量的重复计算，可以通过**记忆化**的方式来进行优化，避免重复计算
  
* 题解：直接递归

  > ```java
  > public int fib(int n) {
  >     if(n <= 1) return n;
  >     int mod = (int)1e9+7;
  >     int res = fib(n-1) + fib(n-2);
  >     res %= mod;
  >     return res;
  > }
  > ```
  
* 题解：递归 & 记忆化

  > ```java
  > private int[] memo = new int[101];
  > public int fib(int n) {
  >     int mod = (int)1e9+7;
  >     if(n <= 1) return n;
  >     if(memo[n] != 0) return memo[n];
  >     memo[n] = fib(n-1) + fib(n-2);
  >     memo[n] %= mod;
  >     return memo[n];
  > }
  > ```

* 复杂度分析(优化后)：n 是输入的整数

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(n)$

---

## 3. 方法二：动态规划

* 思路

  * 对于这种具有递推关系的题目，动态规划同样是一种常规方法，相当于将递归转换为迭代的方式
  * 对于本题，因为当前状态只和其前面三个状态相关，所以可以将DP数组优化为两个个普通的变量，降低空间复杂度
  
* 题解：直接DP

  > ```java
  > public int fib(int n) {
  >        if(n <= 1) return n;
  >        int mod = (int)1e9+7;
  >        int[] dp = new int[n+1];
  >        dp[0] = 0;
  >        dp[1] = 1;
  >        for(int i = 2; i < n+1; i++) {
  >            dp[i] = dp[i-1]+dp[i-2];
  >            dp[i] %= mod;
  >        }
  >        return dp[n];
  >    }
  >    ```
  
* 题解：空间优化

  > ```java
  > public int fib(int n) {
  >        if(n <= 1) return n;
  >        int mod = (int)1e9+7;
  >        int a = 0, b = 1;
  >        int res = 0;
  >        for(int i = 2; i < n+1; i++) {
  >            res = (a + b) % mod;
  >            a = b;
  >            b = res;
  >        }
  >        return res;
  >    }
  > ```
  
* 复杂度分析(优化后)：n 是输入的整数

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(1)$

---

## 4. 方法三：矩阵快速幂

* 思路：矩阵乘法 & 快速幂

  * 对于状态机DP问题而言，如果**状态转移**的过程可以表示为**线性的叠加**，就可以转换为**矩阵计算**来提高效率
  * 为了求出`dp[n]`，状态DP一般都需要遍历次，即**线性**的时间复杂度 $O(n)$，而矩阵计算可以通过矩阵具有**结合律**的性质来将复杂度优化到**对数级** $O(logn)$
  * 这种方法可以正确求解结果的根本原因是**矩阵乘法**，具体推导涉及到**线性代数**的知识，而复杂度为对数级是因为在求解矩阵时使用了**快速幂算法**

* **快速幂**

  * 快速幂是用于求解 x 的 n 次幂问题的一种算法，通过**奇偶次幂**的判断并分类讨论，将线性复杂度**二分**为对数复杂度
  * 具体而言，是在偶次幂时进行矩阵平方并将幂除2，奇次幂时进行矩阵与初始值相乘并赋值给初始值

* **矩阵乘法**

  * 矩阵乘法要求**前一个矩阵的列数**等于**后一个矩阵的行数**
  * 相乘得到的矩阵的行数与前一个矩阵行数相等，列数与后一个矩阵列数相等

* **状态机DP**转化为**矩阵乘法**的过程，以本题为例

  * 对于本题，根据递推公式 $F_{n} = F_{n-1}+F_{n-2}$ 以及矩阵乘法的性质，可以得到
    $$
    \begin{gathered}
    \begin{bmatrix} 
    F(i) 
    \\ F(i - 1) 
    \end{bmatrix}
    \end{gathered}
    =
    \begin{gathered}
    \begin{bmatrix} 
    F(i - 1)*1+F(i - 2)*1 \\
    F(i - 1)*1+F(i - 2)*0 \\
    \end{bmatrix}
    \end{gathered}=
    
    \begin{gathered}
    \begin{bmatrix} 
    1 & 1 \\ 
    1 & 0  \\ 
    \end{bmatrix}*
    \end{gathered}
    \begin{gathered}
    \begin{bmatrix} 
    F(i - 1) 
    \\ F(i - 2)  
    \end{bmatrix}
    \end{gathered}
    $$
    
  * 其中，我们可以得到一个状态转移的**系数矩阵** M：
    $$
    M=\begin{gathered}
    \begin{bmatrix} 
    1 & 1 \\ 
    1 & 0  \\  
    \end{bmatrix}
    \end{gathered}
    $$
    
  * 将矩阵形式递推到 $T(n)$， 根据矩阵的结合律，可以得到：
  
  $$
  \begin{gathered}
  \begin{bmatrix} 
  F(n) 
  \\ F(n - 1)  
  \end{bmatrix}
  \end{gathered}
  = M*
  \begin{gathered}
  \begin{bmatrix} 
  F(n - 1) 
  \\ F(n - 2)  
  \end{bmatrix}
  \end{gathered}=
  M^{n-1}*
  \begin{gathered}
  \begin{bmatrix} 
  F(0) 
  \\ F(1) 
  \end{bmatrix}
  \end{gathered}=M^{n-1}*
  \begin{gathered}
  \begin{bmatrix} 
  0
  \\ 1 
  \end{bmatrix}
  \end{gathered}
  $$
  
  * 所以就将整个过程化简为**求矩阵 M 的 n-1 次幂问题**，实际上就是矩阵乘法，结合快速幂算法，求出矩阵 M 后，与初始状态做矩阵乘法，得到的结果矩阵的第一个元素就是我们要求的结果
  
* 题解：初始数组使用初始值填充，一维

  > ```java
  > final int MOD = (int)1e9+7;
  >    public int fib(int n) {
  >        if (n <= 1) return n;
  >        long[][] ini = new long[][]{
  >            {1},{0}
  >        };
  >        long[][] matrix = new long[][]{
  >            {1, 1},
  >            {1, 0}
  >        };
  >        int x = n - 1;
  >        while (x > 0) {
  >            if ((x & 1) == 1) {
  >                ini = mul(matrix, ini);
  >            }
  >            matrix = mul(matrix, matrix);
  >            x >>= 1;
  >        }
  >        return (int) (ini[0][0] % MOD);
  >    }
  >    
  >    private long[][] mul(long[][] matrixA, long[][] matrixB) {
  >        int row = matrixA.length;
  >     int col = matrixB[0].length;
  >     long[][] res = new long[row][col];
  >     int size = matrixB.length;
  >        for (int i = 0; i < row; i++) {
  >            for (int j = 0; j < col; j++) {
  >                for (int k = 0; k < size; k++) {
  >                    res[i][j] += matrixA[i][k] * matrixB[k][j];
  >                    res[i][j] %= MOD;
  >                }
  >            }
  >        }
  >        return res;
  >    }
  >    ```

* 复杂度分析：n 是输入的整数

  * 时间复杂度：$O(C^3*logn)$
  * 空间复杂度：$O(C^2)$

