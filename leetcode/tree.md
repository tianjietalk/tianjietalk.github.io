# 二叉树

## [94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */

// func dfs(root *TreeNode) {
// 	if root == nil {
// 		return
// 	}
// 	dfs(root.Left)
// 	res = append(res, root.Val)
// 	dfs(root.Right)
// }

func inorderTraversal(root *TreeNode) []int {
    res := make([]int, 0)
    if root == nil {
        return res
    }
    // 观察dfs递归，越往左越早被打印
    // 还是用 stack，左子树全部 push stack，pop 的时候，print
    // 顺便把 currentNode.right push stack
    stack := make([]*TreeNode, 0)
    stack = append(stack, root)
    visitedMap := make(map[*TreeNode]bool)
    for len(stack) > 0 {
        top := stack[len(stack)-1]
        visited, ok := visitedMap[top.Left]
        // top is root, top.Left has been visited
        if top.Left != nil && (!ok || !visited ){
            stack = append(stack, top.Left)
        } else {
            res = append(res, top.Val)
            stack = stack[:len(stack)-1]
            visitedMap[top] = true
            if top.Right != nil {
                stack = append(stack, top.Right)
            }
        }
    }
    return res
}
```

## [589. N 叉树的前序遍历](https://leetcode.cn/problems/n-ary-tree-preorder-traversal/)

```go
func preorder(root *Node) []int {
	res := make([]int, 0)
	if root == nil {
		return res
	}
	stack := make([]*Node, 0)
	stack = append(stack, root)
	for len(stack) > 0 {
		top := stack[len(stack)-1]
		res = append(res, top.Val)
		stack = stack[:len(stack)-1]
		for i := len(top.Children) - 1; i >= 0; i-- {
			stack = append(stack, top.Children[i])
		}
	}
	return res
}
```

## 145. 二叉树的后序遍历

```go
var res []int

func postorderTraversal(root *TreeNode) []int {
	res = make([]int, 0)
	dfs(root)
	return res
}

