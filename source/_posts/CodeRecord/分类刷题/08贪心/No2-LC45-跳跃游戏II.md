---
title: LeetCode219.存在重复元素II：「哈希表」&「滑动窗口」
reward: true
declare: true
gitalkenable: true
categories: CodeRecord
toc: true
tags:
  - 贪心
  - 动态规划
abbrlink: 9d7d5c9f
date: 2022-01-22 00:22:12
top:
---
---

* LeetCode55：跳跃游戏
  * 时间：2022-01-22
  * 力扣难度：Medium
  * 个人难度：Medium-
  * 数据结构：数组
  * 算法：贪心、动态规划


---

<!-- more -->

# LeetCode55.跳跃游戏：「贪心」&「动态规划」

## 1. 题目描述

* 题目：[原题链接](https://leetcode-cn.com/problems/jump-game/)

  * 给定一个非负整数数组 nums ，你最初位于数组的第一个下标 。
  * 数组中的每个元素代表你在该位置可以跳跃的最大长度。
  * 判断你是否能够到达最后一个下标。
  * `1 <= nums.length <= 3 * 10^4`
  * `0 <= nums[i] <= 10^5`

* 输入输出规范

  * 输入：数组 nums
  * 输出：布尔值

* 输入输出示例

  * 输入：nums = [2,3,1,1,4]
  * 输出：true
  

---

## 2. 方法一：贪心 & 动态规划：正向遍历

* 思路

  * 本题属于贪心的经典问题，首先明确下问题求解的是能否到达最后一个元素
  * 这类问题一般都是通过**动态规划**，再结合**贪心**的思想来解决，而且很多时候动态规划的DP数组的转移只依赖常量空间，所以可以进行空间优化
  * 回到本题，为了判断能否到达最后一个元素，我们可以正向遍历整个数组，然后计算每个可到达的位置加上自身元素后，可以到达的最远位置，如果遍历的过程中或者最终可以到达的最远位置超过了末尾，即可以到达
  * DP数组的含义：`dp[i]`表示第 i 个元素可以到达的最远位置，注意可能该位置无法到达，此时为默认值0
  * 实际上，我们**只需要知道当前遍历到的元素及其之前元素中**，目前可到达的最远位置(局部最优)，而无需知道其他元素的信息，因此可以省略DP数组
  
* 题解

  > ```java
  > public boolean canJumpDP(int[] nums) {
  >     if(nums == null || nums.length == 0 )return false;
  >     int n = nums.length;
  >     if(n == 1) return true;
  >     int[] dp = new int[n];
  >     int maxIndex = 0;
  >     for (int i = 0; i < n; i++) {
  >         if(i <= maxIndex) {
  >             dp[i] = Math.max(dp[i], i + nums[i]);
  >         }
  >         maxIndex = Math.max(maxIndex, dp[i]);
  >         if(maxIndex >= n - 1) return true;
  >     }
  >     return false;
  > }
  > ```

* 复杂度分析：n 是数组的大小

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(n)$
  
* 题解：空间优化

  > ```java
  > public boolean canJump(int[] nums) {
  >     if(nums == null || nums.length == 0 )return false;
  >     int n = nums.length;
  >     if(n == 1) return true;
  >     int maxIndex = 0;
  >     for (int i = 0; i < n; i++) {
  >         if(i <= maxIndex) {
  >             maxIndex = Math.max(maxIndex, i + nums[i]);
  >         }
  >         if(maxIndex >= n - 1) return true;
  >     }
  >     return false;
  > }
  > ```

* 复杂度分析：n 是数组的大小

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(1)$

---

## 3. 方法二：贪心：逆向遍历

* 思路

  * 同样的，我们可以通过倒推，从末尾逆向遍历，并维护一个表示需要到达的元素的索引
  * 遍历过程中，我们判断当前元素能否到达末尾，能到达的话就将维护的索引改变为当前元素的索引，遍历最后如果该索引为起点0，表示可以到达末尾
  * 这是因为假如当前元素可以到达末尾，那么只要从起点可以到达该元素，就可以到达末尾，而且假设如果起点可以到达该元素，那么其实也可以不经过该元素直接到达末尾，这种判断不会影响是否能到达结尾的结果

* 题解

  > ```java
  > public boolean canJump(int[] nums) {
  >     if(nums == null || nums.length == 0 )return false;
  >     int n = nums.length;
  >     if(n == 1) return true;
  >     int lastIndex = 0;
  >     for (int i = n - 2; i >= 0; i--) {
  >         if(i + nums[i] >= lastIndex) {
  >             lastIndex = i;
  >         }
  >     }
  >     return lastIndex == 0;
  > }
  > ```

* 复杂度分析：n 是数组的大小

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(1)$
