# LeetCode 二叉树

## 进度条：二叉树：搜索树中的删除操作(未看)

## 递归三部曲

- 确定递归函数的参数和返回值
- 确定终止条件
- 确定单层递归的逻辑

## 144. 二叉树的前序遍历

> 题目：给定二叉树的根节点root，返回节点值的前序遍历

### 思路一：递归

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        preorderTraversal(root, list);
        return list;       
    }
    // 递归实现前序遍历
    public void preorderTraversal (TreeNode node, List<Integer> list) {
        if (node == null) {
            return;
        }
        list.add(node.val);
        preorderTraversal(node.left, list);
        preorderTraversal(node.right, list);
    }
}
```



### 思路二：迭代

- 迭代需要借助栈Stack这个数据结构，前序遍历顺序： 中  ->  左  ->  右；所以左右节点入栈的顺序先进右节点后进左节点；

```java
class Solution {
    // 迭代实现，借助数据结构
    public List<Integer> preorderTraversal(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        List<Integer> list = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        // 前序遍历先输出左节点，所以用栈就先压入右节点
        while (!stack.isEmpty()) {
            TreeNode temp = stack.pop();
            list.add(temp.val);
            if (temp.right != null) {
                stack.push(temp.right);
            }
            if (temp.left != null) {
                stack.push(temp.left);
            }
        }
        return list;        
    }
}
```



## 145. 二叉树的后序遍历

> 题目：给定一个二叉树，返回它的后序遍历。

### 思路一：递归

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        postorderTraversal(root, list);
        return list;
    }
    // 递归后序遍历
    public void postorderTraversal(TreeNode node, List<Integer> list) {
        if(node == null) {
            return;
        }
        postorderTraversal(node.left, list);
        postorderTraversal(node.right, list);
        list.add(node.val);
    }

}
```

### 思路二：迭代

- 后序遍历的迭代实现，借助于前序遍历的思路：

```shell
# 后序遍历顺序： 左  ->  右  ->  中
# 前序编程顺序： 中  ->  左  ->  右，通过前序遍历迭代算法中改变入栈节点的顺序，可以将前序遍历变为：中  ->  右  ->  左
# 之后将遍历的序列 Collection.reverse() 即可；
```

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        List<Integer> list = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.add(root);
        // 将前序遍历改为：中  ->  右  ->  左
        // 先将左节点入栈后将右节点入栈
        while (!stack.isEmpty()) {
            TreeNode temp = stack.pop();
            list.add(temp.val);
            if (temp.left != null) {
                stack.push(temp.left);
            }
            if (temp.right != null) {
                stack.push(temp.right);
            }
        }
        Collections.reverse(list);
        return list;
    }
}
```



## 94. 二叉树的中序遍历

> 题目：给定一个二叉树的根节点 root，返回其中序遍历；

### 思路一：递归

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        inorderTraversal(root, list);
        return list;    
    }
    // 递归中序遍历
    public void inorderTraversal (TreeNode node, List<Integer> list) {
        if (node == null) {
            return;
        }
        inorderTraversal(node.left, list);
        list.add(node.val);
        inorderTraversal(node.right, list);
    }

}
```



### 思路二：迭代

- 使用栈来模拟系统栈的调用情况，while (root != null)
- root = root.right；

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        while (root != null || !stack.isEmpty()) {
            // 找到中序遍历的节点后停止
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            root = stack.pop();
            list.add(root.val);
            root = root.right;
        }
        return list;
    }
}
```



## Morris Traversal 方法遍历二叉树(非递归，不用栈，O(1)空间)

```shell
# 参考链接：https://www.cnblogs.com/AnnieKim/archive/2013/06/15/MorrisTraversal.html
```

- 实现二叉树的前中后序遍历，有两个要求：
  - O(1) 空间复杂度，即只能使用常数空间；
  - 二叉树的形状不能被破坏(中间过程允许改变其形状)

- 要使用O(1) 空间进行遍历，最大的难点在于，遍历到子节点的时候怎样重新返回到父节点(假设节点中没有指向父节点的指针)，由于不能用栈作为辅助空间，为了解决这个问题，Morris方法用到了**线索二叉树(Threaded Binary Tree)**的概念；
- 线索二叉树：对于n个结点的二叉树，在二叉链存储结构中有n+1个空链域，利用这些空链域存放在某种遍历次序下该结点的前驱结点和后继结点的指针，这些指针称为线索，加上线索的二叉树称为线索二叉树。
- 在Morris方法中不需要为每个节点额外分配指针指向其前驱(predecessor)和后继节点(successor)，只需要利用叶子节点中的左右空指针指向某种遍历下的前驱或后继节点就可以了；

```java
//  二叉树结点
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int val) {
        this.val = val;
        left = null;
        right = null;
    }
}
```



### 应用1：中序遍历

- 利用右子树，可以将整个二叉树都划分为右子树，每个节点都过两遍，此时可以知道所有的节点总共遍历两次，所以总的时间复杂度为O(N)
- Morris遍历本质：对于递归版本的二叉树，来到一个节点会遍历左子树和右子树，遍历完左子树之后会重新回到当前节点，遍历完右子树之后，同样需要返回当前节点，所以总共遍历了当前节点三次。Morris遍历中，如果这个数树有左子树，则遍历这个节点两次，否则遍历这个节点一次。Morris是利用左子节点的最右孩子来判断是第一次来到这个节点还是第二次来到这个节点。**一个节点在第一次来到这个节点其实就是前序遍历。**

```shell
# 步骤：
	1. 若当前节点的左孩子为空，则输出当前节点并将其右孩子作为当前节点；
	2. 如果当前节点的左孩子不为空，在当前节点的左子树中找到当前节点在中序遍历下的前驱节点。
	  a) 如果前驱节点的右孩子为空，将它的右孩子设置为当前节点。当前节点更新为当前节点的左孩子。
   	  b) 如果前驱节点的右孩子为当前节点，将它的右孩子重新设为空（恢复树的形状）。输出当前节点。当前节点更新为当前节点的右孩子。
	3. 重复以上1、2直到当前节点为空。
```

```java
class Solution {
    // 利用线索二叉树，空间复杂度O(1)
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        TreeNode cur = root;
        while (cur != null) {
            if (cur.left == null) {
                // 左节点为空，跳到右节点
                res.add(cur.val);
                cur = cur.right;
            } else {
                TreeNode pre = cur.left;
                // 找到cur节点中序遍历的前驱结点：左子树的最右节点
                while (pre.right != null && pre.right != cur) {
                    pre = pre.right;
                }
                // 第一次到达，构造线索树，设置线索，右节点为当前节点；
                if (pre.right == null) {
                    pre.right = cur;
                    cur = cur.left;
                } else {
                // pre.right == cur 的情况：
                // 第二次到达，需要回溯，说明cur节点的左子树已经遍历完毕
                // 意味着前驱节点的右节点已被设置，该次遍历为回溯；左边已经搞定，接下来处理右边
                    pre.right = null;
                    res.add(cur.val);
                    cur = cur.right;
                }
            }
        }
        return res;
    }
}
```



### 应用2：前序遍历

- 前序遍历在中序遍历的基础上，构造线索树即第一次到达叶子节点的左右节点的时候就将cur节点值加入到结果集中；

```java
class Solution {
    // 构造线索树实现前序遍历的非递归写法
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        TreeNode cur = root;
        while (cur != null) {
            if (cur.left == null) {
                res.add(cur.val);
                cur = cur.right;
            } else {
                TreeNode pre = cur.left;
                while (pre.right != null && pre.right != cur) {
                    pre = pre.right;
                }
                if (pre.right == null) {
                    // 构建线索树
                    res.add(cur.val);
                    pre.right = cur;
                    cur = cur.left;
                } else {    // pre.right == cur 的情况
                    pre.right = null;
                    cur = cur.right;
                }
            }

        }
        return res;
    }
}
```





## 102. 二叉树的层序遍历

> 题目：给定一棵二叉树，返回其按层序遍历得到的节点值(即逐层地，从左到右访问所有节点)
>
> 将每一层封装为一个 List<Integer>

- 注意：返回结果要按层包装为List<Integer>；

### 思路一：myself

- 在整个循环体中，当前层的Node出队，下一层的非空节点入队，统计下一层的数量；
- 统计层的变量 layerList 放在循环体中；

```java
class Solution {

    public List<List<Integer>> levelOrder(TreeNode root) {
        if(root == null){
            return new ArrayList<>();
        }
        List<List<Integer>> res = new ArrayList<>();         // 封装最终的返回结果      
        Deque<TreeNode> queue = new LinkedList<>();
        int layerNum = 1;                                    // 统计每层的节点个数
        queue.add(root);
        while (!queue.isEmpty()) {
            int tempNextLayerNum = 0;                         // 统计下一层的节点个数
            List<Integer> layerList = new ArrayList<>();      // 封装每层的节点值，layerList要放在循环内部，不然返回结果中的值会被每次更新的替换，比如第一次是 [1,2]，第二次是[3,4]，那么最后res中的结果就是{[3,4],[3,4]};
            // 将该层的节点出队                        
            while (layerNum > 0) {
                TreeNode node = queue.remove();
                // 下一层节点入队
                if(node.left != null){
                    queue.add(node.left);
                    tempNextLayerNum ++;
                }
                if(node.right != null){
                    queue.add(node.right);
                    tempNextLayerNum ++;
                }
                layerList.add(node.val);
                layerNum --;                
                if(layerNum == 0){
                    res.add(layerList);
                }
            }
            layerNum = tempNextLayerNum;
        }
        return res;
    }

}
```

### 思路二：LeetCode

- 阅读代码之后，发现LeetCode中的思路与自己的思路大致相同，维护一个每层中TreeNode的数量，将每层的节点入队并在出队的时候将下一层的节点入队；
- 但有一说一，人家的代码写的比我的好，相当于是在自己的思路上的一个优化吧

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if (root == null) {
            return res;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();		   // 保存每一层节点的值，list要在循环内部
            int layerNum = queue.size();			       // 上一层的node出队后，下一层的节点数量就是queue.size();
            for (int i = 1; i <= layerNum; i ++) {		   // 循环起始 i == 1,循环结束 i <= layerNum
                TreeNode temp = queue.poll();
                list.add(temp.val);
                if (temp.left != null) {
                    queue.add(temp.left);
                }
                if (temp.right != null) {
                    queue.add(temp.right);
                }
            }
            res.add(list);
        }
        return res;
    }
}
```

