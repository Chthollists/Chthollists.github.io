---
title: 'LeetCode89.格雷编码：「回溯」&「动态规划」&「公式法」'
reward: true
declare: true
gitalkenable: true
categories: CodeRecord
toc: true
tags:
  - 回溯
  - 动态规划
  - 位运算
abbrlink: c70781d8
date: 2022-01-08 22:18:33
top: 
---
---

* LeetCode89：格雷编码
  * 时间：2022-01-08
  * 力扣难度：Medium
  * 个人难度：Medium+
  * 数据结构：二进制、决策树
  * 算法：动态规划、回溯、位运算


---

<!-- more -->

# LeetCode89.格雷编码：「回溯」&「动态规划」&「公式法」

## 1. 题目描述

* 题目：[原题链接](https://leetcode-cn.com/problems/gray-code/)

  * n 位格雷码(Gray Code)序列是一个由 2n 个整数组成的序列，其中：
    * 每个整数都在范围 [0, $2^n - 1$] 内（含 0 和 $2^n - 1$）
    * 第一个整数是 0
    * 一个整数在序列中出现 不超过一次
    * 每对**相邻**整数的二进制表示恰好**一位不同** 
    * **第一个**和**最后一个**整数的二进制表示恰好一位不同
  * 给你一个整数 n ，返回任一有效的 n 位格雷码序列 

* 输入输出规范

  * 输入：格雷码的位数n
  * 输出：格雷码整数序列的数组

* 输入输出示例

  * 输入：n = 2
  * 输出：[0,1,3,2]
  * 输入：n = 3
  * 输出：[0,1,3,2,6,7,5,4]，即`000 -> 001->011->010->110->111->101->100`
  

---

## 2. 方法一：回溯

* 思路

  * 本题要求解n位格雷码，可以首先通过举例观察得到：
    * n = 2 时，输出序列是 [0,1,3,2]，对应 [00, 01, 11, 10]
    * n = 3 时，输出序列时 [0,1,3,2,6,7,5,4]，对应 [000, 001, 011, 010, 110, 111, 101, 100]
  * 可以发现规律：对于格雷码的二进制数形式，如果一位一位取值时(0或1)，会满足关于01和10对称的关系
  * 将整个问题抽象成为决策树的形式，可以更明显的看到格雷码序列关于01和10对称的规律
  * 此时该问题转化为回溯中常见的组合问题，且是具有一定规律的组合，即决策树每一层的遍历都是先遍历01，再遍历10，以此往复
  
* 回溯+决策树的思路图解

  ![格雷编码回溯决策树](https://gitee.com/chthollists/PicRepo/raw/master/CodeRecord/images-daily/LC89%E6%A0%BC%E9%9B%B7%E7%BC%96%E7%A0%81%E5%9B%9E%E6%BA%AF%E5%86%B3%E7%AD%96%E6%A0%91.png)

* 复杂度分析：n是输入的格雷码的位数

  * 时间复杂度：$O(2^n)$，一共要遍历$2^n$个数字，将其组成格雷码序列
  * 空间复杂度：$O(n)$，存放二进制的结构需要$O(n)$

* 题解：`StringBuilder`储存回溯过程中的二进制数

  > ```java
  > List<Integer> grayCodeList = new ArrayList<>();
  >    StringBuilder binaryCode = new StringBuilder();
  >    int[] left = new int[]{0, 1};
  >    int[] right = new int[]{1, 0};
  >    
  >    public List<Integer> grayCode(int n) {
  >        // int[] nums ： 该数组表示接下来去取的二进制数字
  >        backTracing(n, binaryCode, left);
  >        return grayCodeList;
  >    }
  >    
  >    private void backTracing(int n, StringBuilder binaryCode, int[] nums) {
  >        // 找到一个符合的结果并添加到结果集中
  >        if (binaryCode.length() == n) {
  >            // 二进制与十进制转换 Integer.valueOf(binaryCode.toString(), 2);
  >            int grayCode = Integer.parseInt(binaryCode.toString(), 2);
  >            grayCodeList.add(grayCode);
  >            return;
  >        }
  >    
  >        // 回溯{0，1}数组
  >        binaryCode.append(nums[0]);
  >        backTracing(n, binaryCode, left);
  >        binaryCode.deleteCharAt(binaryCode.length() - 1);
  >        
  >        // 回溯{1，0}数组
  >        binaryCode.append(nums[1]);
  >        backTracing(n, binaryCode, right);
  >        binaryCode.deleteCharAt(binaryCode.length() - 1);
  >    }
  >    ```
  
* 思考

  * 本题使用了字符串`StringBuilder`类型的变量来储存回溯过程中的二进制数，这是为了使用`Integer`类的API直接将二进制数转换为最后要输出的十进制数
  * 也可以使用回溯常用的`List`类型来存储二进制数，此时需要手动实现二进制与十进制的转换
  * 下面附上该版本的解法，会更加复杂一些

* 题解：`List`储存回溯过程中的二进制数

  > ```java
  > List<List<Integer>> binaryCodeList = new ArrayList<>();
  > List<Integer> list = new ArrayList<>();
  > int[] left = new int[]{0, 1};
  > int[] right = new int[]{1, 0};
  > 
  > public List<Integer> grayCode(int n) {
  >     backTracing(n, list, left);
  >     // 二进制与十进制转换
  >     List<Integer> grayCodeList = new ArrayList<>();
  >     for(List<Integer> binaryCode:binaryCodeList) {
  >         int grayCode = 0;
  >         for(int i = 0; i < binaryCode.size(); i++) {
  >             grayCode = grayCode*2 + binaryCode.get(i);
  > 		}
  >         grayCodeList.add(grayCode);
  >     }
  >     return grayCodeList;
  > }
  > 
  > private void backTracing(int n, List<Integer> list, int[] nums) {
  >     // 找到一个符合的结果并添加到结果集中
  >     if (list.size() == n) {
  >         binaryCodeList.add(new ArrayList<>(list));
  >         return;
  >     }
  > 
  >     // 回溯{0，1}数组
  >     list.add(nums[0]);
  >     backTracing(n, list, left);
  >     list.remove(list.size() - 1);
  >     
  >     // 回溯{1，0}数组
  >     list.add(nums[1]);
  >     backTracing(n, list, right);
  >     list.remove(list.size() - 1);
  > }
  > ```

---

## 3. 方法二：位运算+二进制推导

* 思路

  * 首先需要了解下格雷码的特性：[格雷码百科](https://baike.baidu.com/item/%E6%A0%BC%E9%9B%B7%E7%A0%81)
    * 二进制为 0 值的格雷码为第零项
    * 第一项改变最右边的位元
    * 第二项改变右起第一个为1的位元的左边位元
    * 第三、四项方法同第一、二项，如此反复，即可排列出n个位元的格雷码
  * 示例：n = 3 
    * 0 0 0 第零项初始化为 0
    * 0 0 **1** 第一项改变上一项最右边的位元
    * 0 **1** 1 第二项改变上一项右起第一个为 1 的位元的左边位
    * 0 1 **0** 第三项同第一项，改变上一项最右边的位元
    * **1** 1 0 第四项同第二项，改变最上一项右起第一个为 1 的位元的左边位
    * 1 1 **1** 第五项同第一项，改变上一项最右边的位元
    * 1 **0** 1 第六项同第二项，改变最上一项右起第一个为 1 的位元的左边位
    * 1 0 **0** 第七项同第一项，改变上一项最右边的位元

* 复杂度分析：n是path字符串的长度

  * 时间复杂度：$O(n*2^n)$
  * 空间复杂度：$O(1)$

* 题解：运用`Integer.highestOneBit()`方法

  > ```java
  > public List<Integer> grayCode(int n) {
  >     List<Integer> res = new ArrayList<Integer>();
  >     res.add(0); // 第零项
  >     for (int i = 1; i < (1 << n); i++) {
  >         int prev = res.get(i - 1);
  >         // 1. 改变最右边的位元
  >         if (i % 2 == 1) {
  >             prev ^= 1;  // 0 ^ 1 = 1 and 1 ^ 1 = 0
  >             res.add(prev);
  >         } else { // 2. 改变右起第一个为1的位元的左边位
  >             int temp = prev;
  >             // 寻找右边起第一个为1的位元
  >             for (int j = 0; j < n; j++) {
  >                 if ((temp & 1) == 1) {
  >                     // 改变该位置的左边位置
  >                     prev = prev ^ (1 << (j + 1));
  >                     res.add(prev);
  >                     break;
  >                 }
  >                 // 向右移位
  >                 temp = temp >> 1;
  >             }
  >         }
  >     }
  >     return res;
  > }
  > ```

---

## 4. 方法三：动态规划

* 思路：动态规划

  * 本题要求解n位格雷码，可以首先通过举例观察得到：
    * n = 1 时，输出序列是 [0,1]
    * n = 2 时，输出序列是 [0,1,3,2]
    * n = 3 时，输出序列时 [0,1,3,2,6,7,5,4]
  * 即可以得到前缀对称规律
    * 1位格雷码有两个数字
    * 当 n 增大时，新的序列与旧序列是相关的，且是在旧序列上面追加了新的序列
    * n+1 位格雷码中的**前**$2^n$个数字等于 n 位格雷码序列，按**顺序**书写，加**前缀0**
    * n+1 位格雷码中的**后**$2^n$个码字等于 n 位格雷码序列，按**逆序**书写，加**前缀1**
    * 格雷码的最后一位由于要和首位(0)只有一位不同，所以都是$2^{n - 1}$
  * 因此，n+1 位格雷码的序列等于 **n 位格雷码序列顺序加前缀0** + **n 位格雷码序列逆序加前缀1**
    * 二进制前缀+0表示数字没有改变
    * 二进制前缀+1表示在原来的 n 位二进制数的基础上，十进制增加 $2^n$
  * 根据这种新序列依赖于旧序列，且边界(首尾格雷码)确定的场景，可以考虑使用动态规划的思想来解决

* 图解：前缀对称规律

  ![LC89格雷编码动态规划思路](https://gitee.com/chthollists/PicRepo/raw/master/CodeRecord/images-daily/LC89%E6%A0%BC%E9%9B%B7%E7%BC%96%E7%A0%81%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E6%80%9D%E8%B7%AF.png)

* 动态规划三剑客的思路

  * DP数组定义：`dp[i]`表示格雷码序列的第 i 个数字，i 从0开始
  * 边界条件：dp[0] = 0 dp[n] = $2^{n-1}$
  * 状态转移方程：注意求解 n 位格雷码序列时，参照的就是 n-1 位的格雷码序列
    * $i<2^{n-1}$：$dp[i] = dp[i]$
    * $2^{n-1}<=i<2^n$：$dp[i] = dp[2^n-1-i]+2^{n-1}$
  * 注意
    * 本题不使用普通数组DP，而使用List进行DP
    * 由于求 n 位格雷码序列时需要知道 n-1 位的情况，所以需要外层循环 0 ~ n 位的格雷码序列
    * 由于前一半的序列不变，所以直接可以从后半序列开始添加`add`

* 复杂度分析：n是path字符串的长度

  * 时间复杂度：$O(2^n)$，外层循环$O(n)$，内层循环每次为$O(2^i)$，最后一次为$O(2^n)$，整体为$O(2^n)$量级
  * 空间复杂度：$O(1)$

* 题解

  > ```java
  > // 方法二：动态规划
  >    public List<Integer> grayCode(int n) {
  >        List<Integer> dp = new ArrayList<>();
  >        dp.add(0);
  >        for (int i = 0; i < n; i++) {
  >            int prefix = 1 << i; // 前缀为 1, 左移i位
  >            for (int j = dp.size() - 1; j >= 0 ; j--) {
  >                dp.add(dp.get(j) + prefix);
  >            }
  >        }
  >        return dp;
  >    }
  >    ```

---

## 5. 方法四：格雷码公式法

* 思路

  * 一个 n 位二进制码可以直接转化为一个 n 位格雷码，对应的公式为：
    * 对 n 位二进制码，从右到左，以0到n-1编号，即$B_{n-1}...B_1B_0$
    * 对 n 位格雷码，从右到左，以0到n-1编号，即$G_{n-1}...G_1G_0$
    * 如果二进制码的第 i 位和 i+1 位相同，则对应的格雷码的第 i 位为0，否则为1
    * 当 i+1 = n 时，二进制码的第 n 位被认为是0，即第 n-1 位不变
  * 因此，可以直接根据公式算出整个格雷码序列

* 图解：格雷码与二进制码转换公式

  ![LC89格雷编码公式](https://gitee.com/chthollists/PicRepo/raw/master/CodeRecord/images-daily/LC89%E6%A0%BC%E9%9B%B7%E7%BC%96%E7%A0%81%E5%85%AC%E5%BC%8F.png)

* 复杂度分析：n是path字符串的长度

  * 时间复杂度：$O(2^n)$
  * 空间复杂度：$O(1)$

* 题解

  > ```java
  > public List<Integer> grayCode(int n) {
  >     List<Integer> res = new ArrayList<>();
  >     for(int binary = 0;binary < 1 << n; binary++){
  >         res.add(binary ^ binary >> 1);
  >     }
  >     return res;
  > }
  > ```

---

## 参考资料

* 格雷码Wiki：https://zh.wikipedia.org/wiki/%E6%A0%BC%E9%9B%B7%E7%A0%81
* 详细通俗的思路分析，多解法：https://leetcode-cn.com/problems/gray-code/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--12/
