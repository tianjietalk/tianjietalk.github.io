# 数组基础题


## [两数之和](http://leetcode-cn.com/problems/two-sum)

```go
func twoSum(nums []int, target int) []int {
	res := make([]int, 0)
	sumMap := make(map[int]int, 0) // key: nums[i], value: i
	for i, num := range nums {
		if j, ok := sumMap[target-num]; ok {
			res = append(res, i, j)
		} else {
			sumMap[num] = i
		}
	}
	return res
}
```

## 删除有序数组中的重复项

```go
func removeDuplicates(nums []int) int {
    var n = 0
    for i, num := range nums {
        if (i == 0 || nums[i] != nums[i - 1]) {
            nums[n] = num
            n += 1
        }
    }
    return n
}
```

## 移动零

```go
func moveZeroes(nums []int)  {
    var n = 0
    for _, num := range nums {
        if (num != 0) {
            nums[n] = num
            n += 1
        }
    }
    for n < len(nums) {
        nums[n] = 0
        n += 1
    }
}
```

## 合并两个有序数组

```go
func merge(nums1 []int, m int, nums2 []int, n int)  {
    var (
        i = 0
        j = 0
    )
    var arr []int
    for (i < m || j < n) {
        if (j >= n || (i < m && nums1[i] <= nums2[j])) {
            arr = append(arr, nums1[i])
            i += 1
        } else {
            arr = append(arr, nums2[j])
            j += 1
        }
    }
    for i, num := range arr {
        nums1[i] = num
    }
}
```