## 107. 二叉树的层序遍历 II

> 题目：给定一个二叉树，返回其节点值自底向上的层序遍历。(即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历)

### 思路：二叉树层序遍历 + 翻转reverse

```java
class Solution {
    // 二叉树层序遍历的基础上，将结果reverse
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if (root == null) {
            return res;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i ++) {
                TreeNode temp = queue.poll();
                list.add(temp.val);
                if (temp.left != null) {
                    queue.add(temp.left);
                }
                if (temp.right != null) {
                    queue.add(temp.right);
                }
            }
            res.add(list);
        }
        Collections.reverse(res);
        return res;
    }
}
```



## 199. 二叉树的右视图

> 题目：给定一棵二叉树，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧能看到的节点值；

### 思路一：层序遍历 + 右节点先入队

- 目的是要找到每一层的最右面的节点，但不一定就是一直遍历 右节点 ---  右节点；
- 改进层序遍历，每次子树的右节点先入队，每次队列中的元素是每一层中的节点，所以这样队列头元素就是最右面的元素；

```java
class Solution {
    // 找到每一层最右的节点，不一定就是一直右节点 -- 右节点
    // 改进层序遍历，每次子树的右节点先入队，拿出队列头元素即可
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            res.add(queue.peek().val);
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i ++) {
                TreeNode temp = queue.poll();	// poll() 方法出队
                if (temp.right != null) {		// 出队节点的右节点先入队
                    queue.add(temp.right);
                }
                if (temp.left != null) {
                    queue.add(temp.left);
                }
            }
        }
        return res;
    }
}
```



## 637. 二叉树的层平均值

> 题目：给定一个非空二叉树，返回一个由每层节点平均值组成的数组。

### 思路一：层序遍历 + 求平均值

- 利用层序遍历框架，求出每一层的总和和平均值，放入结果中即可；

```java
class Solution {
    public List<Double> averageOfLevels(TreeNode root) {
        List<Double> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            double sum = 0.0;
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i ++) {
                TreeNode temp = queue.poll();
                sum += temp.val;
                if (temp.left != null) {
                    queue.add(temp.left);
                }
                if (temp.right != null) {
                    queue.add(temp.right);
                }
            }
            res.add(sum / layerNum);
        }
        return res;
    }
}
```



## 429. N叉树的层序遍历

> 题目：给定一个N叉树，返回其节点值的层序遍历(即从左到右，逐层遍历)。树的序列化输入是用层序遍历，每组子节点都由null分割。

```java
// Definition for a Node
class Node {
    public int val;
    public List<Node> children;
    public Node () {};
    pubblic Node (int _val) {
        val = _val;
    }
    public Node (int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
}
```

### 思路一：层序遍历框架 + 存储children节点

```java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if (root == null) {
            return res;
        }
        Deque<Node> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();     // 存储每层的节点值
            int layerNum = queue.size();                // 存储每层的节点个数
            for (int i = 1; i <= layerNum; i ++) {
                Node temp = queue.poll();
                list.add(temp.val);
                for (Node node: temp.children) {
                    queue.add(node);
                }
            }
            res.add(list);
        }
        return res;
    }
}
```



## 226.  翻转二叉树

> 题目：翻转一棵二叉树。

**解题思路：翻转二叉树其实就是把每一个节点的左右孩子交换一下。遍历过程中去翻转每一个节点的左右孩子就可以达到整体翻转的效果。这道题目可以使用前、后、层序遍历，但不可使用中序遍历。因为中序遍历过程中交换根节点的左右节点后，遍历右节点还是相当于遍历未交换根节点左右节点前的左节点，最终的效果就是原二叉树的左节点的左右孩子交换了两遍，右节点没有交换左右孩子。**

### 思路一：层序遍历框架 + 翻转节点的左右子节点

- 基础框架仍然为**层序遍历框架**；

- 在遍历每一层节点的时候，交换出队节点的左右子节点；
- 注意点：交换左右子节点的时候，若有空节点仍然需要执行交换操作；但左右子节点入队的时候，空节点不需要入队；

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i ++) {
                TreeNode temp = queue.poll();
                Swap(temp);  // 出队节点，交换左右节点。每一层解决交换节点的左右子节点，若有一个节点为空，仍然需要交换左右节点，但入队时空节点不需要入队；
                if (temp.left != null) {                                 // 左右子节点入队，若为空则不需要入队操作
                    queue.add(temp.left);
                }
                if (temp.right != null) {
                    queue.add(temp.right);
                }
            }
        }
        return root;
    }

    public void Swap (TreeNode node) {
        TreeNode node1 = node.left;
        TreeNode node2 = node.right;
        node.left = node2;
        node.right = node1;
    }

}
```

### 思路二：后序遍历 + 交换左右孩子

- 又到了有一说一的时候了，有一说一，人家这代码是真滴简单啊
- 直接用递归。。。，我咋就不会递归呢。。。

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        TreeNode node1 = invertTree(root.left);
        TreeNode node2 = invertTree(root.right);
        root.left = node2;
        root.right = node1;
        return root;
    }
}
```

### 思路三：前序遍历 + 交换左右孩子

```java
class Solution {
    // 前序遍历 + 交换左右孩子
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        } 
        // 前序遍历框架
        Swap(root);
        invertTree(root.left);
        invertTree(root.right);
        return root;
    }

    private void Swap (TreeNode node) {
        TreeNode left = node.left;
        TreeNode right = node.right;
        node.left = right;
        node.right = left;
    }
}
```

### 思路四：前序遍历迭代 + 交换左右孩子

```java
class Solution {
    // 前序迭代 + 交换左右孩子
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        // 二叉树的前序遍历迭代实现
        while (!stack.isEmpty()) {
            TreeNode temp = stack.pop();
            Swap(temp);
            if (temp.right != null) {
                stack.push(temp.right);
            }
            if (temp.left != null) {
                stack.push(temp.left);
            }
        }
        return root;
    }

    private void Swap (TreeNode node) {
        TreeNode left = node.left;
        node.left = node.right;
        node.right = left;
    }

}
```



## 101. 对称二叉树

> 题目：给定一个二叉树，检查它是否是镜像对称的。

- 仔细想想，这个题只能用**后序遍历**，因为要确定树是否是对称二叉树，要从下往上来判断(每一层都是对称的)，只有下一层是对称的上一层才有可能对称。
- 看出使用的遍历方式，左子树左右中，右子树右左中，所以我把这个遍历顺序也称之为“后序遍历”（尽管不是严格的后序遍历）
- root 节点为空的时候返回 true；

### 思路：后序遍历 + 后序遍历改进(递归)

- 对于二叉树是否对称，要比较的是根节点的左子树与右子树是不是相互翻转的，理解这一点就知道了，其实要比较的是两个数，所以在递归遍历的过程中，也是要同时遍历两棵树。比较两个子树的里侧和外侧元素是否相等。
- 本题遍历只能是后序遍历，因为要通过递归函数的返回值来判断两个子树的内侧节点和外侧节点是否相等。
- 正是因为要遍历两棵树而且要比较内侧和外侧节点，所以准确的来说是一个数的遍历顺序是左右中，一个树的遍历顺序是左中右。

![image-20210110210914601](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210110210914601.png)

- 递归条件1：确定递归函数的参数和返回值

> 要比较根节点的两个子树是否是相互翻转的，进而判断这个数是不是对称的，所以要比较的是两个树，参数就是左子树节点和右子树节点。

- 递归条件2：确定终止条件

>要比较两个节点数值相不相同，首先要把两个节点为空的情况弄清楚！否则后面比较数值的时候就会操作空指针了。
>
>节点为空的情况有：（**「注意我们比较的其实不是左孩子和右孩子，所以如下我称之为左节点右节点」**）
>
>左节点为空，右节点不为空，不对称，return false
>
>左不为空，右为空，不对称 return  false
>
>左右都为空，对称，返回true
>
>此时已经排除掉了节点为空的情况，那么剩下的就是左右节点不为空：
>
>左右都不为空，比较节点数值，不相同就return false
>
>此时左右节点不为空，且数值也不相同的情况我们也处理了。
>
>把以上情况都排除之后，剩下的就是 左右节点都不为空，且数值相同的情况

- 递归条件3：确定单层递归的逻辑

