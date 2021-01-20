# LeetCode 回溯

## 进度：

## 回溯算法理论

- 回溯是递归的副产品，只要有递归就会有回溯，回溯在递归函数下面，回溯是纯暴力搜索，**抽象为图形结构**；
- 回溯可以解决的问题：
  - 组合问题(元素无序)：N个数里面按定义规则找出k个数的集合
  - 排列问题：N个数按一定规则全排列，有几种排列方式
  - 切割问题：一个字符串按一定规则有几种切割方式
  - 子集问题：一个N个数的几个里有多少符合条件的子集
  - 棋盘问题：N皇后、解数独等
- **「回溯法解决的问题都可以抽象为树形结构」**，所有回溯法的问题都可以抽象为树形结构！因为回溯法解决的都是在集合中递归查找子集，**「集合的大小就构成了树的宽度，递归的深度，都构成的树的深度」**。递归就要有终止条件，所以必然是一颗高度有限的树（N叉树）。

![image-20210118115733388](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210118115733388.png)

```shell
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```



## 回溯三部曲

- 回溯函数返回值以及参数；
- 回溯终止条件；
- 回溯搜索的遍历过程，单层递归逻辑；
- 剪枝操作：一般在回溯函数for循环条件中做处理，for循环：横向遍历；递归：纵向遍历；



## 77. 组合(组合问题)

> 题目：给定两个整数n和k，返回1...n中所有可能的k个数的组合。

### 思路：回溯+搜索整个树(代码随想录)

- 选择1所有的组合添加完成后，选择2时剩余的结果要从3开始；因为是组合问题；

![image-20210118162054756](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210118162054756.png)

```java
class Solution {
    // 变量定义为全局变量可以减少回溯函数参数
    List<List<Integer>> res = new ArrayList<>();
    Deque<Integer> path = new LinkedList<>();
    public List<List<Integer>> combine(int n, int k) {
        backtracing(n, k, 1);
        return res;
    }

    // 回溯函数,startIndex代表选择第几个节点
    public void backtracing(int n, int k, int startIndex) {
        // 返回条件
        if (path.size() == k) {
            res.add(new ArrayList(path));
            return;
        }
        // 单层逻辑
        for (int i = startIndex; i <= n; i++) {
            path.add(i);
            backtracing(n, k, i + 1);
            path.removeLast();
        }
    }

}
```



### 思路：回溯+剪枝操作(代码随想录)

![image-20210118162230143](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210118162230143.png)

![image-20210118162338344](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210118162338344.png)

- 至多从 n - (k  - path.size) + 1节点开始操作,索引是从1开始的；
- **「所以，可以剪枝的地方就在递归中每一层的for循环所选择的起始位置」**。**「如果for循环选择的起始位置之后的元素个数 已经不足 我们需要的元素个数了，那么就没有必要搜索了」**。

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    Deque<Integer> path = new LinkedList<>();
    public List<List<Integer>> combine(int n, int k) {
        backtracking(n, k, 1);
        return res;
    }

    // 回溯 + 剪枝
    private void backtracking(int n, int k, int startIndex) {
        if (path.size() == k) {
            res.add(new ArrayList(path));
            return;
        }
        // 剪枝操作，至多从 n - (k - path.size()) + 1 开始搜索,索引从1开始
        for (int i = startIndex; i <= n - (k-path.size()) + 1; i++) {
            path.add(i);
            backtracking(n, k, i + 1);
            path.removeLast();
        }

    }

}
```



## 216.  组合总和|||(子集问题)

> 题目：找出所有相加之和为n的k个数的组合。组合中只允许含有1 - 9 的正整数，并且每种组合中不存在重复的数字。
>
> 说明：所有数字都是正整数，解集中不能包含重复的组合。

### 思路一：回溯算法 + 遍历整棵树(代码随想录)

![image-20210118170007560](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210118170007560.png)

- 回溯终止条件,path 中元素个数等于k即终止;

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    Deque<Integer> path = new LinkedList<>();
    public List<List<Integer>> combinationSum3(int k, int n) {
        backtracking(n, k, 0, 1);
        return res;
    }

    // 普通回溯
    private void backtracking(int targetNum, int k, int sum, int startIndex) {
        // 回溯终止条件,path 中元素个数等于k即终止
        if (path.size() == k) {
            if (sum == targetNum) {
                res.add(new ArrayList(path));
            }
            return;
        }
        // 单层逻辑
        for (int i = startIndex; i <= 9; i++) {
            path.add(i);
            sum = sum + i;
            backtracking(targetNum, k, sum, i + 1);
            sum = sum - i;
            path.removeLast();
        }
    }

}
```



