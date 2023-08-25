# 递归入门

## 78. 子集

```go
var ans [][]int

func subsets(nums []int) [][]int {
	ans = [][]int{}
	recur(0, nums, []int{})
	return ans
}

func recur(i int, nums []int, chosen []int) {
	if i == len(nums) {
		ans = append(ans, append([]int(nil), chosen...))
		return
	}
	chosen = append(chosen, nums[i])
	recur(i+1, nums, chosen)
	chosen = chosen[:len(chosen)-1]
	recur(i+1, nums, chosen)
}
```

## 77.  组合

```go
var ans [][]int

func combine(n int, k int) [][]int {
	ans = [][]int{}
	recur(1, n, k, []int{})
	return ans
}
func recur(i, n, k int, chosen []int) {
	if len(chosen) == k {
		ans = append(ans, append([]int(nil), chosen...))
		return
	}
	if i == n+1 {
		return
	}
	chosen = append(chosen, i)
	recur(i+1, n, k, chosen)
	chosen = chosen[:len(chosen)-1]
	recur(i+1, n, k, chosen)
}
```

## 46. 全排列

```go
var ans [][]int

func permute(nums []int) [][]int {
	ans = [][]int{}
	used := make([]bool, len(nums))
	recur(nums, []int{}, used)
	return ans
}

func recur(nums, chosen []int, used []bool) {
	if len(chosen) == len(nums) {
		ans = append(ans, append([]int(nil), chosen...))
		return
	}
	for i := 0; i < len(nums); i++ {
		if !used[i] {
			chosen = append(chosen, nums[i])
			used[i] = true
			recur(nums, chosen, used)
			chosen = chosen[:len(chosen)-1]
			used[i] = false
		}
	}
}
```

## 47. 全排列 II

```go
var ans [][]int

func permuteUnique(nums []int) [][]int {
	ans = [][]int{}
	used := make([]bool, len(nums))
	sort.Ints(nums)
	recur(nums, []int{}, used)
	return ans
}

func recur(nums, chosen []int, used []bool) {
	if len(chosen) == len(nums) {
		ans = append(ans, append([]int(nil), chosen...))
		return
	}
	for i := 0; i < len(nums); i++ {
		if used[i] || i > 0 && nums[i] == nums[i-1] && !used[i-1] {
			continue
		}
		chosen = append(chosen, nums[i])
		used[i] = true
		recur(nums, chosen, used)
		chosen = chosen[:len(chosen)-1]
		used[i] = false

	}
}
```

## 226. 翻转二叉树

```go
func invertTree(root *TreeNode) *TreeNode {
	if root == nil {
		return nil
	}
	left := root.Left
	right := root.Right
	root.Left = invertTree(right)
	root.Right = invertTree(left)
	return root
}
```

## 98. 验证二叉树

分治思想：拆分任务，原问题分解为多个子问题，递归求解子问题，合并子问题的结果

```go
func isValidBST(root *TreeNode) bool {
    return recursive(root, math.MaxInt, math.MinInt)
}

func recursive(node *TreeNode, max, min int) bool {
    if node == nil {
        return true
    }
    if node.Val > max || node.Val < min {
        return false
    }
    return recursive(node.Left, node.Val-1, min) && recursive(node.Right, max, node.Val + 1)
}
```

## 104. 二叉树的最大深度

```go
func maxDepth(root *TreeNode) int {
	if root == nil {
		return 0
	}
	leftMax := maxDepth(root.Left)
	rightMax := maxDepth(root.Right)
	return int(math.Max(float64(leftMax), float64(rightMax))) + 1
}
```

## 111. 二叉树的最小深度

```go
func minDepth(root *TreeNode) int {
	if root == nil {
		return 0
	}
	left := minDepth(root.Left)
	right := minDepth(root.Right)
	if left == 0 || right == 0 {
		return left + right + 1
	}
	return int(math.Min(float64(left), float64(right))) + 1
}
```

## 50. Pow(x, n)

```go
func myPow(x float64, n int) float64 {
	if n == 0 {
		return 1
	}
	if n < 0 {
		n = -n
		x = 1 / x
	}
	if n == 1 {
		return x
	}
	if n%2 == 0 {
		return myPow(x*x, n/2)
	}
	return x * myPow(x*x, n/2)
}
```

## 22. 生成括号

```go
var res []string

func generateParenthesis(n int) []string {
	res = make([]string, 0)
	recursive("", 0, 0, n)
	return res
}
func recursive(str string, l, r, n int) {
	if l == n && r == n {
		res = append(res, str)
	}
	if l < n {
		recursive(str+"(", l+1, r, n)
	}
	if r < l {
		recursive(str+")", l, r+1, n)
	}
}
```

## 23. Merge K Sorted Lists

```go
	func mergeKLists(lists []*ListNode) *ListNode {
	if len(lists) == 0 {
		return nil
	}
	k := len(lists)
	if k == 1 {
		return lists[0]
	}
	l1 := mergeKLists(lists[:k/2])
	l2 := mergeKLists(lists[k/2:])
	return mergeTwoList(l1, l2)
}

func mergeTwoList(l1, l2 *ListNode) *ListNode {
	if l1 == nil {
		return l2
	}
	if l2 == nil {
		return l1
	}
	if l1.Val <= l2.Val {
		l1.Next = mergeTwoList(l1.Next, l2)
		return l1
	} else {
		l2.Next = mergeTwoList(l1, l2.Next)
		return l2
	}
}
```