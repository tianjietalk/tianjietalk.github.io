# 二分查找

## 🌟 [704. 二分查找](https://leetcode.cn/problems/binary-search/)

```go
// 举例，猜数字，0-100猜数字
// 每次查找区间为之前的1/2，n,n/2,n/4,n/4 => n/2^k=1 => logn => 高效
// 局限性：有序数组
func search(nums []int, target int) int {
	left, right := 0, len(nums)-1
	for left <= right { // 1. 退出条件
    // [0,1,2], search 2, l=0,m=1,l=m+1=2
    // left == right 是闭区间，还有一个元素
		mid := left + (right-left)/2 // 2. mid 取值
		if nums[mid] == target {
			return mid
		} else if nums[mid] < target {
			left = mid + 1 // 3. left right 的更新
		} else {
			right = mid - 1
		}
	}
	return -1
}
```

## [74. 搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)(和704一起讲)**

```go
func searchMatrix(matrix [][]int, target int) bool {
	for _, arr := range matrix {
		left, right := 0, len(arr)-1
		if arr[left] > target || arr[right] < target {
			continue
		}
		return binarySearch(arr, target)
	}
	return false
}

func binarySearch(nums []int, target int) bool {
	left, right := 0, len(nums)-1
	for left <= right {
		mid := left + (right-left)/2
		if nums[mid] == target {
			return true
		} else if nums[mid] < target {
			left = mid + 1
		} else {
			right = mid - 1
		}
	}
	return false
}
```

## [374. 猜数字大小](https://leetcode.cn/problems/guess-number-higher-or-lower/)

```go
func guessNumber(n int) int {
	left, right := 1, n
	for left <= right {
		mid := left + (right-left)/2
		if guess(mid) == 0 {
			return mid
		} else if guess(mid) == -1 {
			right = mid - 1
		} else {
			left = mid + 1
		}
	}
	return -1
}
```

## 🌟 162. 寻找峰值

```go
func findPeakElement(nums []int) int {
	n := len(nums)
	if n == 1 {
		return 0
	}
	left, right := 0, n-1
	for left <= right {
		mid := left + (right-left)/2

		// 边界判断
		if (mid == n-1 && nums[mid] > nums[mid-1]) || (mid == 0 && nums[mid] > nums[mid+1]) {
			return mid
		}

		// 需要判断在左侧还是右侧
		if nums[mid] < nums[mid+1] {
			// 右侧
			left = mid + 1
		} else if nums[mid] < nums[mid-1] {
			// 左侧
			right = mid - 1
		} else {
			return mid
		}
	}
	return -1
}
```

## [153. 寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

```go
// 4,5,6,7,0,1,2
// 一侧是有序数组，一侧是循环数组，min在循环数组中
func findMin(nums []int) int {
	left, right := 0, len(nums)-1
	for left <= right {
		mid := left + (right-left)/2
		// 判断 min 在左侧还是右侧
		if nums[mid] > nums[right] {
			// 左侧有序，右侧循环
			left = mid + 1
		} else {
			// mid 是 min
			if mid == 0 || nums[mid] < nums[mid-1] {
				return nums[mid]
			} else {
				// mid 不是 min，则 左侧循环，右侧有序
				right = mid - 1
			}
		}
	}
	return -1
}
```

## 🌟 [33. 搜索旋转排序数组](https://leetcode.com/problems/search-in-rotated-sorted-array/description/)

```go
func search(nums []int, target int) int {
	left, right := 0, len(nums)-1
	for left <= right {
		mid := left + (right-left)/2
		// 一分为二后，一侧是有序数组，另一侧是循环数组
		// 4, 5, 6, 7, 0, 1, 2
		if nums[mid] == target {
			return mid
		}
		if nums[mid] > nums[right] {
			// 左侧有序数组，右侧循环数组
			// 确定是在有序数组里，还是在循环数组里
			if nums[left] <= target && nums[mid] > target {
				right = mid - 1
			} else {
				left = mid + 1
			}
		} else {
			// 右侧是有序数组，左侧是循环数组
			if nums[mid] < target && nums[right] >= target {
				left = mid + 1
			} else {
				right = mid - 1
			}
		}
	}
	return -1
}
```

