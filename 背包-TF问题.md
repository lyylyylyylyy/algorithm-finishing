True or False问题

- 416. 分割等和子集

这两类问题最后是判断True or False问题，可以转化为为背包问题

## 分割等和子集

给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

注意:

每个数组中的元素不会超过 100
数组的大小不会超过 200
###示例 1:

输入: [1, 5, 11, 5]

输出: true

解释: 数组可以分割成 [1, 5, 5] 和 [11]

---

思路：
- 选与不选的问题
- 首先，数组的总和一定是偶数，否则分成两部分一定不相等
- 状态定义：dp[i][j]表示从数组的 [0, i] 这个子区间内挑选一些正整数，每个数只能用一次，使得这些数的和恰好等于 j。
- 状态转移方程：很多时候，状态转移方程思考的角度是“分类讨论”，对于“0-1 背包问题”而言就是“当前考虑到的数字选与不选”。
    - 1、不选择 nums[i]，如果在 [0, i - 1] 这个子区间内已经有一部分元素，使得它们的和为 j ，那么 dp[i][j] = true；
    - 2、选择 nums[i]，如果在 [0, i - 1] 这个子区间内就得找到一部分元素，使得它们的和为 j - nums[i]。

- 状态转移方程是：
dp[i][j] = dp[i - 1][j] or dp[i - 1][j - nums[i]]
- 初始化条件
  - 1、j - nums[i] 作为数组的下标，一定得保证大于等于 0 ，因此 nums[i] <= j；
  - 2、注意到一种非常特殊的情况：j 恰好等于 nums[i]，即单独 nums[j] 这个数恰好等于此时“背包的容积” j，这也是符合题意的。

```javascript
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var canPartition = function(nums) {
    var len = nums.length;
    
    if (len == 0) return false;
    
    var sum = 0;
    for (var i = 0; i < len; i++) {
        sum = sum + nums[i];
    }
    
    if (sum % 2 !== 0) return false;
    
    var target = Math.floor(sum / 2);
    
    var dp = new Array(len).fill(0).map(x => new Array(target + 1).fill(0));
    if (nums[0] < target) dp[0][nums[0]] = true;
    
    for(var i = 1; i < len; i++){
        for(var j = 0; j <= target; j++){
            //先保证和前i-1个数的TF相同，比如前面的i-1个已经为true，那么第i个数就不加上，那么也是true
            //反之，如果i-1是false，那么先让i也是false，看能不能从别的状态转移过来
            dp[i][j] = dp[i-1][j];
            if(nums[i] == j){
                dp[i][j] = true;
                continue;
            }
            if(nums[i] < j){
                //0-1背包问题，从j-nums[i]转移过来
                dp[i][j] = dp[i-1][j] || dp[i-1][j - nums[i]];
            }
        }
    }
    return dp[len - 1][target];
};
```