> 此时才进入单层递归的逻辑，单层递归的逻辑就是处理 右节点都不为空，且数值相同的情况。
>
> - 比较二叉树外侧是否对称：传入的是左节点的左孩子，右节点的右孩子。
> - 比较内测是否对称，传入左节点的右孩子，右节点的左孩子。
> - 如果左右都对称就返回true ，有一侧不对称就返回false 。

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        boolean res = isSymmetric(root, root);
        return res;
    }

    public boolean isSymmetric (TreeNode left, TreeNode right) {
        // 两个节点均为空，返回true
        if (left == null && right == null) {
            return true;
        }
        // 有一个节点为空，返回false
        if (left == null || right == null) {
            return false;
        }
        // 接下来的情况就是两个节点均不为空，需要比较节点值
        if (left.val != right.val) {
            return false;
        }
        // 两个节点值相等
        boolean outside = isSymmetric(left.left, right.right);// 左子树：左、 右子树：右
        boolean inside = isSymmetric(left.right, right.left); // 左子树：右、 右子树：左
        boolean isSame = outside && inside;                   // 左子树：中、 右子树：中 （逻辑处理）
        return isSame;
    }

}
```

### 思路二：迭代实现

```java
class Solution {
    // 试着使用迭代实现以下
    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        // 使用队列
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root.left);
        queue.add(root.right);
        while (!queue.isEmpty()) {
            TreeNode left = queue.poll();
            TreeNode right = queue.poll();
            // 若两个节点均为空，则继续循环
            if (left == null && right == null) {
                continue;
            }
            // 左右节点有一个为空，或者值不相等，返回false
            if (left == null || right == null || left.val != right.val) {
                return false;
            }
            // 注意孩子节点的添加顺序
            queue.add(left.left);
            queue.add(right.right);
            queue.add(left.right);
            queue.add(right.left);
        }
        return true;
    }

}
```



## 100. 相同的树

> 题目：给定两个二叉树，编写一个函数来检验它们是否相同。如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

### 思路一：前序遍历

- 整体思路与对称二叉树的思路相同；只是在对称二叉树的左右节点递归顺序的位置发生了变化；

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        // 两个节点均为空，返回true
        if (p == null && q == null) {
            return true;
        }
        // 两个节点有一个为空，返回false
        if (p == null || q == null) {
            return false;
        }
        // 两个节点均不为空并且两个节点的值不相同，返回false
        if (p.val != q.val) {
            return false;
        }
        // 两个节点均不为空切两个节点的值相同
        boolean leftFlag = isSameTree(p.left, q.left);
        boolean rightFlag = isSameTree(p.right, q.right);
        return leftFlag && rightFlag;
    }
}
```



## 572.  另一个树的子树

> 题目：给定两个非空二叉树s和t，检验s中是否包含和t具有相同结构和节点值的子树。s的一个子树包括s个一个节点和这个节点的所有子孙。s也可以看做它自身的一棵子树。

### 思路一：前序遍历框架

- 需要注意的是，s中的节点与t的根节点相同的时候，就需要递归比较s的子树和t是否相同。

```java
class Solution {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        if (t == null) {
            return true;
        }
        if (s == null) {
            return false;
        }
        if (isSameTree(s, t)) {
            return true;
        } else {
            return isSubtree(s.left, t) || isSubtree(s.right, t);
        }

    }

    // 判断是否是相同子树，前序遍历,100题代码的精简
    public boolean isSameTree (TreeNode t1, TreeNode t2) {
        if (t1 == null && t2 == null) {
            return true;
        }
        if (t1 == null || t2 == null) {
            return false;
        }
        if (t1.val != t2.val) {
            return false;
        }
        return isSameTree(t1.left, t2.left) && isSameTree(t1.right, t2.right);
    }

}
```











## 104. 二叉树的最大深度

> 题目：给定一个二叉树，找出其最大深度。二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。
>
> 说明：叶子节点是指没有子节点的节点。

- **树的最大深度就是树的层数**

### 思路一：层序遍历(迭代)(Myself)

```java
class Solution {
    // 层序遍历，每次每一层的节点出队
    public int maxDepth(TreeNode root) {
        int res = 0;//最大深度
        if (root == null) {
            return res;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i++) {
                TreeNode temp = queue.poll();
                if (temp.left != null) {
                    queue.add(temp.left);
                }
                if (temp.right != null) {
                    queue.add(temp.right);
                }
            }
            res  = res + 1;

        }
        return res;
    }
}
```

### 思路二：后序遍历

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);
        int res = 1 + Math.max(leftDepth, rightDepth);
        return res;
    }
}
```



## 559. N叉树的最大深度

> 题目：给定一个N叉树，找到其最大深度。最大深度是指从根节点到最远叶子节点的最长路径上的节点综述。
>
> N叉树输入按层序遍历序列化表示，每组子节点由空值分隔。

- **注意：树的最大深度就是树的层数**

### 思路一：N叉树的层序遍历

```java
class Solution {
    // N叉树的层序遍历
    public int maxDepth(Node root) {
        int res = 0;
        if (root == null) {
            return res;
        }
        Deque<Node> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i++) {
                Node temp = queue.poll();
                for (Node node: temp.children) {
                    queue.add(node);
                }
            }
            res = res + 1;
        }
        return res;
    }
}
```

### 思路二：N叉树的后序遍历

```java
class Solution {
    public int maxDepth(Node root) {
        int res = 0;
        if (root == null) {
            return res;
        }
        List<Integer> list = new ArrayList<>();
        for (Node node: root.children) {
            list.add(maxDepth(node));            
        }
        res = maxNum(list) + 1;
        return res;
    }

    // 寻找ArrayList中的最大值
    private int maxNum (List<Integer> list) {
        if (list.size() == 0) {
            return 0;
        }
        int res = list.get(0);
        for (int i = 0; i < list.size(); i++) {
            if (list.get(i) > res) {
                res = list.get(i);
            }
        }
        return res;
    }

}
```



## 111. 二叉树的最小深度(要找到叶子节点)

> 题目：给定一个二叉树，找出其最小深度。最小深度是从根节点到最近叶子节点的最短路径上的节点数量。
>
> 说明：叶子节点是指没有子节点的节点。

- 该题目中需要注意的问题：二叉树的最小深度需要注意节点是否是叶子节点；

### 思路一：二叉树的层序遍历

- 在层序遍历的基础上，要增加对二叉树节点是否是叶子节点的判断；当层序遍历到达叶子节点的时候，就可以返回最小深度值。

```java
class Solution {
    // 层序遍历
    public int minDepth(TreeNode root) {
        int res = 0;
        if (root == null) {
            return res;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            res = res + 1;
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i++) {
                TreeNode temp = queue.poll();
                // 判断叶子节点
                if (temp.left == null && temp.right == null) {
                    return res;
                }
                if (temp.left != null) {
                    queue.add(temp.left);
                }
                if (temp.right != null) {
                    queue.add(temp.right);
                }
            }
        }
        return res;
    }
}
```

### 思路二：后序遍历(递归代码改进)

- 处理单层逻辑的时候，分情况讨论：该节点只有左孩子节点、该节点值只有右孩子节点、该节点有左右孩子节点

```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftDepth = minDepth(root.left);
        int rightDepth = minDepth(root.right);
        // 若该节点只有左孩子节点,保证最后返回的节点深度是左孩子深度即可
        if (root.left != null && root.right == null) {
            rightDepth = leftDepth + 1;
        }
        // 若该节点只有右孩子节点,保证最后返回的节点深度是右孩子深度即可
        if (root.right != null && root.left == null) {
            leftDepth = rightDepth + 1;
        }
        int res = Math.min(leftDepth, rightDepth) + 1;
        return res;
    }
}
```



## 222. 完全二叉树的节点个数

> 题目：给出一个完全二叉树，求出该树的节点个数。

- 完全二叉树：完全二叉树的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点树都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第h层，则该层包含1~2^h个节点。

### 思路一：层序遍历

```java
class Solution {
    // 层序遍历统计节点个数
    public int countNodes(TreeNode root) {
        int res = 0;
        if (root == null) {
            return res;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i++) {
                TreeNode temp = queue.poll();
                if (temp.left != null) {
                    queue.add(temp.left);
                }
                if (temp.right != null) {
                    queue.add(temp.right);
                }
                res = res + 1;
            }
        }
        return res;
    }
}
```



### 思路二：后序遍历(递归)

- 处理单层逻辑时，`int res = leftNum + rightNum + 1;`

```java
class Solution {
    // 后序遍历，递归实现
    public int countNodes(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftNum = countNodes(root.left);
        int rightNum = countNodes(root.right);
        int res = leftNum + rightNum + 1;
        return res;
    }
}
```



## 110. 平衡二叉树

> 题目：给定一个二叉树，判断它是否是高度平衡的二叉树。

- 本题中，一棵高度平衡二叉树定义为：一个二叉树每个节点的左右两个子树的高度差的绝对值不超过1.
- **二叉树结点的深度**：指从根节点到该节点的最长简单路径边的条数；
- **二叉树结点的高度**：指从该节点到叶子节点的最长简单路径边的条数；
- LeetCode 中强调的深度和高度很明显是按照节点来计算的

![image-20210112222528097](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210112222528097.png)

### 思路一：后序遍历(代码随想录)

- 比较高度，后序遍历；
- 递归函数：返回当前节点为根节点的高度或者返回-1表示已经不是二叉树; 递归过程中即判定高度差也返回高度。

- 递归第一步：明确递归函数的参数和返回值

```shell
# 参数为传入的节点指针，就没有其他参数需要传递了，返回值要返回传入节点为根节点树的深度。
# 如何标记左右子树是否差值大于1呢？
# 即如果当前传入节点为根节点的二叉树已经不是平衡二叉树了，那么就不需要返回根节点的高度。
# 所以如果已经不是二叉平衡树了，可以返回 -1，来标记已经不符合平衡树的规则了。
```

- 递归第二步：明确终止条件

```shell
# 递归的过程中依然是遇到了空节点为终止，返回0，表示当前节点为根节点的高度为0
```

- 递归第三步：明确单层递归的逻辑

```shell
# 分别求出左右子树的高度，若差值小于等于1，则返回当前二叉树的高度，否则返回 -1，表示已经不是平衡二叉树了。
```

```java
class Solution {
    // 后序遍历
    public boolean isBalanced(TreeNode root) {
        return getDepth(root) == -1 ? false : true;
    }

    // 递归函数：返回当前节点为根节点的高度或者返回-1表示已经不是二叉树
    // 递归过程中即判定高度差也返回高度
    private int getDepth (TreeNode node) {
        if (node == null) {
            return 0;
        }
        int leftDepth = getDepth(node.left);
        if (leftDepth == -1) {
            return -1;
        }
        int rightDepth = getDepth(node.right);
        if (rightDepth == -1) {
            return -1;
        }
        int res = 0;
        if (Math.abs(leftDepth - rightDepth) >1) {
            res = -1;
        } else {
            res = Math.max(leftDepth, rightDepth) + 1;
        }
        return res;
    }

}
```





## 404.  左叶子之和

> 题目：计算给定二叉树的所有左叶子之和。叶子节点是左叶子节点的和。

- 左叶子定义：若左节点不为空，且左节点没有左右孩子，那么这个节点就是左叶子；

### 思路一：后序遍历(Myself)

- 递归过程中，将节点是否是左节点这个flag带上；若节点为左节点且是叶子节点，那么就返回左叶子节点的值；

```java
class Solution {
    // 后序遍历框架 + 判断节点是否是左节点
    // 0 代表左节点，1 代表右节点
    public int sumOfLeftLeaves(TreeNode root) {
        int res = 0;
        if (root == null) {
            return res;
        }
        res = sumOfLeftLeaves(root, -1);
        return res;
    }

