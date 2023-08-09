# 双指针、滑动窗口、前缀和，专题总结

# 数组里的双指针

用暴力解法一定可解，双重循环得出结果。用双指针的方法，借助额外空间（一个变量），实现了降维优化。

## 左右指针，交替*相向*而行

两个指针，分别在数组的头和尾，都往中间移动，直到相遇。

题目特点：每个循环，仅看数组中的2个元素，双指针意在找到符合条件的2个元素，求出对应的结果。

### 典型例题：****盛最多水的容器****

![Untitled](assets/206c5caefe2f61469c81befc5a8e4f7854200.png)

```java
// 暴力解法
public class Solution {
    public int maxArea(int[] height) {
        int len = height.length;
        if (len < 2) {
            return 0;
        }
        int res = 0;
        for (int i = 0; i < len - 1; i++) {
            for (int j = i + 1; j < len; j++) {
                res = Math.max(res, Math.min(height[i], height[j]) * (j - i));
            }
        }
        return res;
    }
}
// 双指针解法
public class Solution {
    public int maxArea(int[] height) {
        int len = height.length;
        if (len < 2) {
            return 0;
        }
        int left = 0;
        int right = len - 1;
        int res = 0;
        while (left < right) {
            int minHeight = Math.min(height[left], height[right]);
            res = Math.max(res, minHeight * (right - left));
            if (height[left] == minHeight) {
                left++;
            } else {
                right--;
            }
        }
        return res;
    }
}
```

### 模板

```go
func template(nums[]int) {
		left, right := 0, len(nums)-1
		for left < right {
				if () {
					left++
				}
				if () {
					right--
				}
		}
}
```

### 同类型练习题

****[167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)****

****[15. 三数之和](https://leetcode.cn/problems/3sum/)****

****[344. 反转字符串](https://leetcode.cn/problems/reverse-string/)****

****[345. 反转字符串中的元音字母](https://leetcode.cn/problems/reverse-vowels-of-a-string/)****

****[125. 验证回文串](https://leetcode.cn/problems/valid-palindrome/)****

****🏆 [5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)**

****[704. 二分查找](https://leetcode.cn/problems/binary-search/)****

## 快慢指针，交替*同向*而行：滑动窗口

两个指针，都位于数组的头部，快指针走到尾部则循环结束，慢指针视条件移动。

题目特点：每个循环，看子区间是否满足某个条件，子区间是由双指针框起来，输出的是子区间的变形。

### 典型例题：****最小覆盖子串****

![Untitled](assets/206c5caefe2f61469c81befc5a8e4f7854201.png)

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
// 1. 不满足输出条件，延长窗口
		r := s[right]
		right++
		//	对右侧指针所对应的字符，做处理
		if _, ok := need[r]; ok {
			window[r]++
			if window[r] == need[r] {
				match++
			}
		}
// 2. 满足输出条件，缩短窗口
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
```

### 模板

```go
left, right := 0, 0
for right < len(s) {
		// 增大窗口范围
		window.add(s[right])
		right++;
		// 满足某个条件，需要缩小窗口范围
    for () {
				window.remove(s[left])
				left++;
		}
}
```

### 同类型练习题

- 找到符合条件的子区间
    
    ****[209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)****
    
    ****[3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)****
    
    ****[438. 找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)****
    
    ****[567. 字符串的排列](https://leetcode.cn/problems/permutation-in-string/)****
    
    ****[424. 替换后的最长重复字符](https://leetcode.cn/problems/longest-repeating-character-replacement/)****
    
- 子区间计数问题
    
    ****[795. 区间子数组个数](https://leetcode.cn/problems/number-of-subarrays-with-bounded-maximum/)****
    
    🏆 ****[992. K 个不同整数的子数组](https://leetcode.cn/problems/subarrays-with-k-different-integers/)****
    
    ****[713. 乘积小于 K 的子数组](https://leetcode.cn/problems/subarray-product-less-than-k/)****
    

# 前缀和

需要用到子区间和时，考虑借助一个数组，去存储前缀和，需要区别于双指针的用法。

![Untitled](assets/206c5caefe2f61469c81befc5a8e4f7854202.png)

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

# 链表里的双指针

快慢指针，快指针一次2步，慢指针一次1步。

## 典型例题：环形链表

![Untitled](assets/206c5caefe2f61469c81befc5a8e4f7854203.png)

```go
func hasCycle(head *ListNode) bool {
// 快慢指针法，有环必套圈，无环快指针会到 null
// 了解即可，在实际开发中应用较少，可以用哈希表来存储，没必要检查环
    fast := head
    for (fast != nil && fast.Next != nil) {
        fast = fast.Next.Next
        head = head.Next
        if (fast == head) {
            return true
        }
    }
    return false
}
```

## 同类型练习题

****[19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)****

****[876. 链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)****

****[142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)****