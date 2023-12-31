# 二叉树的必知必会

> 💡 本期精彩内容：
> 
> 🤖 为什么要学二叉树，现实有哪些应用
> 
> 🙌 二叉树要掌握哪些技巧，有哪些高频题
> 
> 👩🏻‍💻 面试里二叉树怎么考，怎么答

# 有哪些重要的二叉树

## 基本概念

- 节点：父子节点、兄弟节点、叶子节点
- 高度、深度、层
- 存储：链式存储，一个节点存储三个字段，value, left, right

![Untitled](assets/2089821b9a3d104d8fa7f72abac6a42dde.png)

## 树的类型

- 满二叉树

```markdown
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

- 完全二叉树，除了最后一层，其余节点全满，最后一层节点靠左排列 → **堆**

```markdown
        1
      /   \
     2     3
    / \   /
   4   5 6
```

- **二叉搜索树**，平衡二叉树，AVL 和 红黑树（敬请期待….

```markdown
        6
      /   \
     2     8
    / \   / \
   1   4 7   9
      / \
     3   5
```

# 为什么需要二叉树

## 二叉搜索树 - BST

- 中序遍历，会得到有序的数组，时间复杂度 O(n)
- 插入、删除、查找的时间复杂度和树的高度成正比，O(height) → AVL 的时间复杂度 O(logn)

```markdown
        6
      /   \
     2     8
    / \   / \
   1   4 7   9
      / \
     3   5
```

## 🌟 应用场景

- AST，用于编译器、代码的分析和优化，例如：Java 编译器会把 Java 代码转换成 AST，再转换成字节码；前端工具 Babel 处理 JavaScript 代码
- 数据库索引，B+ 树
- Linux 内核文件管理系统，红黑树
- 红黑树、AVL 应用的各种应用

# 关于二叉树，你需要掌握的基本技能

## 树的遍历

- DFS：前序、中序、后序，递归 + 迭代（借助 stack）
- BFS：层次遍历，借助 queue

## 树的构建（遍历的反面）

> 前后序列无法确定唯一的二叉树 → 没有中序，很难判断根节点在哪
> 
- 根据前序、中序数组构建
- 根据中序、后序数组构建
- 序列化、反序列化

## 树的增删改查

> 基本是对 BST 的考察，普通二叉树没有增删改查的考察必要
> 
- BST 插入节点，平衡 BST 是后话
- 🌟 BST 删除节点，重点是前驱、后继 → 建堆的过程
- 🌟 BST 查找指定节点、找父节点、找最大最小值、找前驱、后继

# 面试中，二叉树有哪些高频题

- 遍历
    - 普通遍历（考得少了）
    - 变形：反转二叉树、是否是镜像二叉树、序列化打印二叉树
    - 变形 2：根据给定条件构建二叉树
- 🔥 搜索（BST）→ 主打 DFS，考察递归的熟悉程度
    - 基础：指定节点、找父节点、找最大最小值、找前驱、后继
    - 变形：最近公共祖先、第 K 小的节点、路径和、树的深度
- 修改
    - 删除节点

# 面试考察完二叉树，如何延伸

> 强烈建议，至少掌握一种二叉树的延伸！
> 
- 堆、堆排序
    - 对比快排、归并排序
- AVL / 红黑树（难度较大）
    - 对比散列表、跳表等数据结构
- 树的应用场景，能举几个例子
    - 数据库索引