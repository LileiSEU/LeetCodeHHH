# LeetCode 回溯

## 进度：回溯算法：2020.1.24 结束

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



### 思路二：回溯 + 剪枝(代码随想录)

- 在for循环中判断一下是否需要继续执行：i < candidates.length && target - candidates[i] >= 0;

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<Integer> path = new ArrayList<>();
        boolean[] used = new boolean[candidates.length];
        // 前提：数组递增顺序
        Arrays.sort(candidates);
        backtracking(candidates, target, 0, path, used);
        return res;
    }

    // 回溯, 树层重复或树枝重复
    private void backtracking(int[] candidates, int target, int startIndex, List<Integer> path, boolean[] used) { 
        if (target == 0) {
            res.add(new ArrayList(path));
            return;
        }
        for (int i = startIndex; i < candidates.length && target - candidates[i] >= 0; i ++) {
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



## 131. 分割回文串

> 题目：给定一个字符串s，将s分割成一些子串，使每个子串都是回文串。返回s所有可能的分割方案。

### 思路：回溯(代码随想录)

- 本题涉及到两个关键问题：1. 切割问题，有不同的切割方式；2. 评断回文串
- 切割问题也可以抽象为一棵树形结构，递归用来纵向遍历，for循环用来横向遍历，切割线切割到字符串的结尾位置，说明找到了一个切割方法；

```shell
# 回溯三部曲：
	1. 确定参数和返回值：切割过的地方不能重复切割，所以需要startIndex；
	2. 确定递归终止条件：切割到了字符串最后面，说明找到了一种切割方法，此时就是本层递归的终止条件；startIndex就是切割线，[startIndex, i] 就是要截取的子串；
	3. 判断单层递归逻辑：判断子串是否是回文串；
```

![image-20210122163717794](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210122163717794.png)

```java
class Solution {
    List<List<String>> res = new ArrayList<>();
    List<String> path = new ArrayList<>();
    public List<List<String>> partition(String s) {
        if (s.length() == 0) {
            return res;
        }    
        backtracking(s, 0);
        return res;
    }

    // 回溯算法
    public void backtracking(String s, int startIndex) {
        if (startIndex >= s.length()) {
            res.add(new ArrayList(path));
            return;
        }
        // startIndex - i 即为分割的子串
        // 一个集合中找组合结果，需要startIndex
        for (int i = startIndex; i < s.length(); i++) {
            if (isPalindrome(s, startIndex, i)) {
                path.add(s.substring(startIndex, i + 1));
            } else {
                continue;
            }
            backtracking(s, i + 1);
            path.remove(path.size() - 1);
        }
    }

    // 判断子串是否是回文串
    private boolean isPalindrome(String s, int start, int end) {
        for (int i = start, j = end; i <=j ; i++, j--) {
            if (s.charAt(i) != s.charAt(j)) {
                return false;
            }
        }
        return true;
    }

}
```



## 93. 复原IP地址

> 题目：给定一个只包含数字的字符串，复原它并返回所有可能的IP地址格式。有效的IP地址正好由四个整数(每个整数位于0 - 255之间组成，且不能含有前导0,)，整数之间用'.' 分隔。

### 思路：回溯 + 切割三次

- 切割问题就可以使用回溯搜索法把所有可能性搜出来；
- 判断子串是否合法：段位以0为开头的数字不合法、段位里有非正整数字符不合法、段位如果大于255了不合法；注意判断和是否大于255的方法**秒啊**`num = num * 10 + (s.charAt(i) - '0');`

![image-20210122180742127](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210122180742127.png)

```shell
# 回溯三部曲：
	1. 递归参数：startIndex 是一定需要的，因为不能重复分割，记录下一层递归分割的起始位置；pointNum记录添加逗点的数量(其实就是我理解的分割次数)
	2. 递归终止条件：pointNum表示逗点数量，pointNum为3说明字符串分成了4段了，然后验证一下第四段是否合法，如果合法就加入到结果集里
	3. 单层递归逻辑：在for (int i = startIndex; i < s.size(); i++)循环中 [startIndex, i]这个区间就是截取的子串，需要判断这个子串是否合法。如果合法就在字符串后面加上符号.表示已经分割。如果不合法就结束本层循环，剪掉的分支：
```

```java
class Solution {
    // IP地址由4个整数构成，在String中切3次
    List<String> res = new ArrayList<>();
    public List<String> restoreIpAddresses(String s) {
        if (s.length() == 0) {
            return res;
        }
        StringBuilder builder = new StringBuilder();
        backtracking(s, 0, 0, builder);
        return res;
    }

    // 回溯, splitNum 是指分割次数
    private void backtracking(String s, int startIndex, int splitNum, StringBuilder builder) {
        if (splitNum >= 3) {
            if (isValid(s, startIndex, s.length() - 1)) {
                String result = builder.toString() + s.substring(startIndex, s.length());
                res.add(result);
            }
            return;
        }
        // 当前整数为[startIndex, i]
        for (int i = startIndex; i < s.length(); i++) {
            int length = builder.length();
            if (isValid(s, startIndex, i)) { // 判断[startIndex,i]这个区间的子串是否合法
                builder.append(s.substring(startIndex, i + 1) + ".");// 加i后面插入,
                splitNum ++;
                backtracking(s, i + 1, splitNum, builder);
                splitNum --;
                builder.delete(length, builder.length()); // 删除添加的内容
            } else {
                break;	//不合法，直接结束本层循环
            }           
        }
    }

    // 判断IP地址中每个整数是否有效,[beginIndex, endIndex]
    private boolean isValid(String s, int beginIndex, int endIndex) {
        // 注意 beginIndex > endIndex 的情况
        // 当回溯中for循环到最后一个值即s.length()-1的时候，下一层递归splitNum == 3,startIndex==s.length(), 此时startIndex > s.length() - 1
        if (beginIndex > endIndex) {
            return false;
        }
        // '0'开头的整数不符合
        if (s.charAt(beginIndex) == '0' && beginIndex != endIndex) {
            return false;
        }
        //  判断是否位于0-255之间并且都是数字，用String来判断(测试用例中有long放不下的整数)
        //  太强了
        int num = 0;
        for (int i = beginIndex; i <= endIndex; i++) {
            if (s.charAt(i) < '0' || s.charAt(i) > '9') {
                return false;
            }
            num = num * 10 + (s.charAt(i) - '0'); // 经典
            if (num > 255) {
                return false;
            }
        }        
        return true;
    }

}
```



## 78. 子集(元素互不相同)

> 题目：给你一个整数数组nums，数组中的元素互不相同，返回该数组所有可能的子集(幂集)；解集不能包含重复的子集，可以按任意顺序返回解集。

### 思路一：收集所有节点的path

- 在回溯函数的for循环中，每添加一个元素就可以要在最终的结果集中添加一条路径；注意[] 子集；
- 如果把子集问题、组合问题都抽象为一棵树的话，**那么组合问题和分割问题都是收集树的叶子节点，而子集问题是找树的所有节点**
- 子集也是一种组合问题，因为它的集合是无序的，所以取过的元素就不会重复，for循环要从startIndex 开始，遍历整个树的时候，把所有节点都记录下来，就是要求的子集集合。

![image-20210122201039160](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210122201039160.png)

```java
class Solution {
    // 所有节点的结果都需要收集，同时注意：[] 元素
    List<List<Integer>> res = new ArrayList<>(); 
    public List<List<Integer>> subsets(int[] nums) {
        List<Integer> path = new ArrayList<>();
        res.add(path);
        backtracking(nums, 0, path);
        return res;
    }

    private void backtracking(int[] nums, int startIndex, List<Integer> path) {
        if (startIndex == nums.length) {
            return;
        }
        for (int i = startIndex; i < nums.length; i++) {
            path.add(nums[i]);
            res.add(new ArrayList(path));
            backtracking(nums, i + 1, path);
            path.remove(path.size() - 1);
        }
    }

}
```



### 代码改进：

- 循环开始的时候，收集结果，就可以不用加 [] 元素；

```java
class Solution {
    // 所有节点的结果都需要收集，同时注意：[] 元素
    List<List<Integer>> res = new ArrayList<>(); 
    public List<List<Integer>> subsets(int[] nums) {
        List<Integer> path = new ArrayList<>();
        backtracking(nums, 0, path);
        return res;
    }

    private void backtracking(int[] nums, int startIndex, List<Integer> path) {
        res.add(new ArrayList(path));
        if (startIndex == nums.length) {
            return;
        }
        for (int i = startIndex; i < nums.length; i++) {
            path.add(nums[i]);
            backtracking(nums, i + 1, path);
            path.remove(path.size() - 1);
        }
    }

}
```



## 90. 子集||(重复元素)

> 题目：给定一个可能包含重复元素的整数数组nums，返回该数组所有可能的子集(幂集)。解集不能包含重复的子集。

### 思路：回溯 + 有重复元素

- 添加一个used数组，在单层递归逻辑的for循环中进行判断，树层或树枝是否使用过

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        boolean[] used = new boolean[nums.length];
        List<Integer> path = new ArrayList<>();
        Arrays.sort(nums);
        backtracking(nums, 0, path, used);
        return res;
    }

    // 回溯函数
    private void backtracking(int[] nums, int startIndex, List<Integer> path, boolean[] used) {
        res.add(new ArrayList(path));
        if (startIndex >= nums.length) {
            return;
        }
        // 单层递归逻辑
        for (int i = startIndex; i < nums.length; i++) {
            // nums[i] == nums[i - 1] 且前一个节点被使用过
            // used[i - 1] == true，说明同一树支candidates[i - 1]使用过
            // used[i - 1] == false，说明同一树层candidates[i - 1]使用过
            if (i > 0 && nums[i] == nums[i - 1] && used[i - 1] == false) {
                continue;
            }
            path.add(nums[i]);
            used[i] = true;
            backtracking(nums, i + 1, path, used);
            used[i] = false;
            path.remove(path.size() - 1);
        }
    }

}
```



## 491.  递增子序列

> 题目：给定一个整型数组，找到所有该数组的递增子序列，递增子序列的长度至少是2.

### 思路：代码随想录

- 题目中要求自增子序列，不能对原数组进行排序，排完序的数组都是自增子序列了；**「本题只要同层重复使用元素，递增子序列就会重复」**还有一种情况就是如果选取的元素小于子序列最后一个元素，那么就不能是递增的，所以也要pass掉。
- 用set集合来判断元素是否在某层遍历中使用；
- 递归开始时收集结果，在for循环中对结果进行判断；

![image-20210123171803405](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210123171803405.png)

```shell
# 回溯三部曲：
	1. 递归函数参数：一个元素不能重复使用，所以需要startIndex;
	2. 终止条件：该题目需要遍历树形结构找每一个节点，startIndex控制递归层数；不会无限递归，收集结果的时候题目要求递增子序列大于2；
	3. 单层递归逻辑：同一层上使用过的元素不能在使用了；使用set来去除层中的重复元素；判断nums[i] < path.back()之前一定要判断path是否为空，所以是!path.empty() && nums[i] < path.back()。