    // 递归求左叶子节点的和, flag 判断是否是左节点
    public int sumOfLeftLeaves(TreeNode node, int flag) {
        if (node == null) {
            return 0;
        }
        if (node.left == null && node.right == null && flag == 0) {
            return node.val;
        }
        int sum1 = sumOfLeftLeaves(node.left, 0);
        int sum2 = sumOfLeftLeaves(node.right, 1);
        return sum1 + sum2;
    }

}
```

### 思路二：后序遍历(递归)(代码随想录)

- 递归第一步：确定参数和返回条件；传入根节点，返回左叶子的和
- 递归第二步：递归结束的条件： root == null
- 递归第三步：递归的单层逻辑，后序遍历则先递归左、右节点，在中间节点的处理上，要根据父节点来判断是否是左叶子

```java
class Solution {
    // 后序遍历框架，是否是左叶子需要由父节点同步判断
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int num1 = sumOfLeftLeaves(root.left);
        int num2 = sumOfLeftLeaves(root.right);
        int midValue = 0;
        if (root.left != null && root.left.left == null && root.left.right == null) {
            midValue = root.left.val;
        }
        int res = midValue + num1 + num2;
        return res;
    }
}
```

### 思路三：后序遍历(迭代实现)(代码随想录)

```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        int res = 0;
        if (root == null) {
            return res;
        }
        // 迭代实现后序遍历
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode temp = stack.peek();
            stack.pop();
            if (temp.left != null && temp.left.left == null && temp.left.right == null) {
                res = res + temp.left.val;
            }
            if (temp.right != null) stack.push(temp.right);
            if (temp.left != null) stack.push(temp.left);
        }
        return res;
    }
}
```



## 513.  找树左下角的值

> 题目：给定一个二叉树，在树的最后一行找到最左边的值。

### 思路一：层序遍历 + 改进

- 思路就是在层序遍历的框架基础上，先将右节点入队，再将左节点入队。这样出队过程中，最后一个节点就是每一层的最左节点。

```java
class Solution {
    // 层序遍历的改进，右节点先入队，则最后出队的就是最左节点
    public int findBottomLeftValue(TreeNode root) {
        int res = 0;
        if (root == null) {
            return 0;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int layerNum = queue.size();
            for (int i = 1; i <= layerNum; i++) {
                TreeNode temp = queue.poll();
                if (i == layerNum) {
                    res = temp.val;
                }
                if (temp.right != null) queue.add(temp.right);
                if (temp.left != null) queue.add(temp.left); 
            }
        }
        return res;
    }
}
```



## 257. 二叉树的所有路径

> 题目：给定一个二叉树，返回所有从根节点到叶子节点的路径。说明：叶子节点是指没有子节点的节点。

### 思路一：前序遍历(mysel)

- 递归参数和返回值：参数需要遍历的节点Node、从根节点开始的String、存储路径结果的list，无需返回值；

- 递归终止条件：遍历到达叶子节点，将叶子节点的值加入传来的String后放入list中；
- 递归的单层逻辑：添加当前节点node的值和结果中需要的“->"；注意如果当前节点的左右节点为空则无需递归；因为已经判断过不是叶子节点了。
- 每次都是一个新的String放入list中用字符串就可以不用回溯；

```java
class Solution {
    // 前序遍历
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        treePaths(root, "", res);
        return res;
    }
    // 遍历到根节点时将String加入到list中，无返回值
    public void treePaths(TreeNode node, String string, List<String> list) {
        if (node.left == null && node.right == null) {
            String temp = string + node.val + "";
            list.add(temp);
            return;
        }
        String temp = string + node.val + "->";
        if (node.left != null) treePaths(node.left, temp, list);
        if (node.right != null) treePaths(node.right, temp, list);
    }
}
```

### 思路二：前序遍历 + 回溯算法(代码随想录)

- 利用StringBuilder来回溯，到叶子节点就toString + value 值，否则左右节点加入序列后需要删除；
- 遍历左右节点的时候，要加入回溯，StringBuilder.delete(StringBuilder递归前的长度, StringBuilder递归后的长度);
- 这道题目要求从根节点到叶子节点的路径，所以需要前序遍历；
- 在这道题目中将第一次涉及到回溯，因为我们要把路径记录下来，需要回溯来回退一个路径再进入另一个路径。

![image-20210112234434240](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210112234434240.png)

```java
class Solution {
    // 前序遍历 + 回溯
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        StringBuilder builder = new StringBuilder();
        if (root == null) {
            return res;
        }
        treePath(root, builder, res);
        return res;

    }
    // 前序遍历将所有路径加入paths中，有回溯
    public void treePath(TreeNode node, StringBuilder cur, List<String> paths) {
        // 遍历到达叶子节点
        if (node.left == null && node.right == null) {
            String temp = cur.toString() + node.val;
            paths.add(temp);
            return;
        }
        cur.append(node.val).append("->");
        // 遍历左右节点 + 回溯
        if (node.left != null) {
            int curLen = cur.length();
            treePath(node.left, cur, paths);
            cur.delete(curLen, cur.length());
        }
        if (node.right != null) {
            int curLen = cur.length();
            treePath(node.right, cur, paths);
            cur.delete(curLen, cur.length());
        }
    }
}
```



## 112. 路径总和

> 题目：给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点的值相加等于目标和。
>
> 说明：叶子节点是指没有子节点的节点。

### 思路一：遍历所有到叶节点的路径

- 遍历所有到叶节点的路径并存在Set集合中，查找set中是否包含要查找的目标和。
- 对叶子节点的操作： if (node == null) ...(这一步判断可以省去递归时对null的判断)  if (node.left == null && node.right == null) ...(真正到叶节点的操作) 

```java
class Solution {
    // 找到二叉树所有到根节点的路径和，看是否包含sum
    public boolean hasPathSum(TreeNode root, int sum) {
        Set<Integer> set = new HashSet<>();
        treePaths(root, 0, set);
        if (set.contains(sum)) {
            return true;
        } else {
            return false;
        }
    }
    // 递归记录所有到叶节点的和
    private void treePaths(TreeNode node, int sum, Set<Integer> set) {
        if (node == null) {
            return;
        }
        // 递归到叶子节点
        if (node.left == null && node.right == null) {
            sum = sum + node.val;
            set.add(sum);
        }
        sum = sum + node.val;
        treePaths(node.left, sum, set);
        treePaths(node.right, sum, set);
    }
}
```

### 思路二：递归 + 回溯(代码随想录)

- 递归中带一个计数器，初始化为sum值；每次遍历到一个节点就减该节点的值；
- 如果到达叶子节点求出的和不是sum即count != 0，那么就需要回溯；
- 在递归开始的时候，就将root节点的val减去，即 countDown(root, sum - root.val)，这样在单层逻辑的时候，就可以直接从左右节点开始递归。

```java
class Solution {
    // 递归 + 回溯
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) {
            return false;
        }
        return countDown(root, sum - root.val);
    }

    // 判断是否有路径和为sum即使得count为0
    public boolean countDown(TreeNode node, int count) {
        // 递归到叶子节点则判断返回
        if (node.left == null && node.right == null && count == 0) {
            return true;
        }
        if (node.left == null && node.right == null) {
            return false;
        }
        // 非叶子节点，递归判断 +回溯；不需要遍历所有二叉树的节点的时候，可以用返回值；
        if (node.left != null) {
            count = count - node.left.val;
            if (countDown(node.left, count)) {
                return true;
            }
            count = count + node.left.val;
        }
        if (node.right != null) {
            count = count - node.right.val;
            if (countDown(node.right, count)) {
                return true;
            }
            count = count + node.right.val;
        }
        // 左右子树都没有返回，则返回false；
        return false;
    }

}
```



## 113.  路径总和 ||

> 题目：给定一个二叉树和一个目标和，找到所有从根节点到叶子节点没路径总和等于给定目标和的路径。
>
> 说明：叶子节点是指没有子节点的节点。

- 如果不需要遍历树的所有节点或者路径，则可以添加返回值；否则，若要遍历所有的路径则不能有返回值；
- **如果需要搜索整颗二叉树，那么递归函数就不需要返回值。如果搜索其中一条符合条件的路径，递归函数就需要返回值，因为遇到符合条件的路径就要及时返回。**

### 思路一：前序遍历 + 回溯

- Java 的按值传递和按地址传递区别，找到路径添加到结果集result中时，new ArrayList();

```java
class Solution {
    // 前序遍历整个二叉树
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if (root == null) {
            return res;
        }
        List<Integer> path = new ArrayList<>();
        path.add(root.val);
        treePath(root, sum - root.val, path, res);
        return res;
    }
    // 前序遍历 + 回溯
    private void treePath(TreeNode node, int countNum, List<Integer> path, List<List<Integer>> res) {
        if (node.left == null && node.right == null && countNum == 0) {
            res.add(new ArrayList(path));
            return;
        }
        if (node.left == null && node.right == null) {
            return;
        }
        // 遍历左右节点 + 回溯
        if (node.left != null) {
            countNum = countNum - node.left.val;
            path.add(node.left.val);
            treePath(node.left, countNum, path, res);
            path.remove(path.size()-1);
            countNum = countNum + node.left.val;
        }
        if (node.right != null) {
            countNum = countNum - node.right.val;
            path.add(node.right.val);
            treePath(node.right, countNum, path, res);
            path.remove(path.size()-1);
            countNum = countNum + node.right.val;
        }
        return;
    }

}
```



## 106.  从中序与后序遍历序列构造二叉树

> 题目：根据一棵树的中序遍历与后序遍历构造二叉树。注意：假设树中没有重复元素。

### 思路一：前序遍历

- 后序遍历序列的最后一个值就是该子树的根节点，再通过中序遍历根节点的位置区分出左右子树的节点个数，递归下去直到构建完成。
- 判断 inorder.length == 0 && postorder.length == 0，返回null；
- 如何根据两个顺序构造一个唯一的二叉树，以后序数组的最后一个元素为切割点，先切中序数组，根据中序数组，反过来再切后序数组，一层一层切下去，每次后序数组最后一个元素就是节点元素。
- 递归结束的条件：后序遍历的 StartIndex  >  EndIndex；

```java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        if (inorder.length == 0 && postorder.length == 0) {
            return null;
        }
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        int length = inorder.length;
        TreeNode root = buildTree(inorder, 0, length - 1, postorder, 0, length - 1, map);
        return root;
    }

    // 递归找到中序和后序遍历中的根节点
    private TreeNode buildTree(int[] inorder, int inStartIndex, int inEndIndex, int[] postorder, int postStartIndex, int postEndIndex, Map<Integer, Integer> map) {
        if (postStartIndex > postEndIndex) {
            return null;
        }
        int rootValur = postorder[postEndIndex];
        TreeNode root = new TreeNode(rootValur);
        int rootIndexInInorder = map.get(rootValur); //根节点在中序遍历序列中的index
        int leftTreeNum = rootIndexInInorder - inStartIndex;            // 左子树节点的个数
        int rightTreeNum = inEndIndex - rootIndexInInorder;             // 右子树节点的个数
        TreeNode leftNode = buildTree(inorder, inStartIndex, rootIndexInInorder-1, postorder,postStartIndex, postStartIndex+leftTreeNum-1, map);
        TreeNode rightNode = buildTree(inorder, rootIndexInInorder+1, inEndIndex, postorder, postEndIndex-rightTreeNum, postEndIndex-1, map);
        root.left = leftNode;
        root.right = rightNode;
        return root;
    }
}
```



## 105.  从前序与中序遍历序列构造二叉树

> 题目：根据一棵树的前序遍历与中序遍历构造二叉树。注意：你可以假设树中没有重复的元素。

### 思路一：前序遍历

- 树的前序遍历的第一个节点是该树的根节点，找到根节点后根据中序遍历序列就可以找到左右子树；这样递归下去直到构建完成。

- **注意左右子节点的前序和中序区间值**；

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder.length == 0 && inorder.length == 0) {
            return null;
        }
        Map<Integer, Integer> inorderMap = new HashMap<>();
        int length = inorder.length;
        for (int i = 0; i < length; i++) {
            inorderMap.put(inorder[i], i);
        }
        TreeNode root = buildTree(preorder, 0, length - 1, inorder, 0, length - 1, inorderMap);
        return root;
    }

    // 通过前序和中序序列找到根节点
    private TreeNode buildTree(int[] preorder, int preStartIndex, int preEndIndex, int[] inorder, int inStartIndex, int inEndIndex, Map<Integer, Integer> map) {
        if (preStartIndex > preEndIndex) {
            return null;
        }
        int rootValue = preorder[preStartIndex];
        TreeNode root = new TreeNode(rootValue);
        int rootIndexInInorder = map.get(rootValue);
        int leftNum = rootIndexInInorder - inStartIndex;    // 左子树节点个数
        int rightNum = inEndIndex - rootIndexInInorder;     // 右子树节点个数
        root.left = buildTree(preorder, preStartIndex+1, preStartIndex+leftNum, inorder, inStartIndex, rootIndexInInorder-1, map);
        root.right = buildTree(preorder, preEndIndex-rightNum+1, preEndIndex, inorder, rootIndexInInorder+1, inEndIndex, map);
        return root;
    }

}
```

