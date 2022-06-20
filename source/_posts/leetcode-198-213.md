---
title: "LeetCode #198 #213 打家劫舍"
date: 2022-06-21 00:25:38
tags:
---

# #198

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.size() == 1)
            return nums[0];
        
        nums[1] = max(nums[0], nums[1]);
        for(int i=2; i<nums.size(); i++) {
            nums[i] = max(nums[i-2]+nums[i], nums[i-1]);
        }
        return nums[nums.size()-1];
    }
};
```

### 对正确性的理解

首先经过处理后的 `nums[i]` 代表了在 `[0, i]` 这个区间里，也就是到第 i 间房为止，可能偷到的最大金额，此时数组必单调递增。

为什么 `nums[i] = max(nums[i-2]+nums[i], nums[i-1])` 就是到 i 为止的最大金额？

- 若 `nums[i-1] > nums[i-2] + nums[i]` ，则 `nums[i-1] > nums[i-3]` 肯定也成立，第 i-1 间房肯定被偷过了，此时也不可能再偷第 i 间房，只能沿用 `nums[i-1]` 的值
- 若  `nums[i-1] <= nums[i-2] + nums[i]` ，无需解释

# #213

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.size() == 1)
            return nums[0];
        if (nums.size() == 2)
            return max(nums[0], nums[1]);

        return max(doRob(nums, 0, nums.size()-2), doRob(nums, 1, nums.size()-1));
    }

    int doRob(vector<int> nums, int begin, int end) {
        nums[begin+1] = max(nums[begin], nums[begin+1]);
        for(int i=begin+2; i<=end; i++) {
            nums[i] = max(nums[i-2]+nums[i], nums[i-1]);
        }
        return nums[end];
    }
};
```

与上题类似，只是分两种情况计算，如果第一间房可偷，则最后一间房不可偷，反之亦然