uset.find(nums[i]) != uset.end()判断nums[i]在本层是否使用过。「这也是需要注意的点，unordered_set<int> uset; 是记录本层元素是否重复使用，新的一层uset都会重新定义（清空），所以要知道uset只负责本层！」
```

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> findSubsequences(int[] nums) {
        List<Integer> path = new ArrayList<>();
        backtracking(nums, 0, path);
        return res;
    }

    // 回溯
    private void backtracking(int[] nums, int startIndex, List<Integer> path) {
        if (path.size() >= 2) {
            res.add(new ArrayList(path));
        }
        // 用set记录每一层的相同值是否已经出现
        Set<Integer> set = new HashSet<>();
        for (int i = startIndex; i < nums.length; i ++) {
            if ((!path.isEmpty() && nums[i] < path.get(path.size() - 1)) || set.contains(nums[i])) {
                continue;
            }
            set.add(nums[i]);
            path.add(nums[i]);
            backtracking(nums, i + 1, path);
            path.remove(path.size() - 1);
        }
    }
}
```



## 46.  全排列(无重复数字)

> 题目：给定一个没有重复数字的序列，返回其所有可能的全排列。

### 思路：回溯(myself)

- 回溯函数中的for循环中，i 从 0 开始选取，如果path中已经包含了某个元素，就跳过这次循环。
- 缺点：用 ArrayList.contains() 方法，时间复杂度高、

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> permute(int[] nums) {
        List<Integer> path = new ArrayList<>();
        backtracking(nums, path);
        return res;
    }

    // 回溯，收集叶子节点
    private void backtracking(int[] nums, List<Integer> path) {
        if (path.size() == nums.length) {
            res.add(new ArrayList(path));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (path.contains(nums[i])) {
                continue;
            }
            path.add(nums[i]);
            backtracking(nums, path);
            path.remove(path.size() - 1);
        }
    }

}
```



### 思路2：回溯 + used数组

- 处理排列问题不用使用`startIndex`，需要一个`used`数组标记已经选择的元素；
- 叶子节点就是收割结果的地方；

- 使用 `used`数组来判断在某次遍历中节点是否使用，**而used数组，其实就是记录此时path里都有哪些元素使用了，一个排列里一个元素只能使用一次**；

![image-20210124165607096](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210124165607096.png)

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> permute(int[] nums) {
        List<Integer> path = new ArrayList<>();
        boolean[] used = new boolean[nums.length];
        backtracking(nums, used, path);
        return res;
    }

    // 回溯 + used数组
    private void backtracking(int[] nums, boolean[] used, List<Integer> path) {
        if (path.size() == nums.length) {
            res.add(new ArrayList(path));
            return;
        }
        // used[i] = true，表示树枝中使用过
        // used[i] = false, 表示树层中使用过
        for (int i = 0; i < nums.length; i ++) {
            if (used[i] == true) {
                continue;
            }
            path.add(nums[i]);
            used[i] = true;
            backtracking(nums, used, path);
            used[i] = false;
            path.remove(path.size() - 1);
        }
    }

}
```



