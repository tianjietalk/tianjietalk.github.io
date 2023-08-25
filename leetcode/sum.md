# 前缀和 & 差分

## [560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

```go
func subarraySum(nums []int, k int) int {
	ans := 0
	sum := make([]int, len(nums)+1)
	sum[0] = 0
	for i := 0; i < len(nums); i++ {
		sum[i+1] = sum[i] + nums[i]
	}
	i := 0
	j := len(sum) - 1
	for i < j {
		if sum[j]-sum[i] < k {
			j--
		}
		if sum[j]-sum[i] == k {
			ans++
		}
		if sum[j]-sum[i] > k {
			i++
		}
	}
	return ans
}
```

## [1248. 统计「优美子数组」](https://leetcode.cn/problems/count-number-of-nice-subarrays/)

```go
func numberOfSubarrays(nums []int, k int) int {
	ans := 0
	sum := make([]int, len(nums)+1)
	sum[0] = 0
	for i, num := range nums {
		sum[i+1] = sum[i] + (num % 2)
	}
	count := make([]int, len(sum))
	for _, s := range sum {
		if s-k >= 0 {
			ans += count[s-k]
		}
		count[s]++
	}
	fmt.Println(sum)
	fmt.Println(count)
	return ans
}
```

## [53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

```go
func maxSubArray(nums []int) int {
	ans := nums[0]
	n := len(nums)
	sum := make([]int, n+1)
	sum[0] = 0
	for i, num := range nums {
		sum[i+1] = sum[i] + num
	}
	preMin := make([]int, n+1)
	preMin[0] = sum[0]
	for i := 1; i < n+1; i++ {
		preMin[i] = min(preMin[i-1], sum[i])
	}

	for i := 1; i < n+1; i++ {
// 在i之前，找到 0~i-1 之间，s[i]-s[j]最大
		ans = max(ans, sum[i]-preMin[i-1])
	}
	return ans
}

func min(x, y int) int {
	if x > y {
		return y
	}
	return x
}

func max(x, y int) int {
	if x > y {
		return x
	}
	return y
}
```

## [1109. 航班预订统计](https://leetcode.cn/problems/corporate-flight-bookings/)

```go
func corpFlightBookings(bookings [][]int, n int) []int {
	diff := make([]int, n+1)
	for _, booking := range bookings {
		first := booking[0]
		last := booking[1]
		seats := booking[2]
		diff[first-1] += seats
		diff[last] -= seats
	}
	sum := make([]int, n+1)
	for i := 1; i < n+1; i++ {
		sum[i] = sum[i-1] + diff[i-1]
	}
	return sum[1:]
}

// 差分的特点：差分数组的前缀和 = 原数组
```