func dfs(root *TreeNode) {
	if root == nil {
		return
	}
	dfs(root.Left)
	dfs(root.Right)
	res = append(res, root.Val)
}
```

## [429. N 叉树的层序遍历](https://leetcode.cn/problems/n-ary-tree-level-order-traversal/)

```go
func levelOrder(root *Node) [][]int {
	res := make([][]int, 0)
	if root == nil {
		return res
	}
	queue := make([]*Node, 0)
	queue = append(queue, root)
	for len(queue) > 0 {
		size := len(queue)
		level := make([]int, 0)
		for i := 0; i < size; i++ {
			node := queue[0]
			queue = queue[1:]
			level = append(level, node.Val)
			queue = append(queue, node.Children...)
		}
		res = append(res, level)
	}
	return res
}
```

## [297. 二叉树的序列化与反序列化](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)
    
  ```go
    type Codec struct {
    }
    
    func Constructor() Codec {
    	return Codec{}
    }
    
    // Serializes a tree to a single string.
    func (this *Codec) serialize(root *TreeNode) string {
    	if root == nil {
    		return "x"
    	}
    	leftVal := this.serialize(root.Left)
    	rightVal := this.serialize(root.Right)
    	return fmt.Sprintf("%d,%s,%s", root.Val, leftVal, rightVal)
    }
    
    // Deserializes your encoded data to tree.
    func (this *Codec) deserialize(data string) *TreeNode {
    	// 1,2,x,x,3,4,x,x,5,x,x
    	list := strings.Split(data, ",")
    	return this.buildTree(&list)
    }
    
    func (this *Codec) buildTree(list *[]string) *TreeNode {
    	nodeVal := (*list)[0]
    	*list = (*list)[1:]
    	if nodeVal == "x" {
    		return nil
    	}
    	val, _ := strconv.Atoi(nodeVal)
    	node := &TreeNode{Val: val}
    	node.Left = this.buildTree(list)
    	node.Right = this.buildTree(list)
    	return node
    }
  ```
    
## 🌟 [105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

- 递归
    
    ```go
    // preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
    // 规律：前序数组的第一个元素是根结点，中序数组找到对应的值，就能把中序数组一分为二，前面是左节点，后面是右节点。再往下就是递归地构造左右节点
    // 关键在于，根据 inorder 划分 preorder 的范围，子问题的事交给递归
    // 用一个数组确定根节点位置，另一个数组确定左右树的范围
    // 递归做法
    func buildTree(preorder []int, inorder []int) *TreeNode {
    	if len(preorder) == 0 {
    		return nil
    	}
    	root := &TreeNode{preorder[0], nil, nil}
    	var index int
    	for index = 0; index < len(inorder); index++ {
    		if inorder[index] == preorder[0] {
    			break
    		}
    	}
    	root.Left = buildTree(preorder[1:index+1], inorder[:index])
    	root.Right = buildTree(preorder[index+1:], inorder[index+1:])
    	return root
    }
    ```
    
- 迭代
    
    ```go
    func buildTree(preorder []int, inorder []int) *TreeNode {
    	if len(preorder) == 0 {
    		return nil
    	}
    	pre, in := 0, 0
    	root := &TreeNode{preorder[0], nil, nil}
    	stack := make([]*TreeNode, 0)
    	stack = append(stack, root)
    	pre++
    
    	// preorder 的中心节点在前，inorder 的左节点在前
    	// 2 个指针，对左侧树来说，preorder 顺序，inorder 倒序
    	// 用 stack 刚好满足，按 preorder 来入栈，inorder 来出栈
    	// top node == inorder[in] 时，说明到了中心节点，前面的都是左节点，下一步要找右节点 -> 怎么找？
    	for pre < len(preorder) {
    		node := stack[len(stack)-1]
    		if node.Val == inorder[in] {
    			for len(stack) > 0 && stack[len(stack)-1].Val == inorder[in] {
    				node = stack[len(stack)-1]
    				stack = stack[:len(stack)-1]
    				in++
    			}
    			node.Right = &TreeNode{preorder[pre], nil, nil}
    			stack = append(stack, node.Right)
    			pre++
    		} else {
    			node.Left = &TreeNode{preorder[pre], nil, nil}
    			stack = append(stack, node.Left)
    			pre++
    		}
    	}
    
    	return root
    }
    ```
    

## [106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

- 递归
    
    ```go
    // 说到底，还是分治思想
    func buildTree(inorder []int, postorder []int) *TreeNode {
    	// inorder = [9,3,15,20,7], postorder = [9,15,7,20,3]
    	if len(inorder) == 0 {
    		return nil
    	}
    	val := postorder[len(postorder)-1]
    	root := &TreeNode{
    		Val: val,
    	}
    	var index int
    	for i, v := range inorder {
    		if v == val {
    			index = i
    			break
    		}
    	}
    	root.Left = buildTree(inorder[:index], postorder[:index])
    	root.Right = buildTree(inorder[index+1:], postorder[index:len(postorder)-1])
    	return root
    }
    ```
    
- 迭代

## [BST 的搜索](https://leetcode.cn/problems/search-in-a-binary-search-tree/)

```go
func searchBST(root *TreeNode, val int) *TreeNode {
	if root == nil {
		return nil
	}
	if root.Val == val {
		return root
	}
	if root.Val > val {
		return searchBST(root.Left, val)
	}
	return searchBST(root.Right, val)
}
```

## 找前驱、后继

```go
// [10,4,18,3,6,14,20]
func findPredecessor(root *TreeNode, val int) *TreeNode {
	node := search(root, val)
	if node.Left != nil {
		// has left child, then find the max of the left subtree
		return findMaximum(node.Left)
	}
	// no left child, then find the first left anestor (top -> bottom)
	parent := findParent(root, node.Val)
	for parent != nil && node == parent.Left {
		node = parent
		parent = findParent(root, node.Val)
	}
	return parent
}