## 47. 全排列||(重复元素)

> 题目：给定一个可包含重复数字的序列nums，按任意顺序返回所有不重复的全排列。

### 思路：回溯 + 全排列 + set去重

- 每一层中的重复元素使用`集合set`来去重；全排列中需要使用`used`数组来确定元素是否已经使用过；
- 对于**集合set**，表示的是每一层中的重复元素，每一层都会新建一个set，所以加入元素后不需要删除set中的元素；

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<Integer> path = new ArrayList<>();
        boolean[] used = new boolean[nums.length];
        backtracking(nums, used, path);
        return res;
    }

    // 回溯 
    private void backtracking(int[] nums, boolean[] used, List<Integer> path) {
        if (path.size() == nums.length) {
            res.add(new ArrayList(path));
            return;
        }
        Set<Integer> set = new HashSet<>();// 用set存储同一层中的重复元素是否被使用过
        for (int i = 0; i < nums.length; i ++) {
            if (set.contains(nums[i])) {
                continue;
            }
            if (used[i] == true) {
                continue;
            }
            path.add(nums[i]);
            used[i] = true;
            set.add(nums[i]);
            backtracking(nums, used, path);
            used[i] = false;
            path.remove(path.size() - 1);
        }

    }

}
```



### 思路2：回溯 + 排序 + 去重

- **还要强调的是去重一定要对元素进行排序，这样我们才方便通过相邻的节点来判断是否重复使用了,一般来说：组合问题和排列问题是在树形结构的叶子节点上收集结果，而子集问题就是取树上所有节点的结果**。

- 将数组**排序**后，使用`used`数组去重；
- 对树层中前一位去重，就用`used[i - 1] == false`

![image-20210124172034763](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20210124172034763.png)

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<Integer> path = new ArrayList<>();
        boolean[] used = new boolean[nums.length];
        Arrays.sort(nums);
        backtracking(nums, used, path);
        return res;
    }

    private void backtracking(int[] nums, boolean[] used, List<Integer> path) {
        if (path.size() == nums.length) {
            res.add(new ArrayList(path));
            return;
        }
        for (int i = 0; i < nums.length; i ++) {
            // 对于树层去重来说：nums[i - 1] == true，表示树枝中使用过该元素；nums[i -1] == false表示层中使用过该元素
            if (i > 0 && nums[i] == nums[i - 1] && used[i - 1] == false) {
                continue;
            }
            if (used[i] == true) {// 对于全排列来说，used[i] == true 表示排列中使用过该元素
                continue;
            }
            path.add(nums[i]);
            used[i] = true;
            backtracking(nums, used, path);
            used[i] = false;
            path.remove(path.size() - 1);
        }
    }

}
```