### 思路二：回溯 + 剪枝

- 题目中给出的可以取值的序列 1  -  9，即该序列是有序的；如果前一个节点的sum > targetNum，后面的节点就可以不遍历了；
- 已选元素总和若已经大于n，那么后面的遍历就没有意义了，直接剪掉；剪枝的地方就是递归终止的地方，if (sum > targetNum)  return;

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    Deque<Integer> path = new LinkedList<>();
    public List<List<Integer>> combinationSum3(int k, int n) {
        backtracking(n, k, 0, 1);
        return res;
    }

    // 回溯 + 剪枝
    private void backtracking(int targetNum, int k, int sum, int startIndex) {
        // 剪枝
        if (sum > targetNum) {
            return;
        }
        // 回溯终止条件,path 中元素个数等于k即终止
        if (path.size() == k) {
            if (sum == targetNum) {
                res.add(new ArrayList(path));
            }
            return;
        }
        // 单层逻辑
        for (int i = startIndex; i <= 9; i++) {
            path.add(i);
            sum = sum + i;
            backtracking(targetNum, k, sum, i + 1);
            sum = sum - i;
            path.removeLast();
        }
    }

}
```



## 17.  电话号码的字母组合(不同集合中取结果)

> 题目：给定一个仅包含数字2-9的字符串，返回所有它能表示的字母组合。给出数字到字母的映射如下(与电话按键相同)。注意1不对应任何字母。

![image-20210120194538098](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210120194538098.png)

### 思路一：回溯法解决N个循环问题

注意：**「注意这里for循环，可不像是在[回溯算法：求组合问题！](https://mp.weixin.qq.com/s?__biz=MzUxNjY5NTYxNA==&mid=2247485253&idx=1&sn=8332edaabc9bf43e45835bce7964ce88&scene=21#wechat_redirect)和[回溯算法：求组合总和！](https://mp.weixin.qq.com/s?__biz=MzUxNjY5NTYxNA==&mid=2247485277&idx=1&sn=0553db6b5c5952094d536ae2b8c18124&scene=21#wechat_redirect)中从startIndex开始遍历的」**。

**「因为本题每一个数字代表的是不同集合，也就是求不同集合之间的组合，而77. 组合和216.组合总和III都是是求同一个集合中的组合！」**

- 需要注意的是回溯结束的条件：当回溯的index == digits.length() 时，表示情况已经遍历完成；直接将 StringBuilder.toString() 之后加入结果集即可；
- 学习到的点：Map声明并赋值的操作、删除StringBuilder的最后一个char的方法： builder.deleteCharAt(int index)

![image-20210120201949649](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210120201949649.png)

```java
class Solution {
    List<String> res = new ArrayList<>();
    Map<Character, String> map = new HashMap<>(){
        {
            put('2',"abc");
            put('3',"def");
            put('4',"ghi");
            put('5',"jkl");
            put('6',"mno");
            put('7',"pqrs");
            put('8',"tuv");
            put('9',"wxyz");
        }
    };
    public List<String> letterCombinations(String digits) {
        if(digits.equals("")) {
            return res;
        }
        StringBuilder builder = new StringBuilder();
        backtracking(digits, 0, builder);
        return res;
    }
    
