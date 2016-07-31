---
title: 最长递增路径-LeetCode
date: 2016-07-09 19:54:50
tags: [LeetCode, 算法]
---
## 题目
给定一个 M × N 的矩阵，找出其中最长的递增序列的长度
递增序列的方向可以是当前元素的上下左右，但不能是对角线，也不可以跨越边界

### Example1

```python
nums = [
  [9,9,4],
  [6,6,8],
  [2,1,1]
]
```

返回值是4，最长的递增序列是：1，2，6，9

### Example2

```python
nums = [
  [3,4,5],
  [3,2,6],
  [2,2,1]
]
```

返回值是4，最长的递增序列是：2，4，5，6 或者 3，4，5，6


题目来源： [Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

## 思路
方法很直观，依次计算每一个元素的最长递增路径长（下面简称LI），然后选取最大的值作为最终结果。

然后要考虑的是，如何计算每一个元素的LI。很容易想到，每个元素有2-4个相邻元素，要计算当前元素的LI，
先要找出值比当前元素大的相邻元素，并计算每个相邻元素的LI，然后找出最大的相邻元素LI，加上1就是当前元素的LI。如果所有相邻元素的值都不比当前元素大，则当前元素的LI为0。

上述就是一个递归过程，递归结束点就是值大于等于所有相邻元素的元素。

按照以上思路实现应该就可以输出正确答案了，但是未必能被Accepted（猜的，我没试过）。因为上述算法虽然能输出正确结果，但是每个元素都可能计算多次LI。所以，我们还应该对每个元素的LI进行缓存，确保每个元素只计算一次。

## 代码
```python
class Solution(object):
    def longestIncreasingPath(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: int
        """
        #参数检查
        if matrix is None:
            return 0
        matrixSize = len(matrix)
        if len(matrix) == 0:
            return 0

        #初始化存放increasingPaths的数组
        increasingPaths = [[0 for j in range(len(matrix[i]))] for i in range(matrixSize)]

        #对每一个元素计算increasingPath
        for i in range(0, matrixSize):
            for j in range(0, len(matrix[i])):
                self.calculateIncreasingPath(matrix, i, j, increasingPaths)

        #选出最大的increasingPath并返回
        return max([max(x) for x in increasingPaths])

    def calculateIncreasingPath(self, matrix, i, j, increasingPaths):
        #如果已经计算过，则直接返回缓存的值
        if increasingPaths[i][j] != 0:
            return increasingPaths[i][j]

        nodeValue = matrix[i][j]

        #计算上方相邻元素
        upPathLen = 0
        if i > 0 and matrix[i - 1][j] > nodeValue:
            upPathLen = self.calculateIncreasingPath(matrix, i - 1, j, increasingPaths)

        #计算左边相邻元素
        leftPathLen = 0
        if j > 0 and matrix[i][j - 1] > nodeValue:
            leftPathLen = self.calculateIncreasingPath(matrix, i, j - 1, increasingPaths)

        #计算下面相邻元素
        downPathLen = 0
        if i < len(matrix) - 1 and matrix[i + 1][j] > nodeValue:
            downPathLen = self.calculateIncreasingPath(matrix, i + 1, j, increasingPaths)

        #计算右边相邻元素
        rightPathLen = 0
        if j < len(matrix[i]) -1 and matrix[i][j + 1] > nodeValue:
            rightPathLen = self.calculateIncreasingPath(matrix, i, j + 1, increasingPaths)

        #找出相邻元素中最大的increasingPath，加上1作为当前元素的increasingPath
        increasingPath = max([upPathLen, leftPathLen, downPathLen, rightPathLen]) + 1

        #缓存计算结果
        increasingPaths[i][j] = increasingPath

        return increasingPath
```
