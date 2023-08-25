# 栈 & 单调栈

## [有效的括号](https://leetcode.cn/problems/valid-parentheses/)

```go
func isValid(s string) bool {
    pair := map[byte]byte{
        ')': '(',
        ']': '[',
        '}': '{',
    }
    stack := []byte{}
    for i := 0; i < len(s); i++ {
        if _, ok := pair[s[i]]; ok {
            if len(stack) == 0 || stack[len(stack) - 1] != pair[s[i]] {
                return false
            }
            stack = stack[:len(stack) - 1]
        } else {
            stack = append(stack, s[i])
        }
    }
    return len(stack) == 0
}
// 左括号入栈，右括号与栈顶元素匹配，成功 => 弹出，失败 => 返回 false
// 扫描结束，栈内元素为空 => true，不为空 => false
```

## [逆波兰表达式求值](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)

```go
func evalRPN(tokens []string) int {
    stack := []int{}
    for _, s := range tokens {
        val, err := strconv.Atoi(s)
        if err == nil {
            stack = append(stack, val)
        } else {
            y := stack[len(stack) - 1]
            stack = stack[:len(stack) - 1]
            x := stack[len(stack) - 1]
            stack = stack[:len(stack) - 1]
            z := cal(x, y, s)
            stack = append(stack, z)
        }
    }
    return stack[len(stack) - 1]
}

func cal(x, y int, s string) int {
    if s == "+" {
        return x + y
    }
    if s == "-" {
        return x - y
    }
    if s == "*" {
        return x * y
    }
    if s == "/" {
        return x / y
    }
    return 0
}

// 最近相关性
```

## [基本计算器 II](https://leetcode.cn/problems/basic-calculator-ii/)

```go
func calculate(s string) int {
    s += " "
    tokens := []string{}
    ops := []rune{}
    number := ""
    for _, char := range s {
        if (char >= '0' && char <= '9') {
            number += string(char)
            continue
        } else {
            if number != "" {
                tokens = append(tokens, number)
                number = ""
            }
        }
        if char == ' ' {
            continue
        }
        curRank := getRank(char)
        for (len(ops) > 0 && getRank(ops[len(ops) - 1]) >= curRank) {
            tokens = append(tokens, string(ops[len(ops) - 1]))
            ops = ops[:len(ops) - 1]   
        }
        ops = append(ops, char)
    }
    for (len(ops) > 0) {
        tokens = append(tokens, string(ops[len(ops) - 1]))
        ops = ops[:len(ops) - 1]   
    }
    return evalRPN(tokens)
}

func getRank(char rune) int {
    if (char == '+' || char == '-') {
        return 1
    }
    if (char == '*' || char == '/') {
        return 2
    }
    return 0
}

func evalRPN(tokens []string) int {
    stack := []int{}
    for _, token := range tokens {
        val, err := strconv.Atoi(token)
        if err == nil {
            stack = append(stack, val)
        } else {
            y := stack[len(stack) - 1]
            stack = stack[:len(stack) - 1]
            x := stack[len(stack) - 1]
            stack = stack[:len(stack) - 1]
            z := cal(x, y, token)
            stack = append(stack, z)
        }
    }
    return stack[len(stack) - 1]
}

func cal(x, y int, op string) int {
    if op == "+" {
        return x + y
    }
    if op == "-" {
        return x - y
    }
    if op == "*" {
        return x * y
    }
    if op == "/" {
        return x / y
    }
    return 0
}
```

## [每日温度](https://leetcode.cn/problems/daily-temperatures/)

