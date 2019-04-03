---

title: 数据结构---数组
date: 2019-04-02 17:39:43
categories:
- 数据结构
tags: 

---

## 概述

### 定义：

&emsp;&emsp;**有序**的元素序列，用于存储多个**相同类型数据**数据的集合。如下图：
![](Data_structure\array.png)

### 主要特性：

- 类型相同
- 有序
- 能通过下标访问

在java中数组一经初始化，他的大小是固定的。为此java提供了一些集合框架来支持动态数组的操作，比如ArrayList。


### 扩展

多维数组：由低维数组组合而成。



## 练习


从LeetCode中选取了几道比较有代表性的题目在这边做总结。所有题目链接：[数组简介](https://leetcode-cn.com/explore/learn/card/array-and-string/198/introduction-to-array/771/)


###	至少是其他数字两倍的最大数

思路：使用数组的有序性。将数据进行排序，但又不需要全部排序完成，只需要找出最大和第二大的数，所以用不完全的冒泡排序，进行2次冒泡找出最大以及第二大的进行比较，再冒泡的同时记录最大值的索引。最后返回结果值。以下为实现代码：

```
class Solution {
    public int dominantIndex( int[] nums ) {
        if (nums.length < 2) {
            return 0;
        }
        Stack< Integer > stack = new Stack<>();
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < nums.length - 1 - i; j++) {
                int num = nums[j];
                int num1 = nums[j + 1];
                if (num > num1) {
                    swap(j, nums);
                } else {
                    if (i == 0) {
                        stack.push(j + 1);
                    }
                }
            }
        }
        if (nums[nums.length - 1]  >= nums[nums.length - 2]* 2) {
            return stack.isEmpty() ? 0 : stack.pop();
        }
        return -1;
    }
    private void swap( int j, int[] nums ) {
        int max = nums[j];
        nums[j] = nums[j + 1];
        nums[j + 1] = max;
    }
}

```


### 对角线遍历

思路：根据规律发现，有2种移动方式，右上和左下。

右上

- 先将节点右移。若无法右移则下移一个节点，下移后改变方向往左下移动。
- 若右移成功，再将节点上移。若无法上移则改变方向，则直接改变移动方向。
- 若上移成功，则继续右上步骤直到改变方向。

左下

- 先将节点下移。若节点无法下移，则将节点右移，然后更换方向。
- 下移成功后，将节点左移。若左移不成功，则直接更换方向
- 若左移成功，继续左下移动，直到改变方向。


以下为实现代码：
```
class Solution {
    public int[] findDiagonalOrder( int[][] matrix ) {
        if (matrix.length == 0) {
            return new int[0];
        }
        if (matrix.length == 1) {
            return matrix[0];
        }
        int rows = matrix.length - 1;
        int cols = matrix[0].length - 1;
        int[] result = new int[matrix[0].length * matrix.length];
        int rowCur = 0;
        int colCur = 0;
        int direct = 1;
        int index = 0;
        while (!(rowCur == rows && colCur == cols)) {
            result[index] = matrix[rowCur][colCur];
            index++;
            if (direct == 1) {
                // 向右上遍历
                if (colCur == cols) {
                    // 没有右节点，则取下节点
                    rowCur = rowCur + 1;
                    direct = 0;
                    continue;
                }
                // 右右节点
                colCur = colCur + 1;
                if (rowCur == 0) {
                    // 没有上节点,直接取右节点,改变方向
                    direct = 0;
                    continue;
                }
                // 有上节点
                rowCur = rowCur - 1;
                continue;
            }

            if (direct == 0) {
                // 向左下遍历
                if (rowCur == rows) {
                    // 没有下节点，直接走右节点，且更换方向
                    colCur = colCur + 1;
                    direct = 1;
                    continue;
                }
                // 有下节点
                rowCur = rowCur + 1;
                if (colCur == 0) {
                    // 没有左节点，变方向
                    direct = 1;
                    continue;
                }
                colCur = colCur - 1;
                continue;
            }
        }
        result[result.length-1] = matrix[rows][cols];
        return result;
    }
}
``` 