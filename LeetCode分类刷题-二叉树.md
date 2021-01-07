# LeetCode 二叉树（二叉树的层序遍历未看）

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





































