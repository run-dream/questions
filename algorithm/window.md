### 基本思想

维护一个窗口[left, right) 不停滑动，更新答案



### 示例代码

```go
func slideWindow(s, t string) string {
    condition := make(map[byte]int)
    for i := 0; i < len(t); i ++{
        condition[t[i]]++
    }
    toMatch = len(condition)
    window := make(map[byte]int)
    matched := 0
    start := -1
    size := len(s) + 1
    left, right := 0, 0
    for right < len(s) {
        char := s[right]
        right++
        if condition[char] {
            window[char]++
        	if window[char] == condition[char] {
            	matched++
        	}
        }

        for matched == toMatch {
            if size > right - left {
                size = right - left
                start = left
            }
            left++
            char2 := s[left]
            if condition[char2] > 0 {
                if window[char2] == condition[char2]{
                    matched--
                }
                window[char2]--
            }
        }
    }
    if start == -1 {
        return ""
    }
    return s[start, start + size]
} 
```



### 练习题

https://github.com/run-dream/leetcode-go/tree/master/interview/byte-dance/no-repeat-longest-str