```go
func dailyTemperatures(temperatures []int) []int {
    ans := make([]int, len(temperatures))
    stack := make([]int, 0)
    for i, t := range temperatures {
        // 当前元素比栈顶要大，更新结果，再去弹出栈顶元素
        for len(stack) > 0 && t > temperatures[stack[len(stack)-1]] {
            topIndex := stack[len(stack)-1]
            ans[topIndex] = i - topIndex
            stack = stack[:len(stack) - 1]
        }
        // 当前元素比栈顶要小，直接入栈
        stack = append(stack, i)
    }
    return ans
}

// 遍历过程中，找到右侧第一个比自己大的元素的下标
// 暴力解法：O(N^2)
// 降维：空间换时间
// 数组，从左往右遍历，记录第一个比自己大的元素，找到后更新result，就可以把自己从数组中删去
// 栈来解决，单调栈

// i
// 73,74,75,71,69,72,76,73
// 71,69,
// 1,1,4,2,1,1,0,0
```

## [下一个更大元素 I](https://leetcode.cn/problems/next-greater-element-i/)

```go
func nextGreaterElement(nums1 []int, nums2 []int) []int {
	hashmap := map[int]int{}
	res := make([]int, len(nums1))
	stack := make([]int, 0)
	for i, v := range nums2 {
		// 找到比自己大的元素，就可以弹出
		for len(stack) > 0 && v > nums2[stack[len(stack)-1]] {
			hashmap[nums2[stack[len(stack)-1]]] = v
			stack = stack[:len(stack)-1]
		}
		// 没有比自己大的，就放到栈中。把什么元素存到栈中，是看最终结果需要什么
		stack = append(stack, i)
	}
	for i := range nums1 {
		if val, ok := hashmap[nums1[i]]; ok {
			res[i] = val
		} else {
			res[i] = -1
		}
	}
	return res
}
```

## [下一个更大元素 II](https://leetcode.cn/problems/next-greater-element-ii/)

```go
func nextGreaterElements(nums []int) []int {
	res := make([]int, len(nums))
	for i := range res {
		res[i] = -1
	}
	stack := make([]int, 0)
	for i := 0; i < len(nums)*2; i++ {
		index := i % len(nums)
		// 找到比自己大的元素，就可以弹出
		for len(stack) > 0 && nums[index] > nums[stack[len(stack)-1]] {
			res[stack[len(stack)-1]] = nums[index]
			stack = stack[:len(stack)-1]
		}
		// 没有比自己大的，就放到栈中。把什么元素存到栈中，是看最终结果需要什么
		stack = append(stack, index)
	}
	return res
}
```

## [柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

```go
func largestRectangleArea(heights []int) int {
	newHeights := make([]int, len(heights)+2)
	for i := 0; i < len(heights); i++ {
		newHeights[i+1] = heights[i]
	}
	res := 0
	stack := make([]int, 0)
	for i, height := range newHeights {
		for len(stack) > 0 && height < newHeights[stack[len(stack)-1]] {
			mid := stack[len(stack)-1]
			stack = stack[:len(stack)-1]
			if len(stack) > 0 {
				h := newHeights[mid]
				w := i - stack[len(stack)-1] - 1
				res = max(res, h*w)
			}
		}
		stack = append(stack, i)
	}
	return res
}

func max(x, y int) int {
	if x > y {
		return x
	}
	return y
}

// 找到比自身柱子低的左右柱子，面积=自身高度*左右柱子的距离
// 用单调递增的栈，左右柱子就方便找到
```

## [接雨水](https://leetcode.cn/problems/trapping-rain-water/)

```go
func trap(height []int) int {
	sum := 0
	stack := make([]int, 0)
	for i, h := range height {
		//	遇到比栈顶元素高的，就弹出
		for len(stack) > 0 && h > stack[len(stack)-1] {
			mid := stack[len(stack)-1]
			stack = stack[:len(stack)-1]
			if len(stack) > 0 {
				h := min(height[stack[len(stack)-1]], h) - height[mid]
				w := i - stack[len(stack)-1] - 1
				sum += h * w
			}
		}
		//	比自己小，直接入栈
		stack = append(stack, i)
	}
	return sum
}

func min(x, y int) int {
	if x > y {
		return y
	}
	return x
}

// 相对于自身，找左右比自己高的柱子
// 面积=矮柱子的高度*左右柱子的距离
```