    // 回溯法
    private void backtracking(String digits, int index, StringBuilder builder) {
        if (index == digits.length()) {
            res.add(builder.toString());
            return;
        }
        String temp = map.get(digits.charAt(index));
        for (int i = 0; i < temp.length(); i++) {
            builder.append(temp.charAt(i));
            backtracking(digits, index + 1, builder);
            builder.deleteCharAt(builder.length() - 1);
        }
    }

}
```



## 39.  组合总和(数组无重复元素)

> 题目：给定一个无重复元素的数组candidates和一个目标数target，找出candidates中所有可以使数字和为target的组合。
>
> candidate中的数字可以无限制重复被选取；说明：所有数字(包括target)都是正整数，解集不能包含重复的组合。

### 思路一：回溯(节点可重复选取) （代码随想录）

- 注意：当从同一集合中取结果时，回溯中for循环需要startIndex；当从不同集合中取结果时，回溯中for循环不需要startIndex；直接从0开始循环即可；
- 回溯递归过程中，因为数字可以重复使用，所以startIndex 不需要加1；

![image-20210120205525252](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210120205525252.png)

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<Integer> path = new ArrayList<>();
        backtracking(candidates, target, 0, 0, path);
        return res;
    }

    // 回溯
    private void backtracking(int[] candidates, int target, int sum, int startIndex, List<Integer> path) {
        if (sum > target) {
            return;
        }
        if (sum == target) {
            res.add(new ArrayList(path));
            return;
        }
        for (int i = startIndex; i < candidates.length; i ++) {
            path.add(candidates[i]);
            sum = sum + candidates[i];
            backtracking(candidates, target, sum, i, path);
            sum = sum - candidates[i];
            path.remove(path.size() - 1);
        }
    }

}
```



### 思路二：回溯 + 剪枝优化

- 前提：要对candidates数组排序   --->   Arrays.sort(candidates);
- for循环遍历中，若sum +  candidate[i]  <= target 时循环，是在candidate数组已经升序排序的条件下才可以这样做；

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<Integer> path = new ArrayList<>();
        Arrays.sort(candidates);
        backtracking(candidates, target, 0, 0, path);// 需要排序
        return res;
    }

    // 回溯
    private void backtracking(int[] candidates, int target, int sum, int startIndex, List<Integer> path) {
        if (sum == target) {
            res.add(new ArrayList(path));
            return;
        }
        for (int i = startIndex; i < candidates.length && sum + candidates[i] <= target; i ++) {
            path.add(candidates[i]);
            sum = sum + candidates[i];
            backtracking(candidates, target, sum, i, path);
            sum = sum - candidates[i];
            path.remove(path.size() - 1);
        }
    }

}
```



## 40.  组合总和||(数组中存在重复元素)

> 题目：给定一个数组candidates 和一个目标数 target， 找出 candidates 中所有可以使数字和为 target的组合。注意：candidates 中的每个数字在每个组合中只能使用一次。

```shell
# 参考博客：https://mp.weixin.qq.com/s/_1zPYk70NvHsdY8UWVGXmQ
```

### 思路一：回溯算法 + 不能重复

- 本题candidates中的每个数字在每个组合中只能使用一次；本题数组candidates的元素是有重复的，而 39 是无重复元素的数组candidates；

- **本题的难点在于区别2中：集合(数组candidates有重复元素，但还不能有重复的组合)**
- 组合问题可以抽象为树形结构，那么“使用过”在这个树形结构上是有两个维度的，一个维度是同一树枝上使用过，另一个维度是同一树层上使用过。
- 题目中：元素在同一个组合内是可以重复的，怎么重复都没事，但两个组合不能相同。**「所以我们要去重的是同一树层上的“使用过”，同一树枝上的都是一个组合里的元素，不用去重」**。
- 首先对数组进行排序，之后看出在candidates[i] == candidates[i - 1]相同的情况下：used[i - 1] == true，说明同一树支candidates[i - 1]使用过；used[i - 1] == false，说明同一树层candidates[i - 1]使用过

![image-20210120235418632](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210120235418632.png)

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<Integer> path = new ArrayList<>();
        boolean[] used = new boolean[candidates.length];
        Arrays.sort(candidates);
        backtracking(candidates, target, 0, path, used);
        return res;
    }

    // 回溯, 树层重复或树枝重复
    private void backtracking(int[] candidates, int target, int startIndex, List<Integer> path, boolean[] used) { 
        if (target < 0) {
            return;
        }
        if (target == 0) {
            res.add(new ArrayList(path));
            return;
        }
        for (int i = startIndex; i < candidates.length; i ++) {
            // used[i - 1] == true，说明同一树支candidates[i - 1]使用过
            // used[i - 1] == false，说明同一树层candidates[i - 1]使用过
            if (i > 0 && candidates[i] == candidates[i - 1] && used[i - 1] == false) {
                continue;
            }
            target = target - candidates[i];
            path.add(candidates[i]);
            used[i] = true;
            backtracking(candidates, target, i + 1, path, used);
            used[i] = false;
            target = target + candidates[i];
            path.remove(path.size() - 1);
        }
    }

}
```





















