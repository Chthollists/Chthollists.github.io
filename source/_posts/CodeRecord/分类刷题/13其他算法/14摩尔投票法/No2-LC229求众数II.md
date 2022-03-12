---
title: 'LeetCode229.求众数II：「摩尔投票法」&「哈希表」'
reward: true
declare: true
gitalkenable: true
categories: CodeRecord
toc: true
tags:
  - 哈希表
  - 摩尔投票法
  - 位运算
date: 2022-02-21 15:01:37
top:
---
---

* LeetCode229：求众数II
  * 时间：2022-02-21
  * 力扣难度：Medium
  * 个人难度：Medium
  * 数据结构：数组、哈希表
  * 算法：摩尔投票法


---

<!-- more -->

# LeetCode229.求众数II：「摩尔投票法」&「哈希表」

## 1. 题目描述

* 题目：[原题链接](https://leetcode-cn.com/problems/majority-element-ii/)

  * 给定一个大小为 *n* 的整数数组，找出其中所有出现超过 `⌊ n/3 ⌋` 次的元素。
  * 尝试设计时间复杂度为 O(n)、空间复杂度为 O(1) 的算法解决此问题。
  
* 输入输出规范

  * 输入：整数数组
  * 输出：个数超过三分之一的元素的集合

* 输入输出示例

  * 输入：[1,1,1,3,3,2,2,2]
  * 输出：[1, 2]
  

---

## 2. 方法一：哈希表

* 思路：哈希计数

  * 类似LC169多数元素，本题也可以直接使用哈希表计数，统计元素个数，返回个数超过三分之一的元素
  * 同时，可以发现，实际上个数超过三分之一的元素**最多有两个**

* 题解

  > ```java
  > public int majorityElement(int[] nums) {
  >        if(nums == null || nums.length == 0) return null;
  >        int n = nums.length;
  >        List<Integer> list = new ArrayList<>();
  >        Map<Integer, Integer> cnt = new HashMap<>();
  >        for(int i = 0; i < n; i++) {
  >            cnt.put(nums[i], cnt.getOrDefault(nums[i], 0) + 1);
  >            if(cnt.get(nums[i]) > n / 3) list.add(nums[i]);
  >        }
  >        return list;
  >    }
  >    ```
  
* 复杂度分析：n 是数组大小

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(n)$

---

## 3. 方法二：摩尔投票法

* 思路：摩尔投票法增强版

  * **摩尔投票法**适用于找超过半数的元素的情况，实际上，也可以对其进行改进，从而适用于本题的三分之一元素情况
  * 因为要找两个元素，所以维护四个变量，分别表示两个数及其个数
  * 同样按照**对冲消耗**的思想，从第一个数开始遍历数组，遇到相同的就加1，遇到不同的就减1，减到0就重新换个数开始计数
  * 注意此时选出的两个数不一定都是超过三分之一的元素
  * 因为只有一个超过三分之一的元素时，对冲后还会保留另一个数(注意该数不一定是第二多的数)

* 题解：摩尔对冲

  > ```java
  > public List<Integer> majorityElement(int[] nums) {
  >     if(nums == null || nums.length == 0) return null;
  >     List<Integer> list = new ArrayList<>();
  >     int num1 = nums[0], count1 = 0;
  >     int num2 = nums[0], count2 = 0;  
  >     // 投票选举
  >     for(int num : nums) {
  >         // 候选人票数计数
  >         if(num == num1) {
  >             count1++;
  >             continue;
  >         }
  >         if(num == num2) {
  >             count2++;
  >             continue;
  >         }
  > 
  >         // 新的候选人
  >         if(count1 == 0) {
  >             num1 = num;
  >             count1++;
  >             continue;
  >         }
  > 
  > 
  >         if(count2 == 0) {
  >             num2 = num;
  >             count2++;
  >             continue;
  >         }
  > 
  >         // 其他候选人的票数对冲
  >         count1--;
  >         count2--;
  >     }
  >     // 选举后重新确认下票数
  >     count1 = 0;
  >     count2 = 0;
  >     for(int num : nums) {
  >         if(num1 == num) count1++;
  >         else if(num2 == num) count2++;
  >     }
  >     if(count1 > nums.length / 3) list.add(num1);
  >     if(count2 > nums.length / 3) list.add(num2);
  >     return list;
  > }
  > ```
  
* 复杂度分析：n 是数组大小

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(1)$

