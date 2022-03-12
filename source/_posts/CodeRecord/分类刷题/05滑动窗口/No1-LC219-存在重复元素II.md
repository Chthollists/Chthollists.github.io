---
title: LeetCode219.存在重复元素II：「哈希表」&「滑动窗口」
reward: true
declare: true
gitalkenable: true
categories: CodeRecord
toc: true
tags:
  - 哈希表
  - 滑动窗口
abbrlink: 9d7d5c9f
date: 2022-01-19 13:01:20
top:
---
---

* LeetCode219：存在重复元素II
  * 时间：2022-01-19
  * 力扣难度：Easy
  * 个人难度：Easy
  * 数据结构：数组、哈希表
  * 算法：滑动窗口


---

<!-- more -->

# LeetCode219.存在重复元素II：「哈希表」&「滑动窗口」

## 1. 题目描述

* 题目：[原题链接](https://leetcode-cn.com/problems/contains-duplicate-ii/)

  * 给你一个整数数组 nums 和一个整数 k 
  * 判断数组中是否存在两个不同的索引 i 和 j ，满足 nums[i] == nums[j] 且 abs(i - j) <= k 
  * 如果存在，返回 true ；否则，返回 false 
  * `1 <= nums.length <= 10^5`
  * `-10^9 <= nums[i] <= 10^9`
  * `0 <= k <= 10^5`

* 输入输出规范

  * 输入：数组 nums 和 k
  * 输出：布尔值

* 输入输出示例

  * 输入：nums = [1,2,3,1,2,3], k = 2
  * 输出：false
  

---

## 2. 方法一：哈希表

* 思路

  * 本题要判断数组中是否有重复元素，且限制重复元素的索引之差的大小
  * 因为要判断重复，所以直接想到用**哈希表**存放元素，通过**一次遍历**，一边存元素一边判断是否重复
  
* 题解

  > ```java
  > // 1. 哈希表
  > public boolean containsNearbyDuplicate(int[] nums, int k) {
  >     if(nums == null || nums.length == 0 || k == 0) return false; 
  >     Map<Integer, Integer> map = new HashMap<>();
  >     for(int i = 0; i < nums.length; i++) {
  >         int num = nums[i];
  >         if(map.containsKey(num) && i - map.get(num) <= k) {
  >             return true;
  >         }
  >         map.put(num, i);
  >     }
  >     return false;
  > }
  > ```

* 复杂度分析：n 是数组的大小

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(n)$

---

## 3. 方法二：滑动窗口 & 哈希表

* 思路

  * 同时，我们注意到题目的另一个要求是**重复元素的索引之差小于一个指定的阈值**，即固定了一个**大小确定的窗口**，在窗口中寻找重复元素
  * 所以，实际上哈希表中不需要存放所有的元素，只需要放**窗口大小 k 个元素**即可，然后如果此时窗口内没有重复元素，就取出首个元素，加入下一个元素，再次判断
  * 反复操作，即可完成判断，这是典型的**滑动窗口**思想

* 题解：公式推导

  > ```java
  > // 2. 滑动窗口
  >    public boolean containsNearbyDuplicate(int[] nums, int k) {
  >        if(nums == null || nums.length == 0 || k == 0) return false; 
  >        Set<Integer> set = new HashSet<>();
  >        for(int i = 0; i < nums.length; i++) {
  >            int num = nums[i];
  >            if(i > k) {
  >                set.remove(nums[i - k - 1]);
  >         }
  >            if(!set.add(num)) return true;          
  >        }
  >        return false;
  >    }
  >    ```
  
* 复杂度分析：n 是数组的大小

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(k)$