```shell
# 思考题：
	前序和中序可以唯一确定一棵二叉树，后序和中序可以唯一确定一棵二叉树，那么前序和后序可不可以唯一确定一棵二叉树呢？
	答：前序和后序不能唯一确定一棵二叉树，因为没有中序遍历无法确定左右部分，也就是无法分割。
```

![image-20210114120102629](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210114120102629.png)



## 654.  最大二叉树

> 题目：给定一个不含重复元素的整数数组nums。一个以此数组直接递归构建的最大二叉树定义如下：
>
> ​	1.二叉树的根是数组nums中的最大元素；2.左子树是通过数组中最大值左边部分递归构造出的最大二叉树；
>
> ​	3.右子树是通过数组中最大值右边部分递归构造出的最大二叉树。

### 思路一：前序遍历

- 构造二叉树，就是用前序遍历即可；
- 这个题的思路比较清晰，找到构建二叉树数组的最大值及其对应的下标index，然后分为左右子树进行递归。递归结束的条件为 startIndex  >  endIndex;

```java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        if (nums.length == 0) {
            return null;
        }
        int length = nums.length;
        // 将数组值和对应的下标存入Map中
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < length; i++) {
            map.put(nums[i], i);
        }
        TreeNode root = constructTree(nums, 0, length - 1, map);
        return root;
    }

    // 通过nums数组递归构建最大二叉树
    private TreeNode constructTree(int[] nums, int startIndex, int endIndex, Map<Integer, Integer> map) {
        // 递归结束的条件
        if (startIndex > endIndex) {
            return null;
        }
        int maxNum = maxValue(nums, startIndex, endIndex);
        TreeNode root = new TreeNode(maxNum);
        int maxIndex = map.get(maxNum);
        root.left = constructTree(nums, startIndex, maxIndex-1,map);
        root.right = constructTree(nums, maxIndex+1, endIndex, map);
        return root;
    }

    // 找数组中的最大值
    private int maxValue(int[] nums, int startIndex, int endIndex) {
        int res = nums[startIndex];
        for (int i = startIndex; i <= endIndex; i++) {
            if (nums[i] > res) {
                res = nums[i];
            }
        }
        return res;
    }

}
```

**代码简化**

- 看完公众号解析后，恍然大悟很多工作都是重复的；所以要将代码简化一下。
- 数组中的最大值及其对应的下标，可以在递归中直接找到；不需要新建函数那么复杂；

```java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        if (nums.length == 0) {
            return null;
        }
        int length = nums.length;
        TreeNode root = constructTree(nums, 0, length - 1);
        return root;
    }

    // 通过nums数组递归构建最大二叉树
    private TreeNode constructTree(int[] nums, int startIndex, int endIndex) {
        // 递归结束的条件
        if (startIndex > endIndex) {
            return null;
        }
        int maxNum = nums[startIndex];
        int maxIndex = startIndex;
        for (int i = startIndex; i <= endIndex; i++) {
            if (nums[i] > maxNum) {
                maxNum = nums[i];
                maxIndex = i;
            }
        }
        TreeNode root = new TreeNode(maxNum);
        root.left = constructTree(nums, startIndex, maxIndex-1);
        root.right = constructTree(nums, maxIndex+1, endIndex);
        return root;
    }

}
```



## 617.  合并二叉树

> 题目：给定两个二叉树，想想当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为NULL的节点将直接作为新二叉树的节点

### 思路一：前序遍历(递归)

- 将Tree2 节点的节点值加到Tree1 节点上，返回Tree1节点即可；

```java
class Solution {
    // 把tree2 的节点值直接加到tree1 上，前序遍历
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        // t1 == null && t2 == null 时，返回t1 或 t2 都可以，所以这个条件可以取消
//        if (t1 == null && t2 == null) {
//            return null;
//        }
        if (t1 == null) {
            return t2;
        }
        if (t2 == null) {
            return t1;
        }
        // 两个节点都不为null
        t1.val = t1.val + t2.val;
        t1.left = mergeTrees(t1.left, t2.left);
        t1.right = mergeTrees(t1.right, t2.right);
        return t1;
    }
}
```



### 思路二：层序遍历(迭代)

- 若Tree1 和 Tree2 的层序遍历节点均不为null，则入队列求两个节点的和赋给Tree1；若有一个节点为null，则直接赋值即可，不入队；若两个节点都为null，则不入队；

```java
class Solution {
    // 迭代实现前序遍历，仍是把t2 合并到 t1
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null) {
            return t2;
        }
        if (t2 == null) {
            return t1;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.add(t1);
        queue.add(t2);
        while (!queue.isEmpty()) {
            TreeNode node1= queue.poll();
            TreeNode node2 = queue.poll();
            node1.val = node1.val + node2.val;
            // 左节点均不为null
            if (node1.left != null && node2.left != null) {
                queue.add(node1.left);
                queue.add(node2.left);
            }
            // 右节点均不为null
            if (node1.right != null && node2.right != null) {
                queue.add(node1.right);
                queue.add(node2.right);
            }
            // 左、右节点有一个为null，直接赋值,不入队
            if (node1.left == null && node2.left != null) {
                node1.left = node2.left;
            }
            if (node1.right == null && node2.right != null) {
                node1.right = node2.right;
            }
         // 其余情况：node1的左右节点不为空，但对应的node2的左右节点为null，或者node1 和 node2 的左右节点同时为null，不处理
        }
        return t1;
    }
}
```



## 700. 二叉搜索树中的搜索

- 二叉搜索树搜索过程相当于有了条件，可以选择左子树或右子树搜索；

> 题目：给定二叉搜索树(BST)的根节点和一个值。你需要在BST中找到节点值等于给定值的节点。返回以该节点为根的子树。如果节点不存在，则返回null；

### 思路一：前序遍历 + BST特性

```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        if (root == null) {
            return null;
        }
        if (root.val == val) {
            return root;
        } else if (root.val > val) {
            // 左子树中查询
            TreeNode res = searchBST(root.left, val);
            if (res != null) {
                return res;
            }
        } else {
            // 右子树中查询
            TreeNode res = searchBST(root.right, val);
            if (res != null) {
                return res;
            }
        }
        return null;
    }
}
```

