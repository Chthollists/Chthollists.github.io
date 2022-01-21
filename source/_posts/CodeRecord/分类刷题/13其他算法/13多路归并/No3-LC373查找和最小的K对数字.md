---
title: 'LeetCode373.查找和最小的K对数字：「多路归并 & 堆」&「二分 & 滑动窗口」'
reward: true
declare: true
gitalkenable: true
categories: CodeRecord
toc: true
tags:
  - 数组
  - 堆
  - 优先队列
  - 二分
  - 多路归并
abbrlink: 3ae6918c
date: 2022-01-14 15:16:36
top:
---
---

* LeetCode373：查找和最小的K对数字
  * 时间：2022-01-14
  * 力扣难度：Meduim
  * 个人难度：Meduim
  * 数据结构：数组、堆、优先队列
  * 算法：多路归并 、二分


---

<!-- more -->

# LeetCode373.查找和最小的K对数字：「多路归并 & 堆」&「二分 & 滑动窗口」

## 1. 题目描述

* 题目：[原题链接](https://leetcode-cn.com/problems/find-k-pairs-with-smallest-sums/)

  * 给定两个以 升序排列 的整数数组 nums1 和 nums2 , 以及一个整数 k 。
  * 定义一对值 (u,v)，其中第一个元素来自 nums1，第二个元素来自 nums2 。
  * 请找到和最小的 k 个数对 (u1,v1),  (u2,v2)  ...  (uk,vk) 。

* 输入输出规范

  * 输入：两个升序数组
  * 输出：K个数对(i, j)

* 输入输出示例

  * 输入：nums1 = [1,7,11], nums2 = [2,4,6], k = 3
  * 输出：[1,2],[1,4],[1,6]
  

---

## 2. 方法一：多路归并 & 小根堆

* 思路：优先队列中维护K个数对

  * 本题是**TopK类型问题**，可以参考[LC215数组的第K大个元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)即求出一个序列中的K大或K小个元素，与普通的TopK问题不同的是本题是两个升序数组，然后求解的是K小个数对
  * 首先，可以想到最基础的方法是，将两个数组可以组成的数对全部枚举出来，并对这些元素进行排序，最终取出K小个元素对应的数对即可，时间复杂度取决于排序算法的复杂度，一般为$O(m*nlog(m*n))$
  * 这种方法属于暴力枚举，复杂度较高，所以需要进行优化，实际上，对于TopK问题，我们完全不需要对整个序列进行排序，而是只关心TopK个元素
  * 所以，我们**只需要维护K个元素**，并取出其中的最值，然后每次添加进来一个新元素，继续取出最值，这些最值组成的集合就是最终的TopK个元素
  * 这种思想类似于堆结构，即**大根堆**和**小根堆**，Java中提供了基于堆思想的`PriorityQueue`**优先队列**结构，无需我们手动构建堆
  
* 堆的常规解题方式：以K小为例

  * 方式一：结果为堆中元素
    * 对于单个序列的TopK问题，首先将序列的前 k 个元素添加到**大根堆**(降序)中
    * 然后遍历剩下的 n - k 个元素，逐个判断其与堆顶元素的大小，当前元素小时，取出堆顶，加入当前元素(堆调整)
    * 最终堆中剩余的 k 个元素就是TopK
  * 方式二：结果为堆中每次取出的元素
    * 对于多个序列的TopK问题，如本题是有两个独立的数组，需要先对各个子序列排序
    * 接着同样在**小根堆**(升序)中维护对应序列个数个元素，然后每次取出堆顶元素，并加入当前堆顶元素(最小值)所在序列的下一个值，一共进行 k 次
    * 取出的元素组成的集合( k 个)就是TopK，这种方式也成为**多路归并**，常见于大文件排序、海量数据排序等场景(面试热点)

* 本题的解题步骤

  * 本题对应第二种方式的情况，但由于本题求解的数对，所以与普通的解法有一点区别
  * 由于本题给出的两个数组都是**升序数组**，可以发现，数对(num1[0], nums2[0])是最小的数对，且对于 nums1 中的一个元素，其与 nums2 中每个元素组成的数对序列也是升序的，反之亦然
  * 因此，当求解过程中确定了 (num1[i], nums2[j]) 为一个 TopK后，下一个TopK应该是从堆中已有元素和  (num1[i+1], nums2[j])、(num1[i], nums2[j+1]) 中产生
  * 首先我们将 k 个元素放入小根堆中，为了避免后续查找TopK时加入元素重复的问题，初始时以其中一个数组为基础，加入(0,0), (1,0), ... , (k-1, 0)这些元素，当取出一个元素 (i, j) 后，新加入的元素为(i, j + 1)
  * 取出的元素组成的就是TopK集合

* 题解

  > ```java
  > public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
  >     if (nums1 == null || nums1.length == 0 || nums2 == null || nums2.length == 0) return null;
  >     List<List<Integer>> smallestPairs = new ArrayList<>();
  >     int n = nums1.length;
  >     int m = nums2.length;
  >     PriorityQueue<int[]> queue = new PriorityQueue<>(k, (a, b) -> (nums1[a[0]] + nums2[a[1]]) - (nums1[b[0]] + nums2[b[1]]));
  >     // 1. 维护 K 个元素到堆中 : (i, 0)
  >     for (int i = 0; i < Math.min(n, k); i++) {
  >         queue.add(new int[]{i, 0});
  >     }
  >     // 2. 取出堆顶元素并加入新元素
  >     while (k > 0 && !queue.isEmpty()) {
  >         int[] pairs = queue.poll();
  >         List<Integer> list = new ArrayList<>();
  >         list.add(nums1[pairs[0]]);
  >         list.add(nums2[pairs[1]]);
  >         smallestPairs.add(list);
  >         if(pairs[1] + 1 < m) queue.add(new int[]{pairs[0], pairs[1] + 1});
  >         k--;
  >     }
  >     return smallestPairs;
  > }
  > ```

* 复杂度分析：n 和 m 分别是两个数组的大小，k 是要求的数对个数

  * 时间复杂度：$O(k*log(min(n,k)))$，初始堆$O(min(k,n))$，堆调整$O(log(min(n,k)))$
  * 空间复杂度：$O(min(n,k)$

---

## 3. 方法二：二分 & 滑动窗口

* 思路：通过二分确定序列前 k 个数对与后面的分界点的值

  * TopK问题也可以通过二分的思路来解决，因为数对序列可以等效为类似**坐标轴**的概念，一定存在一个**分界点**，将前 k 个元素与后面的序列分开
  * 数对的最小值为$nums1[0] + nums2[0]$，最大值为$nums1[n-1] + nums2[m-1]$
  * 可以将最小值最大值作为左右起点，可通过**二分查找**，注意查找的条件不是找到满足大小关系的目标值，而是找到某个确保前面有 k 个，或者算上自身有 k 个数对元素的分界值divideNum
  * 二分法中，**计算小于 mid 值的数对元素个数**可以通过**滑动窗口**的方式，计算元素个数的复杂度为$O(m+n)$，整个二分过程的复杂度为$O((m+n)*log(Max(nums)-Min(nums)))$
  * 找到分界值后，就可以遍历两个有序数组，将大小小于分界值的数对加入到结果集中，如果此时不足 k 个元素，则考虑将等于分界值的部分数对加入到结果集中，因为一共要加入 k 个数对，复杂度为$O(k)$
  * 注意：本题输出的顺序优先输出小索引的nums1数组，所以对于等于分界值的情况要注意查找的顺序

* 题解：直接模拟

  > ```java
  > // 方法二：二分 & 滑动窗口
  > public List<List<Integer>> kSmallestPairs2(int[] nums1, int[] nums2, int k) {
  >     if (nums1 == null || nums1.length == 0 || nums2 == null || nums2.length == 0) return null;
  >     List<List<Integer>> smallestPairs = new ArrayList<>();
  >     int n = nums1.length;
  >     int m = nums2.length;
  > 
  >     // 二分查找第 k 小的数对和的大小
  >     int left = nums1[0] + nums2[0];
  >     int right = nums1[n - 1] + nums2[m - 1];
  >     while (left < right) {
  >         int mid = left + ((right - left) >> 1);
  >         long count = 0; // mid之前的元素的个数
  >         int start = 0;
  >         int end = m - 1;
  >         // 双指针查找当前比 mid 小的元素个数，用来确定二分的方向
  >         while (start < n && end >= 0) {
  >             if(count >= k) break;
  >             if (nums1[start] + nums2[end] > mid) {
  >                 end--;
  >             } else {
  >                 count += end + 1;
  >                 start++;
  >             }
  >         }
  >         // mid前的元素超过k个，向左二分，没超过向右
  >         if (count < k) {
  >             left = mid + 1;
  >         } else {
  >             right = mid;
  >         }
  >     }
  > 
  >     // 分界点的值
  >     int divideNum = left;
  >     // 找到小于分界点的值的数对，并添加到TopK中
  > 
  >     for (int num1 : nums1) {
  >         for (int num2 : nums2) {
  >             if( k > 0 && num1 + num2 < divideNum) {
  >                 List<Integer> list = new ArrayList<>();
  >                 list.add(num1);
  >                 list.add(num2);
  >                 smallestPairs.add(list);
  >                 k--;
  >             }else break;
  >         }
  >     }
  > 
  >     // 找到等于分界点的值的数对
  >     int index = m - 1;
  >     for (int i = 0; i < n && k > 0; i++) {
  >         // 找到第一个不大于分界点值的数对
  >         while (index >= 0 && nums1[i] + nums2[index] > divideNum) {
  >             index--;
  >         }
  >         for (int j = i; j >= 0; j--) {
  >             if(k > 0 && nums1[j] + nums2[index] == divideNum) {
  >                 List<Integer> list = new ArrayList<>();
  >                 list.add(nums1[j]);
  >                 list.add(nums2[index]);
  >                 smallestPairs.add(list);
  >                 k--;
  >             }else break;
  >         }
  >     }
  >     return smallestPairs;
  > }
  > ```

* 复杂度分析：n 和 m 分别是两个数组的大小，k 是要求的数对个数

  * 时间复杂度：$O(k+(m+n)*log(Max(nums)-Min(nums)))$
  * 空间复杂度：$O(1)$