## 51. N皇后

> 题目：n皇后问题研究的是如何将n个皇后放置在 n x n 的棋盘上，并且使皇后彼此之间不能相互攻击。给你一个整数n，返回所有不同的n皇后问题的解决方案。每一种解法包含一个不同的n皇后问题的棋子放置方案，该方案中`Q`和`.`分别代表了皇后和空位。注意：皇后彼此不能相互攻击，也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上。

### 思路：回溯(代码随想录)

**只要搜索到了树的叶子节点，说明就找到了皇后们的合理位置了, 递归深度就是row控制棋盘的行，每一层里for循环的col控制棋盘的列，一行一列，确定了放置皇后的位置。**

```shell
# 1. 递归函数参数： 使用二维数组result来记录最终结果，n是棋牌的大小，然后用row来记录当前遍历到棋盘的第几层
# 2. 递归终止条件：只要搜索到了树的叶子节点，说明就找到了皇后们的合理位置了
# 3. 单层递归逻辑：递归深度就是row控制棋盘的行，每一层里for循环的col控制棋盘的列，一行一列，确定了放置皇后的位置。每次都是要从新的一行的起始位置开始搜，所以都是从0开始。验证棋牌是否合法
```



```java
class Solution {
    List<List<String>> res = new ArrayList<>();
    public List<List<String>> solveNQueens(int n) {
        char[][] chessboard = new char[n][n];
        for (int i = 0; i < n; i ++) {
            for (int j = 0; j < n; j ++) {
                chessboard[i][j] = '.';
            }
        }
        backtracking(n, 0, chessboard);
        return res;
    }
    // 回溯算法
    private void backtracking(int n, int row, char[][] chessboard) {
        if (row == n) {
            res.add(array2List(chessboard));
            return;
        }
        // 循环中是列变量；递归深度是行变量
        for (int col = 0; col < n; col ++) {
            if (isValid(row, col, chessboard, n)) {
                chessboard[row][col] = 'Q';
                backtracking(n, row + 1, chessboard);
                chessboard[row][col] = '.';
            }
        }

    }

    // 判断chessboard[row][col]该位置能否放Q
    public boolean isValid(int row, int col, char[][] chessboard, int n) {
        // 该列目前行中是否有Q
        for (int i = 0; i <= row; i ++) {
            if (chessboard[i][col] == 'Q') {
                return false;
            }
        }
        // 判断45度是否有Q
        for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i --, j--) {
            if (chessboard[i][j] == 'Q') {
                return false;
            }
        }
        // 判断145度是否有Q
        for (int i = row - 1, j = col + 1; i >= 0 && j < n; i --, j ++) {
            if (chessboard[i][j] == 'Q') {
                return false;
            }
        }
        return true;
    }

    // 将二维数组chessboard转换为List<String>
    private List<String> array2List(char[][] chessboard) {
        List<String> path = new ArrayList<>();
        for (int i = 0; i < chessboard.length; i ++) {
            path.add(new String(chessboard[i]));
        }
        return path;
    }

}
```











## 332.  重新安排行程

> 题目：给定一个机票的字符串二维数组[form, to] ，子数组中的两个成员分别表示飞机出发和降落的机场地点，对该行程进行重新规划。所有这些机票都属于一个从JFK(肯尼迪国际机场)出发的先生，所以该行程必须从JFK开始。
>
> 提示：
>
> 如果存在多种有效的行程，请你按字符自然排序返回最小的行程组合。例如，行程 ["JFK", "LGA"] 与 ["JFK", "LGB"] 相比就更小，排序更靠前
> 所有的机场都用三个大写字母表示（机场代码）。
> 假定所有机票至少存在一种合理的行程。
> 所有的机票必须都用一次 且 只能用一次。



















