### 基本思路

1. 暴力求解，看是否满足
   - 最优子结构
   - 无后效性
   - 重复子问题 
2. 如果满足的话，按照以下步骤找出动态规划方程
   - 定义状态，即原问题和子问题中变化的变量
   - 确定dp函数的定义
   - 确定选择并择优，也就是前面的状态是如何变化的
   - 明确基准



### 伪代码

```go
func dynamicPlan(n int) int {
    if n < 2 {
        return 1 
    }
    dp := make([]int, n)
    dp[0] = 1
    dp[1] = 1
    for i := 2; i < n; i++{
        dp[i] = dp[i-1] + dp[i-2]
    }
    return dp[n-1]
}
```



### 相关练习题

https://github.com/run-dream/leetcode-go/tree/master/algorithm/dynamic-plan





### 常见题型

- 股票买卖

  定义状态: 第i天，还能买卖j次，当天是否持有股票 k = [0,1]

  选择: 买入 卖出 维持现状

  ```go
  // 基准情况
  dp[0][k][0] = 0 // 第0天 没有收益
  dp[0][k][1] = -1<<31 // 第0天不可能持有股票
  dp[i][0][0] = 0 // 不允许交易就没有收益
  dp[i][0][-1] = -1<<31 //不允许交易不可能持有股票
  
  for i := 1; i <= n; i++ {
      for j := k; k >= 1; k-- {
          // 状态转移方程
          dp[i][k][0] = max(
              dp[i-1][k][0], // 前一天没有持有股票
              dp[i-1][k][1] + prices[i] // 前一天持有股票 当天卖出
          ) 
  
          dp[i][k][1] = max(
              dp[i-1][k][1], // 前一天持有股票，且不操作
              dp[i-1][k-1][0] - prices[i]， // 前一天没有股票 当天买入
          )
      }
  }
  
  
  return max(dp[n-1][1...k][0])
  ```

- houseRobber

  定义状态: 从第i个房间开始抢劫的最大收益 dp[i] 

  选择: 抢 或者不抢

  - 数组

  ```go
  func houseRobber(nums []int) int {
      if len(nums) == 0 {
          return 0
      }
      if len(nums) == 1 {
          return nums[0]
      }
      dp := make([]int, len(nums))
      dp[len(nums)-1] = nums[len(nums)-1]
      for i := len(nums) - 2; i >= 0; i -- {
          dp[i] = max(dp[i+2] + nums[i], dp[i+1]);
      }
      return dp[0]
  }
  ```

  - 成环 等于说添加了限制条件 求max(dp[0,len-2], dp[1, len-1])

    ```go
    func houseRobberII(nums []int) int {
        if len(nums) == 0 {
            return 0
        }
        if len(nums) == 1 {
            return nums[0]
        }
        return max(helper(nums, 0, len(nums)-1), helper(nums, 1, len(nums)))
    }
    
    func helper(nums []int, start, end int) int {
        nums = nums[start:end]
        if len(nums) == 0 {
            return 0
        }
        if len(nums) == 1 {
            return nums[0]
        }
        dp := make([]int, len(nums))
        dp[len(nums)-1] = nums[len(nums)-1]
        for i := len(nums) - 2; i >= 0; i -- {
            dp[i] = max(dp[i+2] + nums[i], dp[i+1]);
        }
        return dp[0]
    }
    ```

  - 成树

    ```go
    func houseRobberIII(root *TreeNode, memo map[*TreeNode]int) int {
        if root == nil {
            return 0
        }
        if _, ok := memo[root]; ok {
            return memo[root]
        }
        val := root.Val
        if root.Left != nil {
            val += houseRobberIII(root.Left.Left, memo) + houseRobber(root.Left.Right, memo)
        }
        if root.Right != nil {
            val += houseRobberIII(root.Right.Left, memo) + houseRobber(root.Right.Right, memo)
        }
        notRobVal := 0
        if root.Left != nil {
            notRobVal += houseRobberIII(root.Left, memo)
        }
        if root.Right != nil {
            notRobVal += houseRobberIII(root.Right, memo)
        }
        result := max(val, notRobVal)
        memo[root] = result
        return result
    }
    ```

- twoSum