# 为什么链表题这么难？

> 链表题为什么不容易写对？
反转链表、合并有序链表这类简单题，在面试中能写对的人寥寥。
> 

# 难点

被指针的 next 绕晕，next.next 等

```go
for (cur.next && cur.next.next) {
    first := cur.next;
    second := cur.next.next;
    first.next = second.next;
    cur.next = second;
    cur.next.next = first;
    cur = cur.next.next;
 }
```

# 四大要点

## 理解指针的含义

```go
// 变量赋值给指针，表示 p 的 next 存储了 q 的内存地址
p.next = q

// 插入
// head => b, head => c => b
c.next = head.next
head.next = c
// 删除
p.next = p.next.next
// 遍历，与数组不同的是，需要根据指针去找下一个节点
for head != nil {
		head = head.next
}
```

## 改变引用前，记得缓存

```go
// 反转链表
// a  b => c
// a <= b  c
next := b.next
b.next = a
```

## 保护节点的使用（fakeHead）

链表内部有变化，结果需要返回链表的头节点

```go
// 合并有序链表，k个一组反转链表，删除倒数第n个节点
p.next = head
for (head != nil) {
	  // 遍历链表，删除、插入等操作
		head = head.next
}
return p.next
```

## 检查边界条件（数组同理）

- 链表为空、链表只有1个节点
- 双向链表，注意头节点、尾节点，是否正常

# 与数组对比

- 数组，存储于连续空间，访问更高效，根据 index 可以在 O(1) 内查到。
- 链表，存储于零散的空间，扩容更简单。数组需要动态扩容。

# LeetCode 实战

LeetBook：[https://leetcode.cn/leetbook/detail/linked-list/](https://leetcode.cn/leetbook/detail/linked-list/)

## 讲过的题

### [反转链表](https://leetcode.cn/problems/reverse-linked-list/)

```go
func reverseList(head *ListNode) *ListNode {
    var last *ListNode
    for head != nil {
        var next = head.Next
        head.Next = last
        last = head
        head = next
    }
    return last
}
```

### [K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func reverseKGroup(head *ListNode, k int) *ListNode {
    protect := &ListNode{ Val: 0, Next: head }
    last := protect
    // 分组遍历
    for head != nil {
    // 1. 分组（往后走 k - 1 步，为一组）
    //    一组的开头 head 结尾 end
        end := getEnd(head, k)
        if end == nil {
            break
        }
        nextGrpHead := end.Next // 下一组的开头

    // 2. 一组内部要反转（head 到 end 之间）（调用反转链表）
        reverseList(head, nextGrpHead)

    // 3. 更新每组与前一组、后一组的边
        last.Next = end;
        head.Next = nextGrpHead
        last = head;
        head = nextGrpHead
    }
    return protect.Next
}

// 反转 head 到 stop 节点
func reverseList(head *ListNode, stop *ListNode) {
    help := head; // 只管内部的边
    head = head.Next
    for head != stop  {
        next := head.Next
        head.Next = help
        help = head
        head = next
    }
}


// 返回走 k - 1 步后的点
// 返回 null 表示不够 k 个
func getEnd(head *ListNode, k int) *ListNode {
    for head != nil {
        k--
        if k == 0 {
            return head
        }
        head = head.Next
    }
    return nil
}
```

### [合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

```go
func mergeTwoLists(A *ListNode, B *ListNode) *ListNode {
    head := &ListNode{ Val: 0, Next: nil }
    p := head
    for (A != nil || B != nil) {
        if (B == nil || (A != nil && A.Val < B.Val)) {
            head.Next = A
            A = A.Next
        } else {
            head.Next = B
            B = B.Next
        }
        head = head.Next
    }
    return p.Next
}
```


[环形链表](https://leetcode.cn/problems/linked-list-cycle/)

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

### [链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)

```go
func middleNode(head *ListNode) *ListNode {
    fast, slow := head, head
    for (fast != nil && fast.Next != nil) {
        fast = fast.Next.Next
        slow = slow.Next
    }
    return slow
}
```

### [删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    p := &ListNode{ Val: 0, Next: head }
    fast, slow := head, p
    for i := 0; i < n; i++ {
        fast = fast.Next
    }
    for fast != nil {
        slow = slow.Next
        fast = fast.Next
    }
    slow.Next = slow.Next.Next
    return p.Next
}

// 倒数第 n + 1 节点, s 需要走 L - (n + 1)
// L - (L - n - 1) = n + 1, f 提前 s n+1 步
//                f
// s
// p => 1 => 2 => 3 => 4 => 5 => nil
```

## 课外练习

- #707 - ****[设计链表](https://leetcode.cn/problems/design-linked-list/)****
- #142 - ****[环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)****
- #160 - ****[相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/)****
- #203 - ****[移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/)****
- #328 - ****[奇偶链表](https://leetcode.cn/problems/odd-even-linked-list/)****
- #234 - ****[回文链表](https://leetcode.cn/problems/palindrome-linked-list/)****
- #2 - ****[两数相加](https://leetcode.cn/problems/add-two-numbers/)****
- #430 - ****[扁平化多级双向链表](https://leetcode.cn/problems/flatten-a-multilevel-doubly-linked-list/)****
- #138 - ****[复制带随机指针的链表](https://leetcode.cn/problems/copy-list-with-random-pointer/)****
- #61 - ****[旋转链表](https://leetcode.cn/problems/rotate-list/)****