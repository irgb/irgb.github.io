---
title: Longest Increasing Subsequence
date: 2017-02-24 13:15:20
categories: coding
tags: [leetcode]
toc: true
---

## [Problem](https://leetcode.com/problems/longest-increasing-subsequence/):
Given an unsorted array of integers, find the length of longest increasing subsequence.

For example,
Given `[10, 9, 2, 5, 3, 7, 101, 18]`,
The longest increasing subsequence is `[2, 3, 7, 101]`, therefore the length is `4`. Note that there may be more than one LIS combination, it is only necessary for you to return the length.

Your algorithm should run in O(n2) complexity.

Follow up: Could you improve it to O(n log n) time complexity?

即从一个无序数组中找出最长的递增子序列的长度.

## Solution-1:
常规的DP.时间复杂度O(n^2)
```java
public class Solution {
    public int lengthOfLIS(int[] nums) {
        if(nums == null || nums.length == 0) return 0;
        int [] len = new int[nums.length];
        len[0] = 1;
        int max = 1;
        for(int i = 1;i<nums.length;++i){
            len[i] = 1;
            for(int j = i-1;j>=0;--j){
                if(nums[i] > nums[j]) len[i] = Math.max(len[i], len[j] +1);
            }
            max = Math.max(max, len[i]);
        }
        return max;
    }
}
```

## [Solution-2](https://leetcode.com/submissions/detail/68685864/):
O(nlogn).很精妙(**推荐**),但很难理解.[参考1](http://www.geeksforgeeks.org/longest-monotonically-increasing-subsequence-size-n-log-n/), [参考2](https://discuss.leetcode.com/topic/28738/java-python-binary-search-o-nlogn-time-with-explanation)
```java
public class Solution {
    public int lengthOfLIS(int[] nums) {
        if(nums == null) return 0;
        if(nums.length <= 1) return nums.length;
        int [] tails = new int[nums.length];
        int size = 0;
        for(int i = 0;i<nums.length;++i){
            int l = 0, r = size;//在[0,size]之间搜索
            while(l<r){//其实就是在 tails 数组中找 nums[i]的 lower_bound
                int mid = l + (r-l)/2;
                if(tails[mid] > nums[i]) r = mid;
                if(tails[mid] < nums[i]) l = mid+1;
                else r = mid;
            }
            if(l==size) ++size;//如果tails中所有的元素都小于nums[i], 此时l==size
            tails[l] = nums[i];
        }
        return size;
    }
}
```
## Reference:
- [lower_bound upper_bound binary_search的最佳写法](http://leanote.com/blog/post/579970e4ab644135ea027c92)
- {% post_link slug hello-world %}
