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

- #206 - ****[反转链表](https://leetcode.cn/problems/reverse-linked-list/)****
- #25 - ****[K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)****
- #21 - ****[合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)****
- #141 - ****[环形链表](https://leetcode.cn/problems/linked-list-cycle/)****
- #876 - ****[链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)****
- #19 - ****[删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)****

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