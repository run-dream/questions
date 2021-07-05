### 基本思路

遍历决策树，dfs。

确定3个问题：

1. 路径： 目前已经做出的选择
2. 选择列表：也就是你当前可以做出的选择
3. 结束条件:   也就是到达决策树的底层，无法继续决策



### 伪代码

```go
result := []string{}
func backtrack(path, options){
    if meetFinishCondition(){
        result = append(result, path)
        return
    }
    for _, opt := range options{
        deal(opt)
        backtrack(path, options)
        revert(opt)
    }
}
```



相关练习题：

https://github.com/run-dream/leetcode-go/tree/master/algorithm/backtracking