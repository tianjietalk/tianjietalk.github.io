# 设计题

## 146. LRU 缓存

```go
type Node struct {
	key   int
	value int
	pre   *Node
	next  *Node
}

type LRUCache struct {
	capacity int
	head     *Node
	tail     *Node
	cacheMap map[int]*Node
}

func Constructor(capacity int) LRUCache {
	head := &Node{}
	tail := &Node{}
	head.next = tail
	tail.pre = head
	return LRUCache{
		capacity: capacity,
		head:     head,
		tail:     tail,
		cacheMap: map[int]*Node{},
	}
}

func (this *LRUCache) Get(key int) int {
	if node, ok := this.cacheMap[key]; ok {
		remove(node)
		insert(this.head, node)
		return node.value
	}
	return -1
}

func (this *LRUCache) Put(key int, value int) {
	if _, ok := this.cacheMap[key]; !ok {
		node := &Node{
			key:   key,
			value: value,
		}
		insert(this.head, node)
		this.cacheMap[key] = node
		if len(this.cacheMap) > this.capacity {
			last := this.tail.pre
			remove(last)
			delete(this.cacheMap, last.key)
		}
	} else {
		node := this.cacheMap[key]
		node.value = value
		remove(node)
		insert(this.head, node)
	}
}

func insert(head, node *Node) {
	next := head.next
	//	head <-> node <-> next
	node.next = next
	next.pre = node
	head.next = node
	node.pre = head
}

func remove(node *Node) {
	//	pre -> next
	//	pre <- next
	node.pre.next = node.next
	node.next.pre = node.pre
}

// length: 5
// put、get的顺序：ABCDBEFG
// GFEBD

// 1. 线性数据结构，按时间顺序存储数据
// 2. 支持在头部插入、尾部删除、中间删除
// 3. PUT: 元素头部插入，容量满了后，删除尾部元素
// 4. GET: 元素从中间删除，插入到头部

```

## 460. LFU 缓存

```go
type Node struct {
	key   int
	value int
	freq  int
	pre   *Node
	next  *Node
}

type DoubleLinkedList struct {
	size int
	head *Node
	tail *Node
}

type LFUCache struct {
	size     int
	capacity int
	min      int
	freqMap  map[int]*DoubleLinkedList
	nodeMap  map[int]*Node
}

func initLinkedList() *DoubleLinkedList {
	head, tail := &Node{}, &Node{}
	head.next = tail
	tail.pre = head
	return &DoubleLinkedList{
		size: 0,
		head: head,
		tail: tail,
	}
}

func Constructor(capacity int) LFUCache {
	return LFUCache{
		size:     0,
		capacity: capacity,
		freqMap:  map[int]*DoubleLinkedList{},
		nodeMap:  map[int]*Node{},
	}
}

// 比LRU复杂的地方，在于多了freq，同freq里依然是LRU => freq map（双向链表） + 节点map
// GET：
//      1、使用节点 map 在 O(1) 取 node
//		2、更新 freq map，从原freq链表中删除，插入到 freq+1 的链表中
//		3、原 freq 变空的话，是否要更新 min freq
// PUT：
//		1、添加到节点 map、freq[1] 链表中
//		2、更新 min freq 为 1
//		3、容量满了后，删去 min freq 链表中的尾部节点

func (this *LFUCache) Get(key int) int {
	if node, ok := this.nodeMap[key]; ok {
		this.update(node)
		return node.value
	}
	return -1
}

func (this *LFUCache) update(node *Node) {
	freq := node.freq
	this.remove(freq, node)
	if this.min == freq && this.freqMap[freq].size == 0 {
		this.min += 1
	}
	node.freq += 1
	this.insert(freq+1, node)
}

func (this *LFUCache) remove(freq int, node *Node) {
	if list, ok := this.freqMap[freq]; ok {
		//	pre node next
		node.pre.next = node.next
		node.next.pre = node.pre
		list.size--
	}
}

func (this *LFUCache) insert(freq int, node *Node) {
	var list *DoubleLinkedList
	if l, ok := this.freqMap[freq]; ok {
		list = l
	} else {
		list = initLinkedList()
	}
	//	head <-> next
	head := list.head
	next := head.next
	node.next = next
	next.pre = node
	head.next = node
	node.pre = head
	list.size += 1
	this.freqMap[freq] = list
}

func (this *LFUCache) Put(key int, value int) {
    if this.capacity == 0 {
        return
    }
	if node, ok := this.nodeMap[key]; ok {
		node.value = value
		this.update(node)
	} else {
		if len(this.nodeMap) == this.capacity {
			if minList, ok := this.freqMap[this.min]; ok {
                last := minList.tail.pre
                this.remove(this.min, last)
                delete(this.nodeMap, last.key)
            }
		}
		node := &Node{
			key:   key,
			value: value,
			freq:  1,
		}
		this.insert(1, node)
		this.nodeMap[key] = node
		this.min = 1
	}
}
```

