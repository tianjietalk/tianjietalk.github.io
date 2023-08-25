# 链表

## [反转链表](https://leetcode.cn/problems/reverse-linked-list/)

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

## [K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

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

## [合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

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

## [链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)

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

## [删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

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

## [设计链表](https://leetcode.cn/problems/design-linked-list/)
## [环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)
## [相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/)
## [移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/)
## [奇偶链表](https://leetcode.cn/problems/odd-even-linked-list/)
## [回文链表](https://leetcode.cn/problems/palindrome-linked-list/)
## [两数相加](https://leetcode.cn/problems/add-two-numbers/)
## [扁平化多级双向链表](https://leetcode.cn/problems/flatten-a-multilevel-doubly-linked-list/)
## [复制带随机指针的链表](https://leetcode.cn/problems/copy-list-with-random-pointer/)
## [旋转链表](https://leetcode.cn/problems/rotate-list/)