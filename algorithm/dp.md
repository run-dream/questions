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

- 背包

  - 0-1背包

    状态: 剩余容量 w，第i个物品（前面的已经做了选择）

    dp函数定义:```dp[i][j]// i 表示选完第i个物品，j表示剩余的容量```

    选择：对于第i个物品是否携带

    dp方程: ```dp[i][j]= max(dp[i-1][j-weight[i]] + values[i], dp[i-1][j])```

    最后去求```dp[n][w]```

    ```go
    func pack(w, n int,weights, values []int){
        dp := make([][]int, n+1)
        for i := 0; i <= n; i++{
            dp[i] = make([]int, w + 1)
        }
        for i := 1; i <= n; i++{
            for j := 1; j <= k; j ++ {
                if weights[i-1] > j {
                    dp[i][j] = dp[i-1][j]
                }else{
                    dp[i][j] = max(dp[i-1][j], dp[i-1][j-weight[i-1]] + values[i])
                }
            }
        }
        return dp[n][w]
    }
    ```

  - 完全背包

    状态：剩余容量w，第i个物品

    dp函数定义: `dp[i][j]` i 表示选完第i个物品，j表示剩余的容量

    选择：是否选择第i个物品

    dp方程

    ```dp[i][j] = sum(dp[i-1][j-values[i]*k] ( k = 0, 1, ...)```

    最后去求`dp[i][w]`

     

    ```go
    func change(amount int, coins []int){
        n := len(coins)
        dp := make([][]int, n+1)
        for i := 0; i < n; i ++{
            dp[i] = make([]int, amount+1)
        }
        for i := 1; i <= n; i++{
            for j := 1; j <= amount; j ++{
                if j - coins[i-1] >= 0 {
                    dp[i][j] = dp[i-1][j] + dp[i][j-coins[i-1]]
                }else {
                    dp[i][j] = dp[i-1][j]
                }
            }
        }
        return dp[n][amount]
    }
    ```

- LCS

  状态: 字符串s,t的长度i,j

  dp函数定义: ```dp[i][j]```表示字符串```s[1:i]```和字符串```t[1:j]```的最长公共子串

  选择: `s[i]` `t[j]`是否会出现在最后的结果里

  动态规划方程:```dp[i][j] = dp[i-1][j-1] + 1 if s[i] == s[j] else max(dp[i-1][j], dp[i][j-1])```

  最后求`dp[len(s)][len(t)]`

  ```go
  func lcs(s t string) int {
      if len(s) == 0 || len(t) == 0 {
          return 0
      }
      dp := make([][]int, len(s) + 1)
      for i := 0; i < len(dp); i ++{
          dp[i] = make([]int, len(t) + 1)
      }
      for i := 1; i <= len(s); i++{
          for j := 1; j <= len(t); j++{
              if s[i-1] == t[j-1]{
                  dp[i][j] = dp[i-1][j-1] + 1
              }else{
                  dp[i][j] = max(dp[i-1][j], dp[i][j-1])
              }
          }
      }
      return dp[len(s)][len(t)]
  }
  ```

- 最短编辑距离

  思路： 用两个指针从后往前遍历，建立状态表

  dp定义: `dp[i][j]` 表示子字符串`s[0:i]`到`t[0:j]`的最小编辑距离

  dp方程:   ```dp[i][j] = dp[i-1][j-1] if s[i] == t[j] else min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) ```

  ```go
  func minEditDistance(s, t string) int {
      if len(s) == 0 || len(t) == 0 {
          return 0
      } 
      dp := make([][]int, len(s) + 1)
      for i := 0; i <= len(s); i ++{
          dp[i] = make([]int, len(t) + 1)
      }
      for i := 1; i <= len(s); i++{
          for j := 1; j <= len(t); j ++{
              if s[i-1] == t[j-1]{
                  dp[i][j] = dp[i-1][j-1] // 跳过
              }else{
                  dp = min(
                      dp[i-1][j], // 删除
                      dp[i][j-1], // 插入
                      dp[i-1][j-1],// 替换
                  ) + 1
              }
          }
      }
      return dp[len(s)][len(t)]
  }
  
  func min(args ...int) int{
      result := args[0]
      for _, val := range args{
          if val > result {
              result = val
          }
      }
      return result
  }
  ```


- LIS 最长递增子序列

  状态： 遍历到第i个字符

  dp函数:`dp[i]` 以第i个字符结束时的LIS长度

  动态规划方程: ```dp[i] = max(dp[i], dp[j] + 1) if 0 =< j < i```

  取最大值

  ```go
  func lcs(nums []int) int {
      size := len(nums)
      if size <= 1{
          return size
      }
      dp := make([]int, size)
      for i := 0; i < size; i++{
          dp[i] = 1
      }
      result := 0
      for i := 0; i < size; i++{
          for j := 0; j < i; j++{
              if nums[j] < nums[i] {
                  dp[i] = max(dp[i], dp[j] + 1)
              }
          }
          result = max(dp[i], result)
      }
      return result
  }
  ```
