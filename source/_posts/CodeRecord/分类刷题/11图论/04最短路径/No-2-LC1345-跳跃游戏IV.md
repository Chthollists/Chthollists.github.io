---
title: 'LeetCode1345.跳跃游戏IV：「单向BFS」&「双向BFS」'
reward: true
declare: true
gitalkenable: true
categories: CodeRecord
toc: true
tags:
  - 图论
  - BFS
abbrlink: 33700aba
date: 2022-01-21 15:52:39
top:
---
---

* LeetCode1345：跳跃游戏IV
  * 时间：2022-01-21
  * 力扣难度：Hard
  * 个人难度：Hard
  * 数据结构：数组、图论
  * 算法：BFS、双向BFS


---

<!-- more -->

# LeetCode1345.跳跃游戏IV：「单向BFS」&「双向BFS」

## 1. 题目描述

* 题目：[原题链接](https://leetcode-cn.com/problems/jump-game-iv/)

  * 给你一个整数数组 arr ，你一开始在数组的第一个元素处（下标为 0）。
  * 每一步，你可以从下标 i 跳到下标：
    * i + 1 满足：i + 1 < arr.length
    * i - 1 满足：i - 1 >= 0
    * j 满足：arr[i] == arr[j] 且 i != j
  * 请你返回到达数组最后一个元素的下标处所需的 最少操作次数 。

  * 注意：任何时候你都不能跳到数组外面。

  * `1 <= arr.length <= 5 * 10^4`
  * `-10^8 <= arr[i] <= 10^8`

* 输入输出规范

  * 输入：数组 arr
  * 输出：最小步数

* 输入输出示例

  * 输入：arr = [100,-23,-23,404,100,23,23,23,3,404]
  * 输出：3
  

---

## 3. 方法一：单向BFS

* 思路

  * 本题是求解给定的数组中，按照一定的规则到达末尾的最小步数，即**最短路径问题**，可以根据元素跳跃的规则来抽象成一个**无向图**
  * 本题的无向图：图的**顶点**是数组中的元素，**边**就表示两个元素能否直接跳跃过去，是无向边，且权值都为1，属于无权图
  * 通过将数组抽象为无向图，就将求到达末尾的最小步数转化为最短路径问题，可以使用BFS来解决，**暴力BFS**的话需要遍历所有顶点和边，因为本题等值的顶点间也存在边，所以**时间复杂度**约为 $O(n^2)$，暴力BFS会TLE超时
  * 因此，需要对BFS的过程进行一定的优化，我们发现，对于等值顶点构成的子图，其实在遍历其中一个顶点时，就将整个子图访问一次，之后清空整个子图，从而避免重复访问该子图
  
* 解题步骤

  * 首先，使用哈希表保存图的顶点和边，即key为元素的值，value 记录的是索引
  * 然后，将首个顶点入队，开始BFS搜素，当搜索到某个顶点时，将其连接的其他顶点入队
  * 搜索中，分为三种情况，向前跳、向后跳和等值跳，分类讨论，将此时有边连接的顶点和此时的步数更新入队列
  * 为了避免重复访问顶点，需要标记每个顶点是否访问过(布尔数组)，如果已经访问过就不进行入队操作，因为访问过的节点能否直接到达末尾已经求解过了
  * 此外，为了避免重复访问子图，还需要在第一次添加等值子图入队后，将该元素从哈希表中删除，从而后续搜索中不会在将该子图加入队列
  * 注意：队列中存放一个长度为2的数组，分别表示当前索引和步数；也可以队列中只放索引，步数通过维护一个和元素数组一样长的数组来表示，同时还可以用该数组表示当前元素是否访问过(代替布尔数组)

* 图解

  ![LC1345跳跃游戏BFS过程分析](https://gitee.com/chthollists/PicRepo/raw/master/CodeRecord/images-daily/LC1345%E8%B7%B3%E8%B7%83%E6%B8%B8%E6%88%8F-BFS%E8%BF%87%E7%A8%8B%E5%88%86%E6%9E%90.png)

* 题解

  > ```java
  > // 方法1 ： 单向BFS
  > public int minJumps(int[] arr) {
  >     if (arr == null || arr.length == 0) return -1;
  >     int n = arr.length;
  >     if (n == 1) return 0;
  >     Map<Integer, List<Integer>> indexMap = new HashMap<>();
  >     // 哈希表存 value 和 index
  >     for (int i = 0; i < n; i++) {
  >         List<Integer> list = indexMap.getOrDefault(arr[i], new ArrayList<>());
  >         list.add(i);
  >         indexMap.put(arr[i], list);
  >     }
  >     boolean[] visited = new boolean[n];
  >     Deque<int[]> deque = new LinkedList<>(); // int[] {index, step}
  >     deque.add(new int[]{0, 0});
  >     while (!deque.isEmpty()) {
  >         int[] cur = deque.poll();
  >         int index = cur[0];
  >         int step = cur[1];
  >         // 搜到了末尾
  >         if (index == n - 1) return step;
  >         // 前后跳
  >         if (index + 1 < n && !visited[index + 1]) {
  >             deque.add(new int[]{index + 1, step + 1});
  >             visited[index + 1] = true;
  >         }
  >         if (index - 1 >= 0 && !visited[index - 1]) {
  >             deque.add(new int[]{index - 1, step + 1});
  >             visited[index - 1] = true;
  >         }
  >         // 等值跳
  >         if (indexMap.containsKey(arr[index])) {
  >             List<Integer> indexList = indexMap.get(arr[index]);
  >             for (int idx : indexList) {
  >                 if(!visited[idx]) {
  >                     deque.add(new int[]{idx, step + 1});
  >                     visited[idx] = true;
  >                 }
  >             }
  >         }
  >         // 注意从哈希表中删除访问过的元素
  >         indexMap.remove(arr[index]);
  >     }
  > }
  > return -1;
  > }
  > ```

* 复杂度分析：n 是数组的大小

  * 时间复杂度：$O(n)$，通过拒绝访问已经访问过的顶点和等值子图，从平方级优化到线性
  * 空间复杂度：$O(n)$

---

## 2. 方法二：双向BFS

* 思路

  * 本题如果使用**单向BFS**暴搜的话，复杂度约为 $O(n^2)$，会造成超时问题，通过额外维护表示顶点是否访问过的数组，并且将访问过的元素从哈希表中进行删除，可以优化到线性复杂度
  * 本题同样可以使用**双向BFS**来进行搜索，双向BFS主要是用来解决单向BFS中**搜索空间爆炸的问题**，即从开头和结尾一起开始搜索，当**搜索到相同的值**时，意味着找到了一条联通起点和终点的**最短路径**

* 解题步骤

  * 首先，为了从开头和结尾一起开始搜索，需要两个队列
  * 其次，为了使的两个搜索方向尽可能平均，每次从元素较少的队列中取值搜索，进行扩展，加入新的顶点到队列中(update)
  * 在搜索过程中，如果搜索到了相同的值，包括对方已经搜索过的值，就表示起点到终点的最短路径
  * 对于本题，搜索过程仍然分三种情况讨论，只是可以省略哈希表的删除操作

* 题解

  > ```java
  > // 方法2 ： 双向BFS
  > public int minJumps(int[] arr) {
  >     if (arr == null || arr.length == 0) return -1;
  >     int n = arr.length;
  >     if (n == 1) return 0;
  >     Map<Integer, List<Integer>> indexMap = new HashMap<>();
  >     // 哈希表存 value 和 index
  >     for (int i = 0; i < n; i++) {
  >         List<Integer> list = indexMap.getOrDefault(arr[i], new ArrayList<>());
  >         list.add(i);
  >         indexMap.put(arr[i], list);
  >     }
  > 
  >     int[] step1 = new int[n];
  >     int[] step2 = new int[n];
  >     Arrays.fill(step1, -1);
  >     Arrays.fill(step2, -1);
  > 
  >     Deque<Integer> deque1 = new LinkedList<>(); //  index
  >     Deque<Integer> deque2 = new LinkedList<>();
  >     deque1.add(0);
  >     step1[0] = 0;
  >     deque2.add(n - 1);
  >     step2[n - 1] = 0;
  > 
  >     while (!deque1.isEmpty() && !deque2.isEmpty()) {
  >         int step = -1;
  >         if (deque1.size() <= deque2.size()) {
  >             step = update(deque1, deque2, step1, step2, indexMap, arr);
  >         } else {
  >             step = update(deque2, deque1, step2, step1, indexMap, arr);
  >         }
  >         if (step != -1) return step;
  >     }
  >     return -1;
  > }
  > 
  > private int update(Deque<Integer> deque1, Deque<Integer> deque2, int[] step1, int[] step2, Map<Integer, List<Integer>> indexMap, int[] arr) {
  >     int size = deque1.size();
  >     int n = arr.length;
  >     while (size > 0) {
  >         int index = deque1.poll();
  >         int step = step1[index];
  >         // 前后跳
  >         if (index + 1 < n) {
  >             if (step2[index + 1] != -1) return step + 1 + step2[index + 1];
  >             if (step1[index + 1] == -1) {
  >                 deque1.add(index + 1);
  >                 step1[index + 1] = step + 1;
  >             }
  >         }
  >         if (index - 1 >= 0) {
  >             if (step2[index - 1] != -1) return step + 1 + step2[index - 1];
  >             if (step1[index - 1] == -1) {
  >                 deque1.add(index - 1);
  >                 step1[index - 1] = step + 1;
  >             }
  >         }
  >         // 等值跳
  >         if (indexMap.containsKey(arr[index])) {
  >             List<Integer> indexList = indexMap.get(arr[index]);
  >             for (int idx : indexList) {
  >                 if (step2[idx] != -1) return step + 1 + step2[idx];
  >                 if (step1[idx] == -1) {
  >                     deque1.add(idx);
  >                     step1[idx] = step + 1;
  >                 }
  >             }
  >         }
  >         // 注意从哈希表中删除访问过的元素
  >         indexMap.remove(arr[index]);
  >         size--;
  >     }
  >     return -1;
  > }
  > ```
  
* 复杂度分析：n 是数组的大小

  * 时间复杂度：$O(n)$
  * 空间复杂度：$O(n)$

---

## 参考资料

* https://leetcode-cn.com/problems/jump-game-iv/solution/gong-shui-san-xie-noxiang-xin-ke-xue-xi-q9tb1/

