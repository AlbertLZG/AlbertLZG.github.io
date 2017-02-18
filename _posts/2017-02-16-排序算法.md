---
layout: post
title: lintcode “二叉树遍历” 题解
date: 2017-02-16
categories: blog
tags: [lintcode题解(python)]
---

#题目 Binary Tree Preorder Traversal

Given a binary tree, return the preorder traversal of its nodes' values.

Example
Given:

        1
       / \
      2   3
     / \
    4   5

return [1,2,4,5,3].

#二叉树的前序遍历

给出一棵二叉树，返回其节点值的前序遍历。

样例
给出一棵二叉树 {1,#,2,3},

    1
    \
     2
    /
    3

 返回 [1,2,3].


##题解：

递归：

```Python:
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""

class Solution:
    # """
    # @param root: The root of binary tree.
    # @return: Preorder in ArrayList which contains node values.
    # """
    def __init__(self):
        self.res = []
        
    def preorderTraversal(self, root):
        # write your code here
        if root:
            self.res.append(root.val)
            self.preorderTraversal(root.left)
            self.preorderTraversal(root.right)
        return self.res
```
#Binary Tree Inorder Traversal

Given a binary tree, return the inorder traversal of its nodes' values.

Example
Given binary tree {1,#,2,3},

    1
    \
     2
    /
    3
    
 
return [1,3,2].


#二叉树的中序遍历

给出一棵二叉树,返回其中序遍历

样例

给出二叉树 {1,#,2,3},

    1
    \
     2
    /
    3
    
返回 [1,3,2].

```Python
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""


class Solution:
    """
    @param root: The root of binary tree.
    @return: Inorder in ArrayList which contains node values.
    """
    def __init__(self):
        self.res = [] 
    def inorderTraversal(self, root):
        # write your code here
        if root:
            self.inorderTraversal(root.left)
            self.res.append(root.val)
            self.inorderTraversal(root.right)
        return self.res
```
#Binary Tree Postorder Traversal

Given a binary tree, return the postorder traversal of its nodes' values.

Example
Given binary tree {1,#,2,3},

    1
    \
     2
    /
    3
    
return [3,2,1].

#二叉树的后序遍历

给出一棵二叉树，返回其节点值的后序遍历。

样例

给出一棵二叉树 {1,#,2,3},

    1
    \
     2
    /
    3
    
返回 [3,2,1]

```
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""


class Solution:
    """
    @param root: The root of binary tree.
    @return: Postorder in ArrayList which contains node values.
    """
    def __init__(self):
        self.res = [] 
    def postorderTraversal(self, root):
        # write your code here
        if root:
            self.postorderTraversal(root.left)
            self.postorderTraversal(root.right)
            self.res.append(root.val)
        return self.res
```

#Binary Tree Level Order Traversal

Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

Have you met this question in a real interview? Yes
Example
Given binary tree {3,9,20,#,#,15,7},

      3
     / \
    9  20
      /  \
     15   7

return its level order traversal as:

[
  [3],
  [9,20],
  [15,7]
]

#二叉树的层次遍历

给出一棵二叉树，返回其节点值的层次遍历（逐层从左往右访问）

样例

给一棵二叉树 {3,9,20,#,#,15,7} ：

      3
     / \
    9  20
      /  \
     15   7

返回它的分层遍历结果：

[
  [3],
  [9,20],
  [15,7]
]

```
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""


class Solution:
    """
    @param root: The root of binary tree.
    @return: Level order in a list of lists of integers
    """
    def __init__(self):
        self.res = []
        
    def levelOrder(self, root):
        # write your code here
        if not root:
            return []
        else:
            
            level_now = [root]
            while level_now:
                self.res.append([])
                level_next = []
                for node in level_now:
                    self.res[-1].append(node.val)
                    if node.left:
                        level_next.append(node.left)
                    if node.right:
                        level_next.append(node.right)
                level_now = level_next
        return self.res
```
![](https://raw.githubusercontent.com/AlbertLZG/AlbertLZG.github.io/master/img/blog_logo.png)











