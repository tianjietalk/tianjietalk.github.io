# 排序

## [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

```go
func findKthLargest(nums []int, k int) int {
    // rand.Seed(time.Now().UnixNano())
    return quickSelect(nums, 0, len(nums)-1, len(nums)-k)
}

func quickSelect(a []int, l, r, index int) int {
    q := partition(a, l, r)
    if q == index {
        return a[q]
    } else if q < index {
        return quickSelect(a, q + 1, r, index)
    }
    return quickSelect(a, l, q - 1, index)
}

func partition(nums []int, l, r int) int {
    pivot := nums[r]
    i, j := l, l
    for j < r {
        if nums[j] < pivot {
            nums[i], nums[j] = nums[j], nums[i]
            i++
        }
        j++
    }
    nums[i], nums[r] = nums[r], nums[i]
    return i
}
```

## [56. 合并区间](https://leetcode.cn/problems/merge-intervals/)

```go
func merge(intervals [][]int) [][]int {
	sort.Slice(intervals, func(i, j int) bool {
		return intervals[i][0] < intervals[j][0]
	})
	res := make([][]int, 0)
	for _, v := range intervals {
		if len(res) == 0 || v[0] > res[len(res)-1][1] {
			res = append(res, v)
		} else {
			res[len(res)-1][1] = max(res[len(res)-1][1], v[1])
		}
	}
	return res
}
```

## [493. 翻转对](https://leetcode.cn/problems/reverse-pairs/)

- 特定大小关系的 pair 可以用归并
- i< j && nums[i] > nums[j] → 逆序对，排序就是一个消除逆序对的过程
- 3,4,8,1,2,3

```go
var ans int

func reversePairs(nums []int) int {
	ans = 0
	mergeSort(nums, 0, len(nums)-1)
	return ans
}

func mergeSort(nums []int, l, r int) {
	if l >= r {
		return
	}
	mid := (l + r) / 2
	mergeSort(nums, l, mid)
	mergeSort(nums, mid+1, r)
	merge(nums, l, mid, r)
}

func merge(nums []int, l, m, r int) {
	left := append([]int(nil), nums[l:m+1]...)    // [l,m]
	right := append([]int(nil), nums[m+1:r+1]...) //[m+1,r]
	for i, j := 0, 0; i < len(left); i++ {
		for j < len(right) && left[i] > right[j]*2 {
			j++
		}
		ans += j
	}
	left = append(left, math.MaxInt)
	right = append(right, math.MaxInt)
	i, j := 0, 0
	for k := l; k <= r; k++ {
		if left[i] < right[j] {
			nums[k] = left[i]
			i++
		} else {
			nums[k] = right[j]
			j++
		}
	}
}
```

## [1122. 数组的相对排序](https://leetcode.cn/problems/relative-sort-array/)

```go
func relativeSortArray(arr1 []int, arr2 []int) []int {
	upper := 0
	for _, v := range arr1 {
		if v > upper {
			upper = v
		}
	}
	freq := make([]int, upper+1)
	for _, v := range arr1 {
		freq[v]++
	}
	ans := make([]int, 0)
	for _, v := range arr2 {
		for freq[v] > 0 {
			ans = append(ans, v)
			freq[v]--
		}
	}
	for i := range freq {
		for freq[i] > 0 {
			ans = append(ans, i)
			freq[i]--
		}
	}
	return ans
}
```