# 双指针

## 数组

### [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)

```go
func twoSum(numbers []int, target int) []int {
	j := len(numbers) - 1
	for i := 0; i < len(numbers); i++ {
		for i < j && numbers[i]+numbers[j] > target {
			j--
		}
		if i < j && numbers[i]+numbers[j] == target {
			return []int{i + 1, j + 1}
		}
	}
	return nil
}
```

### [344. 反转字符串](https://leetcode.cn/problems/reverse-string/)

```go
func reverseString(s []byte) {
	i := 0
	j := len(s) - 1
	for i < j {
		t := s[i]
		s[i] = s[j]
		s[j] = t
		i++
		j--
	}
}
```

### [15. 三数之和](https://leetcode.cn/problems/3sum/)

```go
func threeSum(nums []int) [][]int {
	ans := make([][]int, 0)
	sort.Ints(nums)
	for i := 0; i < len(nums); i++ {
		if i > 0 && nums[i] == nums[i-1] {
			continue
		}
		jks := twoSum(nums, i+1, -nums[i])
		for _, jk := range jks {
			ans = append(ans, []int{nums[i], jk[0], jk[1]})
		}
	}
	return ans
}

func twoSum(numbers []int, start, target int) [][]int {
	ans := make([][]int, 0)
	j := len(numbers) - 1
	for i := start; i < len(numbers); i++ {
		if i > start && numbers[i] == numbers[i-1] {
			continue
		}
		for i < j && numbers[i]+numbers[j] > target {
			j--
		}
		if i < j && numbers[i]+numbers[j] == target {
			ans = append(ans, []int{numbers[i], numbers[j]})
		}
	}
	return ans
}
```

### [345. 反转字符串中的元音字母](https://leetcode.cn/problems/reverse-vowels-of-a-string/)

```go
func reverseVowels(s string) string {
	t := []byte(s)
	i := 0
	j := len(s) - 1
	for i < j {
		for i < j && !isVowel(s[i]) {
			i++
		}
		for i < j && !isVowel(s[j]) {
			j--
		}
		t[i], t[j] = t[j], t[i]
		i++
		j--
	}
	return string(t)
}

func isVowel(s byte) bool {
	return strings.Contains("aeiouAEIOU", string(s))
}
```

### [11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

```go
func maxArea(height []int) int {
	ans := 0
	i := 0
	j := len(height) - 1
	for i < j {
		h := min(height[i], height[j])
		w := j - i
		ans = max(ans, h*w)
		if height[i] > height[j] {
			j--
		} else {
			i++
		}
	}
	return ans
}

func max(x, y int) int {
	if x > y {
		return x
	}
	return y
}

func min(x, y int) int {
	if x < y {
		return x
	}
	return y
}
```

### [125. 验证回文串](https://leetcode.cn/problems/valid-palindrome/)

```go
func isPalindrome(s string) bool {
	s = strings.ToLower(s)
	i := 0
	j := len(s) - 1
	for i < j {
		for i < j && !isValid(s[i]) {
			i++
		}
		for i < j && !isValid(s[j]) {
			j--
		}
		if s[i] != s[j] {
			return false
		}
		i++
		j--
	}
	return true
}

func isValid(ch byte) bool {
	return (ch >= 'a' && ch <= 'z') || (ch >= '0' && ch <= '9')
}
```

### [5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

```go
func longestPalindrome(s string) string {
	res := ""
	for i := 0; i < len(s); i++ {
		a := expand(i, i, s)
		b := expand(i, i+1, s)
		res = max(res, a, b)
	}
	return res
}

func expand(left, right int, str string) string {
	for left >= 0 && right < len(str) && str[left] == str[right] {
		left--
		right++
	}
	return str[left+1 : right]
}

func max(a, b, c string) string {
	res := a
	if len(res) < len(b) {
		res = b
	}
	if len(res) < len(c) {
		res = c
	}
	return res
}
```

### [704. 二分查找](https://leetcode.cn/problems/binary-search/)

```go
func search(nums []int, target int) int {
	i, j := 0, len(nums)-1
	for i <= j {
		mid := (i + j) / 2
		if nums[mid] == target {
			return mid
		} else if nums[mid] < target {
			i = mid + 1
		} else {
			j = mid - 1
		}
	}
	return -1
}
```

## 滑动窗口专题

### [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

```go
func minSubArrayLen(target int, nums []int) int {
	left, right := 0, 0
	sum := 0
	res := math.MaxInt
	for right < len(nums) {
		sum += nums[right]
		right++
		for sum >= target {
			res = int(math.Min(float64(res), float64(right-left)))
			sum -= nums[left]
			left++
		}
	}
	if res == math.MaxInt {
		return 0
	}
	return res
}
```

### [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

```go
func lengthOfLongestSubstring(s string) int {
	window := make(map[byte]int)
	left, right := 0, 0
	res := 0
	for right < len(s) {
		r := s[right]
		right++
		window[r]++
		for window[r] > 1 {
			l := s[left]
			if _, ok := window[l]; ok {
				window[l]--
			}
			left++
		}
		res = int(math.Max(float64(res), float64(right-left)))
	}
	return res
}
```

### [438. 找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