func findSuccessor(root *TreeNode, val int) *TreeNode {
	node := search(root, val)
	if node.Right != nil {
		// has right child, then find the min of the right subtree
		return findMinimum(node.Right)
	}
	// no right child, then find the first right parent
	parent := findParent(root, node.Val)
	for parent != nil && node == parent.Right {
		node = parent
		parent = findParent(root, node.Val)
	}
	return parent
}
```

## 🌟 [450. 删除二叉搜索树中的节点](https://leetcode.cn/problems/delete-node-in-a-bst/description/)

```go
func deleteNode(root *TreeNode, key int) *TreeNode {
    if root == nil {
        return nil
    }
    // key is in the left subtree
    if root.Val > key {
        root.Left = deleteNode(root.Left, key)
    } else if root.Val < key {
        root.Right = deleteNode(root.Right, key)
    } else {
        // leaf node
        if root.Left == nil && root.Right == nil {
            return nil
        } else if root.Left != nil {
            // has left, find the predecessor of root then delete it
            root.Val = findMaximum(root.Left)
            root.Left = deleteNode(root.Left, root.Val)
        } else if root.Right != nil {
            // has right, find the successor of root then delete it
            root.Val = findMinimum(root.Right)
            root.Right = deleteNode(root.Right, root.Val)
        }
    }
    return root
}

func findMinimum(root *TreeNode) int {
    for root.Left != nil {
        root = root.Left
    }
    return root.Val
}

func findMaximum(root *TreeNode) int {
    for root.Right != nil {
        root = root.Right
    }
    return root.Val
}
```

## [701. 二叉搜索树中的插入操作](https://leetcode.cn/problems/insert-into-a-binary-search-tree/)

```go
func insertIntoBST(root *TreeNode, val int) *TreeNode {
    if root == nil {
        return &TreeNode{ Val: val }
    }
    if root.Val > val {
        root.Left = insertIntoBST(root.Left, val)
    }
    if root.Val < val {
        root.Right = insertIntoBST(root.Right, val)
    }
    return root
}
```

## 🌟 [236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

```go
var ans *TreeNode

func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
	ans = nil
	_, _ = dfs(root, p, q)
	return ans
}

func dfs(root, p, q *TreeNode) (bool, bool) {
	if root == nil {
		return false, false
	}
	leftHasP, leftHasQ := dfs(root.Left, p, q)
	rightHasP, rightHasQ := dfs(root.Right, p, q)

	hasP := leftHasP || rightHasP || root.Val == p.Val
	hasQ := leftHasQ || rightHasQ || root.Val == q.Val

	if hasP && hasQ && ans == nil {
		ans = root
	}
	return hasP, hasQ
}
```

## [113. 路径总和 II](https://leetcode.cn/problems/path-sum-ii/)

```go
var ans [][]int

func pathSum(root *TreeNode, targetSum int) [][]int {
	ans = make([][]int, 0)
	res := make([]int, 0)
	dfs(root, targetSum, res, 0)
	return ans
}

func dfs(root *TreeNode, targetSum int, res []int, sum int) {
	if root == nil {
		return
	}
	// push to recursive stack
	res = append(res, root.Val)
	sum = sum + root.Val

	// check the condition
	if sum == targetSum && root.Left == nil && root.Right == nil {
		copied := append([]int(nil), res...)
		ans = append(ans, copied)
	}

	// next recursive
	dfs(root.Left, targetSum, res, sum)
	dfs(root.Right, targetSum, res, sum)

	// // pop from the stack
	// res = res[:len(res)-1]
	// sum = sum - root.Val

	// res []int, save the current path nodes
	// sum += cuurent root.val -> push to stack
	// if sum == target && current node is leaf, then add to res
	// sum -= cuurent root.val -> pop from stack
}
```

## 🌟 [124. 二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

```go
var maxSum int
func maxPathSum(root *TreeNode) int {
    maxSum = math.MinInt32
    dfs(root)
    return maxSum
}

func dfs(root *TreeNode) int {
    // root is null -> 0
    if root == nil {
        return 0
    }

    // next recursive, left sum
    left := dfs(root.Left)
    // next recursive, right sum
    right := dfs(root.Right)

    // check the condition
    // current sum = left + right + current node val
    current := left + right + root.Val
    // check if or not the max
    maxSum = max(maxSum, current)

    // pop from the stack
    // output = current node val + max(left, right)
    output := root.Val + max(left, right)
    return max(output, 0)
}

func max(x, y int) int {
    if x > y {
        return x
    }
    return y
}
```