二叉搜索树是一个有序树：

- 若它的左子树不空，则左子树上所有结点的值均小于它的根节点的值；
- 若它的右子树不空，则右子树上所有结点的值均大于它的根节点的值；
- 它的左、右子树也分别为二叉搜索树。

- **递归当搜索到目标节点的时候立即返回，就需要有返回值**

代码简化----看枯了

```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        if (root == null || root.val == val) {
            return root;
        }
        if (root.val > val) {
            return searchBST(root.left, val);
        }
        if (root.val < val) {
            return searchBST(root.right, val);
        }
        return null;
    }
}
```



### 思路二：前序遍历 + BST特性(迭代)

- 当确定要左或者右节点入队的时候，要判断节点是否为null；

```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        Deque<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode temp = stack.pop();
            if (temp.val == val) {
                return temp;
            } else if (temp.val > val && temp.left != null) {
                stack.push(temp.left);
            } else if (temp.val < val && temp.right != null) {
                stack.push(temp.right);
            }
        }
        return null;
    }
}
```



## 98.  验证二叉搜索树

- 验证二叉搜索树，就相当于变成了判断一个序列是不是递增的了。

> 题目：给定一个二叉树，判断其是否是一个有效的二叉搜索树。

### 思路一：中序遍历序列是否递增

- 将二叉树转变为数组来判断；

- 二叉搜索树中序遍历是否是递增序列，来判断是否是二叉搜索树。

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        inOrder(root, list);
        for (int i = 1; i < list.size(); i++) {
            if (list.get(i) <= list.get(i-1)) {
                return false;
            }
        }
        return true;
    }

    // 二叉树中序遍历放入list中
    public void inOrder(TreeNode node, List<Integer> list) {
        if (node == null) {
            return;
        }
        inOrder(node.left, list);
        list.add(node.val);
        inOrder(node.right, list);
    }

}
```



### 思路二：中序遍历过程中判断

- 陷阱1：不能单纯比较左节点小于中间节点，右节点大于中间节点就完事了。**要比较的是，左子树所有节点小于中间节点，右子树所有节点大于中间节点**
- 陷阱2：样例中最小节点，可能是int的最小值；
- 思路：定义一个全局变量，二叉树中序遍历过程中看这个全局变量是不是从小到大；
- 测试用例中有Integer的最小值---处理方法：全局变量maxValue定义为long类型；

```java
class Solution {
    // 中序遍历，定义全局变量
    long maxValue = Long.MIN_VALUE;
    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        boolean left = isValidBST(root.left);
        if (root.val > maxValue) {
            maxValue = root.val;
        } else {
            return false;
        }
        boolean right = isValidBST(root.right);
        return left && right;
    }
}
```



## 530. 二叉搜索树的最小绝对差

## 783. 二叉搜索树节点最小距离

> 题目：给你一颗所有节点为非负值的二叉搜索树，请你计算树中任意两节点的差的绝对值的最小值。

- 差的绝对值的最小值就在相邻两个节点之间的绝对值差之间，所以总的思路还是中序遍历。

### 思路一：中序遍历序列 + List相邻值比较

### 思路二：中序遍历过程中找结果(List存储)

- 中序遍历的第一个节点和其余节点的处理是不一样的，第一个节点不能做减法，而是赋值操作；所以就用一个List判断是否是空来衡量第一个节点；

```java
class Solution {
    // 中序遍历
    int res = Integer.MAX_VALUE;
    public int getMinimumDifference(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        findMinValue(root, list);
        return res;
    }
    // 递归寻找差的绝对值最小值
    private void findMinValue(TreeNode node, List<Integer> list) {
        if (node == null) {
            return;
        }
        findMinValue(node.left, list);
        list.add(node.val);
        int size = list.size();
        if (size > 1) {
            int difference = Math.abs(node.val - list.get(size-2));
            if (difference < res) {
                res = difference;
            }
        }
        findMinValue(node.right, list);
    }
}
```

### 思路三：中序遍历过程中找结果(存储前一个节点)

- 掌握如何在遍历过程中存储前一个节点：定义全局变量 `TreeNOde pre = null`

```java
class Solution {
    // 中序遍历
    int res = Integer.MAX_VALUE;
    // 存储遍历过程中的前一个节点
    TreeNode pre = null;
    public int getMinimumDifference(TreeNode root) {
        findMinValue(root);
        return res;
    }
    // 递归寻找差的绝对值最小值
    private void findMinValue(TreeNode node) {
        if (node == null) {
            return;
        }
        findMinValue(node.left);
        if (pre != null) {
            int difference = Math.abs(node.val - pre.val);
            if (difference < res) {
                res = difference;
            }
        }
        pre = node;
        findMinValue(node.right);
    }
}
```



## 501. 二叉搜索树中的众数

> 题目：给定一个有相同值的二叉搜索树(BST)，找出BST中的所有众数(出现频率最高的元素)。
>
> 如果众数超过1个，不需考虑输出顺序。

### 思路一：普通二叉树 + 遍历 + 众数

- 普通二叉树，就需要遍历整个树，统计节点值和频率；并找出频率最大的几个值；

```java
class Solution {
    // 普通二叉树，任意顺序遍历+存储Map+Map中找众数
    public int[] findMode(TreeNode root) {
        // key为节点值，value为频次
        Map<Integer, Integer> map = new HashMap<>();
        findMode(root, map);
        int maxFreq = 0; // 存放节点出现的最高频率
        List<Integer> res = new ArrayList<>();
        for (Integer temp: map.keySet()) {
            if (map.get(temp) > maxFreq) {
                maxFreq = map.get(temp);
            }
        }
        for (Integer temp: map.keySet()) {
            if (map.get(temp) == maxFreq) {
                res.add(temp);
            }
        }
        int[] array = new int[res.size()];
        for (int i = 0; i < array.length; i++) {
            array[i] = res.get(i);
        }
        return array;
    }
    // 将节点的对应值的出现频次放到map中
    private void findMode(TreeNode node, Map<Integer, Integer> map) {
        if (node == null) {
            return;
        }
        findMode(node.left, map);
        map.put(node.val, map.getOrDefault(node.val, 1)+1);
        findMode(node.right, map);
    }

}
```



### 思路二：二叉搜索树 + 遍历 + 众数

- 若树为二叉搜索树，则相同节点就存在中序遍历过程中；维护一个List存储结果集；
- 维护一个TreeNode指向前一个节点，维护一个变量存储同一节点值出现的频次；若遍历过程中某一个节点值的频次比之前的频次高，则清空结果集，并将该节点加入结果集中；
- **递归单层逻辑(比较难想到)**：修改当前节点频次  -->  当前节点频次与最大节点频次相同，则放入结果集中  -->  当前节点频次大于最大节点频次，删除原结果集，更新最大频次；

```java
class Solution {
    List<Integer> list = new ArrayList<>(); // 存储结果集
    TreeNode pre = null; // 存储前一个节点
    int freq = 0;       // 存储当前节点频次
    int maxFreq = 0;    // 存储最大节点频次
    public int[] findMode(TreeNode root) {
        findZhongShu(root);
        int[] res = new int[list.size()];
        for (int i = 0; i < res.length; i++) {
            res[i] = list.get(i);
        }
        return res;
    }
    // 中序遍历一次二叉搜索树
    private void findZhongShu(TreeNode node) {
        if (node == null) {
            return;
        }
        findZhongShu(node.left);
        // 相同节点频次的操作
        if (pre == null) {
            freq = 1;
        } else if (pre.val == node.val) {
            freq += 1;
        } else if (pre.val != node.val) {
            freq = 1;
        }
        pre = node;
        // 当前节点频次与最大节点频次相同，则放入结果集中
        if (freq == maxFreq) {
            list.add(node.val);
        }
        // 当前节点频次大于最大节点频次，删除原结果集，更新最大频次
        if (freq > maxFreq) {
            maxFreq = freq;
            list.clear();
            list.add(node.val);
        }
        
        findZhongShu(node.right);
    }

}
```



## 236.  二叉树的最近公共祖先*

> 题目：给定一个二叉树，找到该树中两个指定节点的最近公共祖先；
>
> 中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。深度尽可能大的意思就是尽量远离根节点；
>

```shell
参考链接：https://mp.weixin.qq.com/s/n6Rk3nc_X3TSkhXHrVmBTQ
```

### 思路：代码随想录

- 自底向上查找就可以找到公共祖先，自底向上查找即回溯，后序遍历就是天然的回溯过程，最先处理的一定是叶子节点。

- 如何判断一个节点是节点q和节点p的公共祖先，**如果找到一个节点，发现左子树出现节点p，右子树出现节点q或者左子树出现节点q右子树出现节点p，那么该节点就是节点p和q的公共祖先**，使用后序遍历，回溯的过程就是从底向上遍历节点，一旦发现这个条件的节点，就是最近公共节点了；

- 递归第一步：确定函数返回值以及参数

- 递归第二步：确定终止条件：当递归找到节点p或者节点q或者递归到底null，则返回；

- 递归第三步：确定递归单层逻辑：如果在root节点的左子树和右子树中找到p、q，那么就返回root节点；后序遍历过程中，需要利用left和right做逻辑处理后才可以返回(**注意与搜索一条边的区别**)。**「如果left 和 right都不为空，说明此时root就是最近公共节点。这个比较好理解」**

  **「如果left为空，right不为空，就返回right，说明目标节点是通过right返回的，反之依然」**。

```shell
递归函数有返回值，搜索一条边的写法：
	if (递归函数(root->left)) return ;
	if (递归函数(root->right)) return ;
递归函数有返回值，搜索整个树写法：
	left = 递归函数(root->left);
	right = 递归函数(root->right);
	left与right的逻辑处理;
	「在递归函数有返回值的情况下：如果要搜索一条边，递归函数返回值不为空的时候，立刻返回，如果搜索整个树，直接用一个变量left、right接住返回值，这个left、right后序还有逻辑处理的需要，也就是后序遍历中处理中间节点的逻辑（也是回溯）」
