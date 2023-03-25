The min-product of an array is equal to the minimum value in the array multiplied by the array's sum.

For example, the array [3,2,5] (minimum value is 2) has a min-product of 2 * (3+2+5) = 2 * 10 = 20.
Given an array of integers nums, return the maximum min-product of any non-empty subarray of nums. Since the answer may be large, return it modulo $10^9$ + 7.

Note that the min-product should be maximized before performing the modulo operation. Testcases are generated such that the maximum min-product without modulo will fit in a 64-bit signed integer.

A subarray is a contiguous part of an array.

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/maximum-subarray-min-product
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

#### Ideas of Solving Problems

# HARRRRRRRRRD

#### *Monotonic Stack(单调栈)

**1**.The 'min-product' is defind as 'minimum value' multiply 'array sum', because the 'array sum' is difficult to enumerate, we can consider enumerating 'minimumu value';
**2**.We can enumerate each element *$num_i$* in the array as the minimum value. Since the elements in the array are all postive numbers, the longer the sub array containing *$num_i$* we choose, the better.

**Further on**, the only limitation for selecting a sub array is that *$num_i$* must be the minimum value in the sub array. So we should find:
$\qquad$$\alpha$.An element that precedes *$num_i$* and is strictly smaller than *$num_i$*, and is closest to *$num_i$*, the lower mark of the element is *$left_i$*;
$\qquad$$\beta$.An element that follows *$num_i$* and is strictly smaller than *$num_i$*, and is closest to *$num_i$*, the lower mark of the element is *$right_i$*;

**If no such element exists**, then *$left_i = -1$* and *$right_i = n$*, *n* is the length of array.
And, closed interval [*$left_i$*,*$right_i$*] is contain minimum value *$num_i$* as the longest subarray.

**3**.The finnal answer is $$\mathop{max}\limits_{i=0}^{n-1}(num_i\times\sum\limits_{j=left_i+1}^{right_i-1}num_i)$$

#### Solution1 in C++

```c++
class Solution {
public:
    int maxSumMinProduct(vector<int>& nums) {
        // 左右添加两个哨兵，方便单调栈内的判断
        nums.insert(nums.begin(), 0);
        nums.push_back(0);
        // 前缀和
        vector<long long> presum = {0};
        for(auto& n: nums)
            presum.push_back(presum.back() + n);
        
        // 右边第一个比它小的元素下标
        stack<int> s;
        vector<int> rightFirstSmaller(nums.size(), 0);
        for(int i = 0;i < nums.size();i++){
            // 如果当前元素比栈顶元素小，弹栈
            while(!s.empty() && nums[i] < nums[s.top()]){
                int index = s.top();
                s.pop();
                rightFirstSmaller[index] = i;
            }
            s.push(i);
        }
        
        // 左边第一个比它小的元素下标
        s = stack<int>();
        vector<int> leftFirstSmaller(nums.size(), 0);
        for(int i = nums.size()-1;i >= 0;i--){
            // 如果当前元素比栈顶元素小，弹栈
            while(!s.empty() && nums[i] < nums[s.top()]){
                int index = s.top();
                s.pop();
                leftFirstSmaller[index] = i;
            }
            s.push(i);
        }
        
        // 打擂台得到答案
        long long res = 0;
        for(int i = 1;i < nums.size()-1;i++){
            int l = leftFirstSmaller[i];
            int r = rightFirstSmaller[i];
            res = max(res,nums[i] * (presum[r] - presum[l+1]));
        }
        return res % 1000000007;
    }
};
```

#### Solution2 in C++
```c++
class Solution {
private:
    using LL = long long;
    static constexpr int mod = 1000000007;

public:
    int maxSumMinProduct(vector<int>& nums) {
        int n = nums.size();
        // 数组 left 初始化为 0，数组 right 初始化为 n-1
        // 设置为元素不存在时的特殊值
        vector<int> left(n), right(n, n - 1);
        // 单调栈
        stack<int> s;
        for (int i = 0; i < n; ++i) {
            while (!s.empty() && nums[s.top()] >= nums[i]) {
                // 这里的 right 是非严格定义的，right[i] 是右侧最近的小于等于 nums[i] 的元素下标
                right[s.top()] = i - 1;
                s.pop();
            }
            if (!s.empty()) {
                // 这里的 left 是严格定义的，left[i] 是左侧最近的严格小于 nums[i] 的元素下标
                left[i] = s.top() + 1;
            }
            s.push(i);
        }
        
        // 前缀和
        vector<LL> pre(n + 1);
        for (int i = 1; i <= n; ++i) {
            pre[i] = pre[i - 1] + nums[i - 1];
        }
        
        LL best = 0;
        for (int i = 0; i < n; ++i) {
            best = max(best, (pre[right[i] + 1] - pre[left[i]]) * nums[i]);
        }
        return best % mod;
    }
};
```