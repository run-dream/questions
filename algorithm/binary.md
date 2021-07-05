### 基本思路

利用数组有序的条件，用二分的思路来处理查找。

注意搜索区间的开闭性

### 伪代码

```go
func binarySearch(array []int, target int) int {
    low, high := 0, len(array) - 1
    // 封闭的区间 [low, high]
    for low <= high {
        mid := low + (high - low ) / 2
        if target == array[mid] {
            return target
        }
        if target < array[mid] {
            high = mid - 1
        }else {
            low = mid + 1
        }
    }
    return -1
}
```



### 相关练习题

https://github.com/run-dream/leetcode-go/tree/master/algorithm/binary-search