```

![image-20210116232811138](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210116232811138.png)

```java
class Solution {
    // 自底向上遍历，回溯，后序遍历框架
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == p || root == q || root == null) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left != null && right != null) {
            return root;
        }
        if (left == null && right != null) {
            return right;
        } else if (left != null && right == null) {
            return left;
        } else {
            return null;
        }
    }
}
```



## 235. 二叉搜索树的最近公共祖先

> 题目：给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

### 思路一：中序遍历 + 二叉搜索树特性

- 二叉搜索树找最近的公共祖先，只要判断节点值在两个节点中间即可，前提是中序遍历框架；
- 找到根节点后逐层递归返回，不需要遍历所有节点；
- 其实只要从上到下遍历的时候，cur节点是数值在[p, q]区间中则说明该节点cur就是最近公共祖先了。
- 和二叉树：公共祖先问题不同，普通二叉树求最近公共祖先需要使用回溯，从底向上来查找，二叉搜索树就不用了，因为搜索树有序（相当于自带方向），那么只要从上向下遍历就可以了。

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return null;
        }
        // 中
        if (root.val > p.val && root.val > q.val) { // 左
            TreeNode left = lowestCommonAncestor(root.left, p, q);
            if (left != null) {
                return left;
            }
        }
        if (root.val < p.val && root.val < q.val) { // 右
            TreeNode right = lowestCommonAncestor(root.right, p, q);
            if (right != null) {
                return right;
            }
        }

        return root;
    }
}
```



## 701.  二叉搜索树中的插入操作

> 题目：给定二叉搜索树(BST)的根节点和要插入树中的值，将值插入二叉搜索树。返回插入后二叉搜索树的根节点。输入数据保证，新值和原始二叉搜索树中的任意节点都不同。注意：可能存在多种有效的插入方式，只要在插入后扔保持为二叉搜索树即可。返回任意有效的结果。

### 思路一：比较root.val 和 val 大小，在适当位置插入即可

- 只要按照二叉搜索树的规则去遍历，遇到空节点就插入节点就可以了。
- 注意特殊情况：root == null时，创建新节点(val)返回；

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            root = new TreeNode(val);
            return root;
        }
        insert(root, val);
        return root;
    }

    private void insert(TreeNode node, int val) {
        if (node == null) {
            return;
        } else if (node.val < val && node.right == null){
            node.right = new TreeNode(val);
            return;
        } else if (node.val > val && node.left == null) {
            node.left = new TreeNode(val);
            return;
        }

        if (node.val < val) {
            insert(node.right, val);
        } else {
            insert(node.left, val);
        }
    }
    
}
```



### 思路二：代码随想录

- 只要遍历二叉搜索树，找到空节点插入元素就可以；
- **通过递归函数返回值完成了新加入节点的父子关系赋值操作了，下一层将加入节点返回，本层用root->left或者root->right将其接住**

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            TreeNode node = new TreeNode(val);
            return node;
        }
		// 用 root.left和root.right 接递归返回值即可
        if (root.val > val) {
            root.left = insertIntoBST(root.left, val);
        } else {
            root.right = insertIntoBST(root.right, val);
        }

        return root;
    }
}
```



## 450.  删除二叉搜索树中的节点

> 题目：给定二叉搜索树的根节点和一个值key，删除二叉树中的key对应的节点。并保证二叉搜索树的性质不变，返回二叉搜索树(有可能被更新)的根节点的引用。说明：要求算法时间复杂度为O(h), h为树的高度。

### 思路：代码随想录

```shell
# 二叉搜索树删除节点情况：
	1. 没找到删除的节点，遍历到空节点直接返回
	2. 删除叶子节点，直接返回null；
	3. 删除节点左孩子为null， 右孩子不为null，删除节点，右孩子补位，返回右孩子为根节点；
	4. 删除节点右孩子为null， 左孩子不为null， 删除节点，左孩子补位，返回左孩子为根节点；
	5. 左右孩子节点都不为空，则将删除节点的左子树头结点（左孩子）放到删除节点的右子树的最左面节点的左孩子上，返回删除节点右孩子为新的根节点。
```

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        // 第一种情况: 查到为null， 返回root
        if (root == null) {
            return root;
        }
        if (root.val == key) {
            // 删除叶子节点
            if (root.left == null && root.right == null) {
                root = null;
                return null;
            } else if (root.left != null && root.right == null) {
                return root.left;
            } else if (root.right != null && root.left == null) {
                return root.right;
            } else {
                // 左右节点都存在,找到右子树的最左节点，把该节点的左子树插到最左节点的左子树，
                // 返回最左节点
                TreeNode nextNode = root.right; // 右子树最左边节点
                while (nextNode.left != null) {
                    nextNode = nextNode.left;
                }
                nextNode.left = root.left;	// 删除节点的左子树放在nextNode的左孩子
                root = root.right;			// 返回删除节点的右子树
                return root;
            }
            
        }
        if (root.val > key) {
            root.left = deleteNode(root.left, key);
        }
        if (root.val < key) {
            root.right = deleteNode(root.right, key);
        }
        return root;
    }
}
```



## 669.  修剪二叉搜索树

> 题目：给你二叉搜索树的根节点 root ，同时给定最小边界low 和最大边界 high。通过修剪二叉搜索树，使得所有节点的值在[low, high]中。修剪树不应该改变保留在树中的元素的相对结构（即，如果没有被移除，原有的父代子代关系都应当保留）。 可以证明，存在唯一的答案。
>
> 所以结果应当返回修剪好的二叉搜索树的新的根节点。注意，根节点可能会根据给定的边界发生改变。

### 思路：前序遍历

- 确定单层递归逻辑：如果root（当前节点）的元素小于low的数值，那么应该递归右子树，并返回右子树符合条件的头结点。如果root(当前节点)的元素大于high的，那么应该递归左子树，并返回左子树符合条件的头结点。接下来要将下一层处理完左子树的结果赋给root->left，处理完右子树的结果赋给root->right。
- 即寻找下一个符合区间的节点，又用节点的左右值接返回结果；

![image-20210117110001474](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210117110001474.png)

![image-20210117110019125](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210117110019125.png)

```java
class Solution {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        if (root == null) {
            return null;
        }
        if (root.val < low) {
            TreeNode rightNode = trimBST(root.right, low, high);
            return rightNode;
        }   
        if (root.val > high) {
            TreeNode leftNode = trimBST(root.left, low, high);
            return leftNode;
        }
        root.left = trimBST(root.left, low, high);
        root.right = trimBST(root.right, low, high);
        return root;
    }
}
```



## 108. 将有序数组转换为二叉搜索树

> 题目：将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。本题中，一个高度平衡二叉树是指一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1。

### 思路：分治

- 找到有序数组的中间节点和左右子树区间，构建节点即可；

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        TreeNode res = buildBST(nums, 0, nums.length - 1);
        return res;
    }

    public TreeNode buildBST(int[] nums, int startIndex, int endIndex) {
        if (startIndex > endIndex) {
            return null;
        }
        int midIndex = (endIndex + startIndex) / 2;
        TreeNode mid = new TreeNode(nums[midIndex]);
        mid.left = buildBST(nums, startIndex, midIndex - 1);
        mid.right = buildBST(nums, midIndex + 1, endIndex);
        return mid;
    }

}
```



## 538.  把二叉搜索树转换为累加树

> 题目：给出二叉搜索树的根节点，该树的节点值各不相同，请你将其转换为累加树(Greater Sum Tree)，使每个节点node的新值等于原树中大于或等于node.val的值之和。

### 思路：中序遍历的变形-右中左(反中序遍历)

- 从二叉搜索树的最大节点开始遍历即可，用全局变量存储前一节点的值与该节点的值相加作为该节点的新值。

```java
class Solution {
    // 中序遍历，存储从大到小的节点值的和
    int sumValue = 0;
    public TreeNode convertBST(TreeNode root) {
        reverseTree(root);
        return root;
    }

    // 中序遍历的变形递归整棵树
    private void reverseTree(TreeNode node) {
        if (node == null) {
            return;
        }
        reverseTree(node.right);
        node.val = node.val + sumValue;
        sumValue = node.val;
        reverseTree(node.left);
    }

}
```









## 589. N叉树的前序遍历(前序遍历框架)

> 题目描述：给定一个N叉树，返回其节点值的前序遍历；

### 思路一：递归实现

```java
// 节点定义：
class Node{
    public int val;
    public List<Node> children;
    public Node(){};
    public Node(int _val){val = _val};
    public Node(int _val, List<Node> _children){
        val = _val;
        children = _children;
    }
}

class Solution {
    // 递归实现N叉树的遍历
    public List<Integer> preorder(Node root) {
        List<Integer> list = new ArrayList<>();
        preorder(root, list);
        return list;
    }

    // 递归遍历N叉树
    public void preorder(Node node, List<Integer> list){
        if(node == null){
            return;
        }
        list.add(node.val);
        for(Node temp: node.children){
            preorder(temp, list);
        }
    }
}
```

### 思路二：迭代实现(LeetCode)

> 迭代实现需要借助于Stack数据结构，前序遍历将节点children从右到左放入栈中，这样出栈的顺序就是从左到右；
>
> 和二叉树的前序遍历迭代方法思想一样，先处理根节点，然后从右往左把子节点加入栈中，这样出来的结果就是从左到右。
> 代码中用 for(int i = node.children.size() - 1; i >= 0; -- i) 来控制从右到左的进栈顺序。
> N叉树的前序遍历代码操作的顺序是，根节点—>子节点（从右到左进栈，出来的顺序为左到右），对应得到的遍历结果就是 根节点+子节点（从左到右）。