## 🌟 [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)(找 first / last 等于 target 的元素)

```go
func searchRange(nums []int, target int) []int {
	ans := make([]int, 2)
	ans[0] = findFirst(nums, target)
	ans[1] = findLast(nums, target)
	return ans
}

func findFirst(nums []int, target int) int {
	left, right := 0, len(nums)-1
	for left <= right {
		mid := left + (right-left)/2
		if nums[mid] < target {
			left = mid + 1
		} else if nums[mid] > target {
			right = mid - 1
		} else {
			if mid == 0 || nums[mid-1] != target {
				return mid
			} else {
				right = mid - 1
			}
		}
	}
	return -1
}

func findLast(nums []int, target int) int {
	left, right := 0, len(nums)-1
	for left <= right {
		mid := left + (right-left)/2
		if nums[mid] < target {
			left = mid + 1
		} else if nums[mid] > target {
			right = mid - 1
		} else {
			if mid == len(nums)-1 || nums[mid+1] != target {
				return mid
			} else {
				left = mid + 1
			}
		}
	}
	return -1
}
```

## 278. 第一个错误的版本

```go
func firstBadVersion(n int) int {
	left, right := 1, n
	for left <= right {
		mid := left + (right-left)/2
		// find the first 1 => 重复数组找到第一个等于target的index
		// 0,0,1,1,1,1,1
		if isBadVersion(mid) {
			if mid == 1 || !isBadVersion(mid-1) {
				return mid
			} else {
				right = mid - 1
			}
		} else {
			left = mid + 1
		}
	}
	return -1
}
```

## 🌟 [69. x 的平方根](https://leetcode.cn/problems/sqrtx/)

```go
func mySqrt(x int) int {
	if x == 0 {
		return 0
	}
	left, right := 1, x
	for left <= right {
		mid := left + (right-left)/2
		if mid <= x/mid {
			if (mid + 1) > x/(mid+1) {
				return mid
			}
			left = mid + 1
		} else {
			right = mid - 1
		}
	}
	return -1
}
```

## 🌟 875. 爱吃香蕉的珂珂

简单介绍题目含义，速度为k，要在h小时内吃完，共有n堆香蕉。速度*时间≥总和，但另一个条件是，如果 p[i] < k 那么在这个小时内只会消耗 p[i]，让我们求最小的 k。

最慢的速度 1，最快的速度 max，我们发现是在 1-max 间找到一个最小的 k，使其满足消耗的时间 ≤ h。假设我们把满足条件定为1，不满足定为0，就成了0-1数组问题，即在 0,0,0,1,1,1 中找到 first 1。我们构造出了单调数组，所以可以用二分来解决问题。

```go
func minEatingSpeed(piles []int, h int) int {
	max := 1
	for _, p := range piles {
		max = int(math.Max(float64(p), float64(max)))
	}
	left, right := 1, max
	for left <= right {
		mid := left + (right-left)/2
		if canEatAll(piles, mid, h) {
			if mid == 1 || !canEatAll(piles, mid-1, h) {
				return mid
			}
			right = mid - 1
		} else {
			left = mid + 1
		}
	}
	return -1
}

func canEatAll(piles []int, speed int, h int) bool {
	var time int
	for _, p := range piles {
		t := p / speed
		if p%speed != 0 {
			t += 1
		}
		time += t
	}
	return time <= h
}
```

## [410. 分割数组的最大值](https://leetcode.cn/problems/split-array-largest-sum/)

## [154. 寻找旋转排序数组中的最小值 II](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/)

```go
func findMin(nums []int) int {
	n := len(nums)
	left, right := 0, n-1
	for left <= right {
		if left == right {
			break
		}
		mid := left + ((right - left) >> 1)
		// 一个小技巧，不一直和 nums[n-1] 比较
		if nums[mid] > nums[right] {
			left = mid + 1
		} else {
			right--
		}
	}
	return nums[left]
}
```

## [1482. 制作 m 束花所需的最少天数](https://leetcode.cn/problems/minimum-number-of-days-to-make-m-bouquets/)

## [1011. 在 D 天内送达包裹的能力](https://leetcode.cn/problems/capacity-to-ship-packages-within-d-days/)

## [911. 在线选举](https://leetcode.cn/problems/online-election/)