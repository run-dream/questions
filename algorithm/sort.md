### 基本思路

先按照起点排序，再观察规律



### 伪代码

```go
import “sort”

func sortGreedy(vals []int) {
    sort.Slice(vals, compareFunc);
    return greedy(vals);
}
```



### 常见体系

- 区间覆盖

  ```go
  import "sort"
  func removeCoveredIntervals(intervals [][]int) int {
      sort.Slice(intervals, func(i, j int)bool {
          if intervals[i][0] == intervals[j][0]{
              return intervals[i][1] > intervals[j][1]
          }
          return intervals[i][0] < intervals[j][0]
      })
      
      left, right := intervals[0][0], intervals[0][1]
      result := 0
      for i := 1; i < len(intervals); i++ {
          interval := intervals[i]
          // 错开
          if right < interval[0]{
              left = interval[0]
              right = interval[1]
          }
          // 覆盖
          if left <= interval[0] && right >= interval[1]{
              result ++
          }
          // 相交
          if right >= interval[0] && right <= interval[1]{
              right = interval[1]
          }
      }
      
      return len(intervals) - result
  }
  ```


- twoSum

  ```
  import "sort"
  
  func twoSum(nums []int, target int) []int {
      sort.Ints(nums);
      left, right := 0, len(nums) - 1
      for left < right {
      	for left > 0 && nums[left] == nums[left-1] && left < right{
              left++
      	}
      	for right < len(num) -1 && nums[right] == nums[right+1] && left < right{
              right--
      	}
          sum := nums[left] + nums[right]
          if sum == target {
              return []int{nums[left], nums[right]}
          }else if sum > target {
              right--
          }else if sum < target{
              left++
          }
      }
      return []int{}
  }
  ```