## 641. 设计循环双端队列

```go
type Node struct {
	value int
	pre   *Node
	next  *Node
}

type MyCircularDeque struct {
	capacity int
	size     int
	head     *Node
	tail     *Node
}

func Constructor(k int) MyCircularDeque {
    head, tail := &Node{value: -1}, &Node{value: -1}
	head.next = tail
	tail.pre = head
	return MyCircularDeque{
		capacity: k,
		size:     0,
		head:     head,
		tail:     tail,
	}
}

func (this *MyCircularDeque) InsertFront(value int) bool {
	if this.IsFull() {
		return false
	}
	// head <-> next
	node := &Node{value: value}
	next := this.head.next
	node.next = next
	next.pre = node
	this.head.next = node
	node.pre = this.head
	this.size++
	return true
}

func (this *MyCircularDeque) InsertLast(value int) bool {
	if this.IsFull() {
		return false
	}
	// pre <-> tail
	node := &Node{value: value}
	pre := this.tail.pre
	pre.next = node
	node.pre = pre
	node.next = this.tail
	this.tail.pre = node
	this.size++
	return true
}

func (this *MyCircularDeque) DeleteFront() bool {
	if this.IsEmpty() {
		return false
	}
	// head <-> node <-> next
	node := this.head.next
	next := node.next
	this.head.next = next
	next.pre = this.head
	this.size--
	return true
}

func (this *MyCircularDeque) DeleteLast() bool {
	if this.IsEmpty() {
		return false
	}
	// pre <-> node <-> tail
	node := this.tail.pre
	pre := node.pre
	pre.next = this.tail
	this.tail.pre = pre
	this.size--
	return true
}

func (this *MyCircularDeque) GetFront() int {
	return this.head.next.value
}

func (this *MyCircularDeque) GetRear() int {
	return this.tail.pre.value
}

func (this *MyCircularDeque) IsEmpty() bool {
	return this.size == 0
}

func (this *MyCircularDeque) IsFull() bool {
	return this.size == this.capacity
}
```


## [最小栈](https://leetcode.cn/problems/min-stack/)

```go
type MinStack struct {
    stack []int
    minStack []int
}

// stack:    -2 0
// min
// minStack: -2 -2
// minStack 辅助栈（额外空间存储当前栈的最小值）

func Constructor() MinStack {
    return MinStack{
        stack: []int{},
        minStack: []int{math.MaxInt64},
    }
}

func (this *MinStack) Push(val int)  {
    this.stack = append(this.stack, val)
    top := this.minStack[len(this.minStack) - 1]
    this.minStack = append(this.minStack, min(val, top))
}

func (this *MinStack) Pop()  {
    this.stack = this.stack[:len(this.stack) - 1]
    this.minStack = this.minStack[:len(this.minStack) - 1]
}

func (this *MinStack) Top() int {
    return this.stack[len(this.stack) - 1]
}

func (this *MinStack) GetMin() int {
    return this.minStack[len(this.minStack) - 1]
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}

/**
 * Your MinStack object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(val);
 * obj.Pop();
 * param_3 := obj.Top();
 * param_4 := obj.GetMin();
 */
```