```go
func findAnagrams(s string, p string) []int {
	window, need := make(map[byte]int), make(map[byte]int)
	left, right := 0, 0
	res := make([]int, 0)
	match := 0
	for i, _ := range p {
		need[p[i]]++
	}
	for right < len(s) {
		r := s[right]
		right++
		if _, ok := need[r]; ok {
			window[r]++
			if window[r] == need[r] {
				match++
			}
		}
		for match == len(need) {
			if right-left == len(p) {
				res = append(res, left)
			}
			l := s[left]
			left++
			if _, ok := need[l]; ok {
				if window[l] == need[l] {
					match--
				}
				window[l]--
			}
		}
	}
	return res
}
```

### [567. 字符串的排列](https://leetcode.cn/problems/permutation-in-string/)

```go
func checkInclusion(s1 string, s2 string) bool {
	window, need := make(map[byte]int), make(map[byte]int)
	left, right := 0, 0
	match := 0
	for i, _ := range s1 {
		need[s1[i]]++
	}
	for right < len(s2) {
		r := s2[right]
		right++
		if _, ok := need[r]; ok {
			window[r]++
			if window[r] == need[r] {
				match++
			}
		}
		for match == len(need) {
			if right-left == len(s1) {
				return true
			}
			l := s2[left]
			left++
			if _, ok := need[l]; ok {
				if window[l] == need[l] {
					match--
				}
				window[l]--
			}
		}
	}
	return false
}
```

### [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

```go
func minWindow(s string, t string) string {
	need, window := make(map[byte]int), make(map[byte]int)
	left, right := 0, 0
	match := 0
	res := ""
	for i := 0; i < len(t); i++ {
		need[t[i]]++
	}
	for right < len(s) {
		r := s[right]
		right++
		//	对右侧指针所对应的字符，做处理
		if _, ok := need[r]; ok {
			window[r]++
			if window[r] == need[r] {
				match++
			}
		}
		//	window需要缩小范围
		for match == len(need) {
			// 更新匹配的最小子串长度
			if res == "" || right-left < len(res) {
          res = s[left:right]
      }
			l := s[left]
			left++
			if _, ok := need[l]; ok {
				if window[l] == need[l] {
					match--
				}
				window[l]--
			}
		}
	}
	return res
}
// 1. 不满足输出条件，延长窗口
// 2. 满足输出条件，缩短窗口

// Q：输出条件是什么？
// A：覆盖子串 => map中的key/value对应上 => match记录key满足条件的个数
```

### [424. 替换后的最长重复字符](https://leetcode.cn/problems/longest-repeating-character-replacement/)

```go
func characterReplacement(s string, k int) int {
	window, count := make(map[byte]int), 0
	left, right := 0, 0
	res := 0
	for right < len(s) {
		r := s[right]
		right++
		window[r]++
		count = int(math.Max(float64(count), float64(window[r])))
		if right-left > count+k {
			l := s[left]
			if _, ok := window[l]; ok {
				window[l]--
			}
			left++
		}
		res = int(math.Max(float64(res), float64(right-left)))
	}
	return res
}
```

## 子区间计数问题

### [795. 区间子数组个数](https://leetcode.cn/problems/number-of-subarrays-with-bounded-maximum/)

```go
func numSubarrayBoundedMax(nums []int, left int, right int) int {
	// 前缀和+滑动窗口思想
	return lessEqualsThan(nums, right) - lessEqualsThan(nums, left-1)
}

func lessEqualsThan(nums []int, k int) int {
	res := 0
	for left, right := 0, 0; right < len(nums); right++ {
		if nums[right] > k {
			left = right + 1
		}
		// 右边界固定，连续区间的子区间个数 = right - left
		res += right - left
	}
	return res
}
```

### [992. K 个不同整数的子数组](https://leetcode.cn/problems/subarrays-with-k-different-integers/)

```go
func subarraysWithKDistinct(nums []int, k int) int {
	return atMostKDistinct(nums, k) - atMostKDistinct(nums, k-1)
}

func atMostKDistinct(nums []int, k int) int {
	window := make(map[int]int)
	left, right := 0, 0
	count, res := 0, 0
	for right < len(nums) {
		r := nums[right]
		right++
		window[r]++
		if window[r] == 1 {
			count++
		}
		for count > k {
			l := nums[left]
			left++
			window[l]--
			if window[l] == 0 {
				count--
			}
		}
		res += right - left
	}
	return res
}

// 1. 前缀和思想，计算子数组个数
// 2. 满足条件：至多有K个不同整数 - 至多K-1个不同整数，同样是前缀和思想
```

### [713. 乘积小于 K 的子数组](https://leetcode.cn/problems/subarray-product-less-than-k/)

```go
func numSubarrayProductLessThanK(nums []int, k int) int {
	left, right := 0, 0
	product, res := 1, 0
	for right < len(nums) {
		r := nums[right]
		right++
		product *= r
		for left < right && product >= k {
			l := nums[left]
			left++
			product /= l
		}
		res += right - left
	}
	return res
}
```

## 维护滑动窗口特性

### [239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

```go
func maxSlidingWindow(nums []int, k int) []int {
	queue, res := make([]int, 0), make([]int, 0)
	for right := 0; right < len(nums); right++ {
		for len(queue) > 0 && nums[right] > nums[queue[len(queue)-1]] {
			queue = queue[:len(queue)-1]
		}
		queue = append(queue, right)
		left := right - k + 1
		if left >= 0 {
			if queue[0] < left {
				queue = queue[1:]
			}
			res = append(res, nums[queue[0]])
		}
	}
	return res
}
```