```java
class Solution {
    // 迭代实现
    public List<Integer> preorder(Node root) {
        List<Integer> list = new ArrayList<>();
        Deque<Node> stack = new LinkedList<>();
        if(root == null){
            return list;
        }
        stack.push(root);
        while(!stack.isEmpty()){
            Node temp = stack.pop();
            list.add(temp.val);
            // 前序遍历
            for(int i = temp.children.size() - 1; i >= 0; i --){
                stack.push(temp.children.get(i));
            }
        }
        return list;
    }
}
```

### 扩展：N叉树后序遍历(迭代)

> 树的后序遍历： 左  ->  右  ->  中
>
> 树的前序遍历： 中  ->  左  ->  右
>
> 在前序遍历的实现过程中，children的每个元素从右到左放入栈中，这样出栈的顺序就是从左到右；
>
> 后序遍历中，先利用前序遍历的思路，children中的每个元素从左到右放入栈中，这样出栈的顺序就是：
>
> 中  ->  右 ->  左，这样将最后的结果反转就可以得到  左  ->  右  ->  中 的后序遍历序列；
>
> LeetCode 解析：
>
> N叉树的前序遍历代码操作的顺序是，根节点—>子节点（从右到左进栈，出来的顺序为左到右），对应得到的遍历结果就是 根节点+子节点（从左到右）。
> N叉树的后序遍历代码操作的顺序是，根节点->子节点（从左到右进栈，出来的顺序为右到左），对应得到的遍历结果就是 根节点+子节点（从右到左）。再把这个结果反转一下，遍历结果就是 子节点（从左到右）+ 根节点。
> 代码中用 for(int i = 0; i < node.children.size(); ++ i) 来控制从左到右的进栈顺序。

```java
class Solution {
    // 迭代实现后序遍历
    public List<Integer> preorder(Node root) {
        List<Integer> list = new ArrayList<>();
        Deque<Node> stack = new LinkedList<>();
        if(root == null){
            return list;
        }
        stack.push(root);
        while(!stack.isEmpty()){
            Node temp = stack.pop();
            list.add(temp.val);
            // 从左到右放入栈中，出栈顺序从右到左
            for(int i = 0; i < temp.children.size(); i ++){
                stack.push(temp.children.get(i));
            }
        }
        Collections.reverse(list);
        return list;
    }
}
```



## 剑指Offer 54. 二叉搜索树的第k大节点(中序遍历框架)

> 题目：给定一棵二叉搜索树，找出其中第k大的节点；

### 思路一：中序遍历找倒数第k个节点

- 存储二叉搜索树中序遍历序列，找到第k 大的节点

```java
class Solution {
    public int kthLargest(TreeNode root, int k) {
        // 二叉搜索树中序遍历后找到第k大的节点
        List<Integer> list = new ArrayList<>();
        inOrder(root, list);
        int res = list.get(list.size()-k);
        return res;
    }

    public void inOrder(TreeNode node, List<Integer> list){
        if(node == null){
            return;
        }
        inOrder(node.left, list);
        list.add(node.val);
        inOrder(node.right, list);
    }

}
```

### 思路二：中序遍历倒序找第k个节点

> 二叉搜索树的中序遍历为递增序列，则二叉搜索树中序遍历倒序为递减序列；
>
> 求二叉搜索树第k大的节点可以转化为求中序遍历倒序的第k个节点；
>
> 中序遍历倒序：右  ->  中  ->  左

```java
class Solution {
    int res, k;
    public int kthLargest(TreeNode root, int k) {
        this.k = k;
        inOrder(root);
        return res;
    }

    public void inOrder(TreeNode node){
        if(node == null){
            return;
        }
        inOrder(node.right);
        k = k -1;
        // 中序遍历序列的倒序第k个节点
        if(k == 0){
            res = node.val;
            return;
        }
        inOrder(node.left);
    }

}
```



## 剑指Offer26.树的子结构(前序遍历框架)

> 题目：输入两棵二叉树A和B，判断B是不是A的子结构(约定空树不是任意一个树的子结构)；
>
> B是A的子结构，即A中有出现和B相同的结构和节点值；

### 思路：前序遍历+判断结构(剑指Offer)

- 查找数A中是否存在和树B结构一样的子树，分两步：
  - 遍历树A找到和B的根节点值相同的节点R
  - 判断A中以R为根节点的子树是否有和树B一样的结构

- 遍历函数和判断函数分开，可以解决第二次碰到以B为根节点的值时出现的问题，根节点B一直没变；

- 前序遍历的注意点：递归遍历之前要判断是否已经有相同结构，即: !result；
- 判断两课子树是否相同的注意点：只有当 node2 == null 时返回 true，其余都设置为返回false的条件；

```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        boolean res = preOrder(A, B);
        return res;
    }

    // 二叉树的前序遍历框架
    public boolean preOrder(TreeNode nodeA, TreeNode nodeB){
        boolean res = false;
        if(nodeA != null && nodeB != null){
            if(nodeA.val == nodeB.val){
                res = sameStructure(nodeA, nodeB);
            }
            if(!res){
                res = preOrder(nodeA.left, nodeB);
            }
            if(!res){
                res = preOrder(nodeA.right, nodeB);
            }

        }
        return res;
    }

    // 判断两个子树是否有相同的结构
    // 只有B子树遍历结束返回true
    public boolean sameStructure(TreeNode node1, TreeNode node2){
        // B节点遍历结束，说明子树结构相等
        if(node2 == null){
            return true;
        }
        // A节点子树遍历结束，说明子树结构不相等
        if(node1 == null){
            return false;
        }
        if(!(node1.val == node2.val)){
            return false;
        }
        return sameStructure(node1.left, node2.left) && sameStructure(node1.right, node2.right);
    }

}
```



## 814. 二叉树剪枝(后序遍历框架)

> 题目：给定二叉树根节点root，此外树的每个节点的值要么是0，要么是1；返回移除了所有不包含1的子树的原二叉树；
>
> 节点X的子树为X本身，以及所有X的后代；

- 本质是后序遍历框架，注意当判断节点不是全为0的时候给节点的左右节点赋值；

```java
class Solution {
    public TreeNode pruneTree(TreeNode root) {
        return postOrderImpro(root);
    }
    // 节点node子树是否是不包含1的节点，返回node或null
    public TreeNode postOrderImpro(TreeNode node){
        if(node == null){
            return null;
        }
        TreeNode left = postOrderImpro(node.left);
        TreeNode right = postOrderImpro(node.right);
        if(left == null && right == null && node.val == 0){
            return null;
        }else{
            // 不为空的话，要给节点node添加左右节点返回node
            node.left = left;
            node.right = right;
            return node;
        }
    }

}
```



## 剑指Offer28. 对称的二叉树(前序遍历框架的改进)

> 题目：请实现个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的；

- 正常的前序遍历框架： 中  ->  左  ->  右；判断是否是对称的二叉树改进前序遍历框架： 中  ->  右  ->  左，判断两种遍历过程结果是否相同；

- **比较前序遍历序列和对称前序遍历序列是否相等来判断二叉树是否是相同的**；

### 思路一：剑指Offer

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return isSymmetric(root, root);
    }
    // 分左右两支递归
    public boolean isSymmetric(TreeNode node1, TreeNode node2){
        if(node1 == null && node2 == null){
            return true;
        }
        if(node1 == null || node2 == null){
            return false;
        }
        if(node1.val != node2.val){
            return false;
        }
        return isSymmetric(node1.left, node2.right) && isSymmetric(node1.right, node2.left);
    }
}
```



## 617.  合并二叉树

> 题目：给定两个二叉树，将其中的一个覆盖到另一个上时，两个二叉树的节点重叠。合并的规则：若两个节点重叠，则将节点值相加作为节点合并后的新值，否则不为null的节点将直接作为新二叉树的节点；

### 思路一：myself

```java
class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        TreeNode root = new TreeNode();
        return mergeTrees(t1, t2, root);
    }

    // 遍历t1，t2并创建新节点
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2, TreeNode root){
        if(t1 == null && t2 == null){
            return null;
        }
        TreeNode temp1, temp2, temp3, temp4;
        if(t1 != null && t2 != null){
            root = new TreeNode(t1.val + t2.val);
            temp1 = t1.left;
            temp2 = t2.left;
            temp3 = t1.right;
            temp4 = t2.right;
        }else if(t1 == null){
            root = new TreeNode(t2.val);
            temp1 = temp3 = null;
            temp2 = t2.left;
            temp4 = t2.right;
        }else{
            root = new TreeNode(t1.val);
            temp2 = temp4 = null;
            temp1 = t1.left;
            temp3 = t1.right;
        }
        TreeNode left = mergeTrees(temp1, temp2, root);
        TreeNode right = mergeTrees(temp3, temp4, root);
        root.left = left;
        root.right = right;
        return root;
    }
}
```



### 思路二：LeeCode思路

- 若r1和r2中，只要有一个是null，函数就返回；直接在 Tree1 中修改树中的值；深度优先dfs；
- 同时遍历r1和r2的左右节点；
- 代码简洁；

```java
class Solution {

	public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
		if(t1==null || t2==null) {
			return t1==null? t2 : t1;
		}
		return dfs(t1,t2);
	}
	
	TreeNode dfs(TreeNode r1, TreeNode r2) {
		// 如果 r1和r2中，只要有一个是null，函数就直接返回
		if(r1==null || r2==null) {
			return r1==null ? r2 : r1;
		}
		//让r1的值等于r1和r2的值累加，再递归的计算两颗树的左节点、右节点
		r1.val += r2.val;
		r1.left = dfs(r1.left,r2.left);
		r1.right = dfs(r1.right,r2.right);
		return r1;
	}

}
```







## 1022. 从根到叶的二进制数之和(未做)

> 题目：对树上的每一片叶子，都要找出从根到该叶子的路径所表示的数字；返回这些数字之和；
>
> 给出一棵二叉树，其上每个节点的值都是0或1，每一条从根到叶的路径都代表一个从最高有效位开始的二进制数，返回所有数字之和。



Test GitHub SSH

































