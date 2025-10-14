> - [918. 环形子数组的最大和](https://leetcode.cn/problems/maximum-sum-circular-subarray/)
> - 

## Kadane算法详解

### 1. 问题定义
**最大子数组和问题**：在一个整数数组中，找到一个连续子数组，使其元素之和最大。

**示例**：`[-2, 1, -3, 4, -1, 2, 1, -5, 4]`

- 最大子数组：`[4, -1, 2, 1]`，和为6

---

### 2. 算法核心思想

Kadane算法基于**动态规划**思想，但进行了空间优化。其核心洞察是：

**对于每个位置，我们只需要考虑一个问题：**
> 是以当前元素开始一个新的子数组，还是将当前元素加入到前面的子数组中？

---

### 3. 算法步骤分解

让我们用示例 `[-2, 1, -3, 4, -1, 2, 1, -5, 4]` 来逐步分析：

**初始化**：

- `max_ending_here = nums[0]`（以当前位置结尾的最大和）
- `max_so_far = nums[0]`（全局最大和，当 max_so_far 更新时，会以此为锚点更新 max_ending_here，如果还想知道最大子数组，可以在此添加index记录）

**遍历过程**：

| 索引 | 元素 | 决策：`max(当前元素, 前序和+当前元素)` | `max_ending_here`   | `max_so_far`   |
| ---- | ---- | -------------------------------------- | ------------------- | -------------- |
| 0    | -2   | -                                      | -2                  | -2             |
| 1    | 1    | `max(1, -2+1= -1)`                     | **1**               | `max(-2, 1)=1` |
| 2    | -3   | `max(-3, 1-3= -2)`                     | **-2**              | `max(1, -2)=1` |
| 3    | 4    | `max(4, -2+4= 2)`                      | **4**（新锚点）     | `max(1, 4)=4`  |
| 4    | -1   | `max(-1, 4-1= 3)`                      | **3**               | `max(4, 3)=4`  |
| 5    | 2    | `max(2, 3+2= 5)`                       | **5**               | `max(4, 5)=5`  |
| 6    | 1    | `max(1, 5+1= 6)`                       | **6**（最后的锚点） | `max(5, 6)=6`  |
| 7    | -5   | `max(-5, 6-5= 1)`                      | **1**               | `max(6, 1)=6`  |
| 8    | 4    | `max(4, 1+4= 5)`                       | **5**               | `max(6, 5)=6`  |

**最终结果**：6（来自子数组 `[4, -1, 2, 1]`）

---

### 4. 完整代码实现

```python
def kadane(nums):
    if not nums:
        return 0
    
    max_ending_here = nums[0]
    max_so_far = nums[0]
    
    for i in range(1, len(nums)):
        # 关键决策：延续还是重新开始？
        max_ending_here = max(nums[i], max_ending_here + nums[i])
        # 更新全局最大值
        max_so_far = max(max_so_far, max_ending_here)
    
    return max_so_far

# 测试
test_cases = [
    [-2, 1, -3, 4, -1, 2, 1, -5, 4],  # 输出：6
    [1, 2, 3, -2, 5],                 # 输出：9
    [-1, -2, -3, -4],                 # 输出：-1
    [5, 4, -1, 7, 8]                  # 输出：23
]

for nums in test_cases:
    print(f"数组: {nums} -> 最大子数组和: {kadane(nums)}")
```

---

### 5. 算法正确性证明

**为什么这个决策是正确的？**

假设我们有一个最优子数组 `A[i...j]`，那么对于任意位置 `k`（i ≤ k ≤ j）：
- 子数组 `A[i...k]` 的和必须是非负的
- 如果 `A[i...k]` 的和为负数，那么去掉这部分会得到更大的和 `A[k+1...j]`

因此，当我们计算 `max_ending_here` 时：
- 如果前面的和是负数，就重新开始
- 如果前面的和是正数，就继续累加

---

### 6. 时间复杂度与空间复杂度

- **时间复杂度**：O(n)，只需一次遍历
- **空间复杂度**：O(1)，只用了常数个变量

---

### 7. 变种：记录子数组边界

如果需要知道具体是哪个子数组，可以稍作修改：

```python
def kadane_with_indices(nums):
    if not nums:
        return 0, 0, 0
    
    max_ending_here = nums[0]
    max_so_far = nums[0]
    start = end = 0
    temp_start = 0
    
    for i in range(1, len(nums)):
        if nums[i] > max_ending_here + nums[i]:
            max_ending_here = nums[i]
            temp_start = i
        else:
            max_ending_here += nums[i]
        
        if max_ending_here > max_so_far:
            max_so_far = max_ending_here
            start = temp_start
            end = i
    
    return max_so_far, start, end
```

---

### 8. 在环形数组问题中的应用

理解了标准Kadane算法后，我们就能：
1. 用Kadane求普通最大子数组和（情况1）
2. 用类似的思路求最小子数组和（情况2）
3. 结合两种情况解决环形问题
