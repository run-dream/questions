### 基本思路

在一个有向图中找到起点到终点的最近距离。



### 伪代码

```go
func bfs(start, target *Node) int {
    queue := make([]*Node)
    visited:= make(map[*Node]bool)
    
    queue = append(queue, start);
    visited[start] = true
    step := 0
    
    for len(queue) > 0 {
        size := len(queue)
        
        for i := 0 ;i < size; i++ {
            cur := queue[i]
            if cur === target{
                return step;
            }
            for x := range cur.adj() {
                if !visited[x] {
                    queue = append(queue, x)
                    visited[x] = true
                }
            }
        }
        
        step ++
        queue = queue[size:]
    }
    
    return step
}

```

