# 堆

## 建堆

```go
type MinHeap struct {
	heap []int
}

func (h *MinHeap) BuildHeap(array []int) {
	// copy
	h.heap = append(h.heap, array...)
	// sift down heapify
	lastNonLeafIndex := (len(array) - 2) / 2
	for i := lastNonLeafIndex; i >= 0; i-- {
		h.siftDown(i)
	}
}

func (h *MinHeap) siftDown(index int) {
	leftIndex := index*2 + 1
	rightIndex := index*2 + 2
	swapIndex := index
	// 比较
	if leftIndex < len(h.heap) && h.heap[leftIndex] < h.heap[swapIndex] {
		swapIndex = leftIndex
	}
	if rightIndex < len(h.heap) && h.heap[rightIndex] < h.heap[swapIndex] {
		swapIndex = rightIndex
	}
	// 交换
	if swapIndex != index {
		h.swap(swapIndex, index)
		h.siftDown(swapIndex)
	}
}
func (h *MinHeap) siftUp(index int) {
	parentIndex := (index - 1) / 2
	swapIndex := index
	if parentIndex >= 0 && h.heap[parentIndex] > h.heap[swapIndex] {
		swapIndex = parentIndex
	}
	if swapIndex != index {
		h.swap(swapIndex, index)
		h.siftUp(swapIndex)
	}
}

func (h *MinHeap) Pop() int {
	n := len(h.heap)
	value := h.heap[0]
	h.swap(0, n-1)
	h.heap = h.heap[:n-1]
	h.siftDown(0)
	return value
}

func (h *MinHeap) Insert(num int) {
	h.heap = append(h.heap, num)
	h.siftUp(len(h.heap) - 1)
}

func (h *MinHeap) swap(i, j int) {
	h.heap[i], h.heap[j] = h.heap[j], h.heap[i]
}
```

## [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array)

```go
func findKthLargestUseMaxHeap(nums []int, k int) int {
	h := &MaxHeap{heap: make([]int, 0)}
	h.BuildHeap(nums)
	for i := 1; i <= k; i++ {
		value := h.Pop()
		if i == k {
			return value
		}
	}
	return 0
}
```
## [23. 合并K个排序链表](https://leetcode.cn/problems/merge-k-sorted-lists)

```go
func mergeKListsUseMinHeap(lists []*ListNode) *ListNode {
	l := &ListMinHeap{
		heap: make([]*ListNode, 0),
	}
	l.buildHeap(lists)
	head := &ListNode{}
	protect := head
	for {
		if len(l.heap) == 0 {
			break
		}
		// 1. find min node
		minNode := l.pop()
		// 2. put it to head.next
		head.Next = minNode
		head = head.Next
		// 3. put minNode.next to heap
		if minNode.Next != nil {
			l.heap = append(l.heap, minNode.Next)
			l.upHeap(len(l.heap) - 1)
		}
	}
	return protect.Next
}
```

## [347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

```go
func topKFrequent(nums []int, k int) []int {
	// 构造 count map，记录每个数字出现的次数
	counter := make(map[int]int) // key: num, value: count
	for _, v := range nums {
		counter[v]++
	}
	// 构造 min heap，因为每次 pop 的都是最小频率的元素，替换成大的
	minHeap := &CountMinHeap{heap: make([]int, 0)}
	for num, count := range counter {
		// heap length < k -> push
		if len(minHeap.heap) < k {
			minHeap.Insert(num, counter)
		} else {
			// heap length >= k -> compare -> pop -> push
			top := minHeap.heap[0]
			topCount := counter[top]
			if topCount < count {
				minHeap.Pop(counter)
				minHeap.Insert(num, counter)
			}
		}
	}
	// 遍历 heap，输出结果
	res := make([]int, 0)
	for i := len(minHeap.heap) - 1; i >= 0; i-- {
		res = append(res, minHeap.heap[i])
	}
	return res
}
```


## [295. 数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)

```go
// 构造大顶堆、小顶堆，大顶堆放前半部分数据，小顶堆放后半部分数据
// 当两边 size 差距 > 2 时，要调整，pop -> insert
type maxHeap []int
type minHeap []int

func (h maxHeap) Len() int           { return len(h) }
func (h maxHeap) Less(i, j int) bool { return h[i] > h[j] }
func (h maxHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *maxHeap) Push(x any) {
	*h = append(*h, x.(int))
}
func (h *maxHeap) Pop() any {
	old := *h
	n := len(old)
	x := old[n-1]
	*h = old[0 : n-1]
	return x
}

func (h minHeap) Len() int           { return len(h) }
func (h minHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h minHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *minHeap) Push(x any) {
	*h = append(*h, x.(int))
}
func (h *minHeap) Pop() any {
	n := len(*h)
	x := (*h)[n-1]
	*h = (*h)[0 : n-1]
	return x
}

type MedianFinder struct {
	maxQue minHeap // 比中位数大的数，队头是这些大数里的 min
	minQue maxHeap // 比中位数小的数，队头是这些小数里的 max，奇数情况比 maxQue多一位
}

func Constructor() MedianFinder {
	return MedianFinder{
		maxQue: minHeap{},
		minQue: maxHeap{},
	}
}

func (m *MedianFinder) AddNum(num int) {
	// num <= 中位数 -> 放 minQue -> 中位数变小
	if m.minQue.Len() == 0 || num <= m.minQue[0] {
		heap.Push(&m.minQue, num)
		if m.minQue.Len()-m.maxQue.Len() > 1 {
			value := heap.Pop(&m.minQue)
			heap.Push(&m.maxQue, value)
		}
	} else {
		// num > 中位数 ->中位数变大
		heap.Push(&m.maxQue, num)
		if m.maxQue.Len() > m.minQue.Len() {
			value := heap.Pop(&m.maxQue)
			heap.Push(&m.minQue, value)
		}
	}
}

func (m *MedianFinder) FindMedian() float64 {
	if m.maxQue.Len() == m.minQue.Len() {
		// 偶数个元素，计算均值
		first := m.maxQue[0]
		second := m.minQue[0]
		return (float64(first) + float64(second)) / 2
	}
	return float64(m.minQue[0])
}
```
