# 二分查找，一招搞定

> 二分查找，好几个模板怎么记？分别解决什么问题？细节多记不住？
> 

> 理解本质后，其实只有一个模板 🤩
> 

# 二分思想

本质上是折半查找思想，**每一轮查找的范围是上一轮的一半**，每次查找比较中间元素的值和目标值的大小，比较结论**决定取哪一半边**作为下一轮的查找范围。当查找范围**缩小到空**时停止。

## 优点：速度极快 O(logn)、不需要额外空间

- 速度快：在 2 的 36 次方这个极大数量级的数组中，找到某个元素，最多只需要比较 36 次。

```jsx
n, n/2, n/4, n/8, ..., n/2^k => n/2^k=1 => k=log2n => O(logn)
```

- 不需要额外空间：对比二叉树查找，需要额外空间存储二叉树。

## 局限性：有序、数组

- 有序性：保证每次取半的意义
- 数组：数组寻址的复杂度是 O(1)
    - 如果是用链表存储的一串数，二分查找是无意义的。链表的寻址是 O(n)。

# 简单二分

数组中**不包含重复元素**，重点关注每次判边的逻辑。

## 标准模板

```go
func search(nums []int, target int) int {
	left, right := 0, len(nums)-1
  // 1. 退出条件，left == right 是闭区间，还有一个元素，需要和 target 比较
	for left <= right {
    // 2. mid 取值，防止溢出
		mid := left + (right-left)/2
		if nums[mid] == target {
			return mid
		} else if nums[mid] < target {
			// 3. left right 的更新，防止陷入死循环
			left = mid + 1
		} else {
			right = mid - 1
		}
	}
	return -1
}
```

## 实战题

### 🌟 ****704. 二分查找****

```go
func search(nums []int, target int) int {
	left, right := 0, len(nums)-1
	for left <= right { // 1. 退出条件
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

### ****74. 搜索二维矩阵****

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

### ****374. 猜数字大小****

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

### 🌟 162. 寻找峰值

```go
// 1,2,1,3,5,6,4
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

		// 需要判断峰值在左侧还是右侧
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

# 简单二分 + 循环数组

循环数组：4, 5, 6, 7,    8, 0, 1, 2 ⇒ target = 5,    target = 1

特点：一分为二后，一侧是有序数组，另一侧是循环数组。

根据这个特点，先判断有序数组、循环数组分别在哪一侧，再判断 target 在有序数组 or 循环数组，判断方法是让 target 和有序数组的首尾做比较，看是否在有序数组中。

## 实战题

### 🌟 33. 搜索旋转排序数组

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

### ****153. 寻找旋转排序数组中的最小值****

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

# 二分变形：存在重复元素

## 找 first / last 等于 target 的元素

数组：1, 2, 3, 3, 7, 8, 8, 9

```go
func binarySearch(nums []int, target int) int {
	left, right := 0, len(nums)-1
	for left <= right {
		mid := left + (right-left)/2
		if nums[mid] < target {
			left = mid + 1
		} else if nums[mid] > target {
			right = mid - 1
		} else {
			// 1,3,8,8,8,8,9
			// nums[mid] 等于 target，先确定是否为 first，则往前检查一个元素是否等于 target
			if mid == 0 || nums[mid-1] != target {
				return mid
			} else {
				right = mid - 1
			}
			// if mid == len(nums)-1 || nums[mid+1] != target {
			// 	return mid
			// } else {
			// 	left = mid + 1
			// }
		}
	}
	return -1
}
```

## 应用最多：0 - 1 问题

二分查找的应用，最后都是要转换成一个单调非递减的数组。

0 - 1 问题本质是构造单调数组，再找到 first 1 或 last 0。

```go
// 1,2,3,4,5,6
// 0,0,1,1,1,1
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

## 实战题

### 🌟 ****34. 在排序数组中查找元素的第一个和最后一个位置****

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

### **278. 第一个错误的版本**

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

### 🌟 ****69. x 的平方根****

```go
// m*m > x => 1
// 1,2,3,4,5,6,7,8
// 0,0,1,1,1,1,1,1
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

### 🌟 875. 爱吃香蕉的珂珂

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

### ****410. 分割数组的最大值****

### ****1482. 制作 m 束花所需的最少天数****

### ****1011. 在 D 天内送达包裹的能力****

### ****911. 在线选举****