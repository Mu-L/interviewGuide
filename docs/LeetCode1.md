## LeetCode 热门100题-题解

##### 主要是记录自己刷题的过程，也方便自己复习

#### [第72题-编辑距离](#第72题-编辑距离)
#### [第155题-最小栈](#第155题-最小栈)
#### [第160题-相交链表](#第160题-相交链表)
#### [第142题-环形链表II](#第142题-环形链表II)
#### [第739题-每日温度](#第739题-每日温度)

#### [第题-](#第题-)




### 第72.题-编辑距离

给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符


示例 1：

输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')

##### 解题思路
就是我们要把word1转换为word2，从word2第一个字符开始遍历，对于每一个字符而言，i，j分别为word1和word2当前遍历的位置有四种选择：
##### 1.直接跳过
当words[i]与word[j]相同时，简单举例来说，就是假设要把"acc"转换为"abb"，由于第一个字符相同，那么编辑的步数其实是等于"cc"转换为"bb"的步数
所以假设使用restLength[i][j]代表剩余子串需要的步数，
```
restLength[i][j] = restLength[i+1][j+1]
```
##### 2.将word1[i]删除
假设我们要将"abcd"转换为"bc"，那么其实我们可以将a删除掉，相当于对于word1跳过了当前字符，继续遍历
```
restLength[i][j] = restLength[i+1][j]
```
##### 3.在word1[i]前增加新字符word2[j]
假设我们要将"bbb"转换为"abbb",那么就可以对于word1增加一个word2当前的字符，然后继续遍历，相当于是对word2的字符跳过了
```
restLength[i][j] = restLength[i][j+1]
```
##### 4.对word1[i]替换成word2[j]

假设我们要将"abbb"转换为"cbbb",那么直接将a替换成c就好了，相当于对word1和word2字符串都跳过了当前字符
```
restLength[i][j] = restLength[i+1][j+1]
```
代码
```java
    int[][] cacheLength;
    public int minDistance(String word1, String word2) {
        if (word1==null||word2==null) {
            return 0;
        }
        cacheLength = new int[word1.length()+1][word2.length()+1];
        return minDistance(word1,word2,0,0);
    }

    public int minDistance(String word1, String word2,int i,int j) {
        //当有一个字符串已经走到末尾了，要变成另一个字符串只能是将另一个字符串剩余字符全部添加过来
        if (cacheLength[i][j]!=0) {
            return cacheLength[i][j];
        }
        if (i == word1.length()) {
            return word2.length() - j;
        }
        if (j == word2.length()) {
            return word1.length() - i;
        }
        if (word1.charAt(i) == word2.charAt(j)) {//相等就直接向后移动
            return minDistance(word1,word2,i+1,j+1);
        }
        //这一步选择删除，那么就是将word1的当前字符删除
        int deleteLength = minDistance(word1,word2,i+1,j) + 1;
        //这一步选择插入，就是将word2的当前字符插入到word1
        int insertLength = minDistance(word1,word2,i,j+1) + 1;
        //这一步走替换，就是将word2的当前字符替换到word1
        int replaceLength = minDistance(word1,word2,i+1,j+1) + 1;
        //选择上面三种路线中最短的
        int min = deleteLength < insertLength ? deleteLength : insertLength;
        min = replaceLength < min ? replaceLength : min;
        cacheLength[i][j] = min;
        return min;
    }
```

### 第155题-最小栈
设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

push(x) —— 将元素 x 推入栈中。
pop() —— 删除栈顶的元素。
top() —— 获取栈顶元素。
getMin() —— 检索栈中的最小元素。


示例:

输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.

##### 解题思路
就是用一个最小栈来记录最小值
```java
Stack<Integer> stack;
    Stack<Integer> minStack;
    /** initialize your data structure here. */
    public MinStack() {
        stack = new Stack<Integer>();
        minStack = new Stack<Integer>();
    }

    public void push(int x) {
        if(minStack.size()==0||x<=minStack.peek()) {
            minStack.push(x);
        }
        stack.push(x);
    }

    public void pop() {
        if(stack.peek().equals(minStack.peek())) {
            minStack.pop();
        }
        stack.pop();
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
       return minStack.peek();
    }
```

### 第160题-相交链表

编写一个程序，找到两个单链表相交的起始节点。

如下面的两个链表**：**

[![img](../static/160_statement.png)](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

在节点 c1 开始相交。

##### 解题思路

分别计算出链表A，链表B的长度，让长的链表先走n步，直到两个链表剩余节点长度一样，然后每个链表都走一步，直到节点相等，即为相交节点。

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        int lengthA=0;
        ListNode nodeA = headA;
        while(nodeA!=null) {
            lengthA++;
            nodeA=nodeA.next;
        }
        int lengthB=0;
        ListNode nodeB = headB;
        while(nodeB!=null) {
            lengthB++;
            nodeB=nodeB.next;
        }
        nodeA = headA;
        nodeB = headB;
        while(lengthA!=lengthB) {
            if (lengthA>lengthB) {
                lengthA--;
                nodeA = nodeA.next;
            } else {
                lengthB--;
                nodeB = nodeB.next;
            }
        }
        while(nodeA!=null && nodeB!=null) {
            if(nodeA == nodeB) {
                return nodeA;
            }
            nodeA = nodeA.next;
            nodeB = nodeB.next;
        }
        return null;
    }
```

 

### 第142题-环形链表II

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意，pos 仅仅是用于标识环的情况，并不会作为参数传递到函数中。

说明：不允许修改给定的链表。

进阶：

你是否可以使用 O(1) 空间解决此题？


示例 1：

![img](../static/circularlinkedlist-9062165.png)

输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。

##### 解题思路

```java
public ListNode detectCycle(ListNode head) {
        //快慢节点在圆中相遇
        //然后慢节点在圆中走一圈，计算出圆的周长
        //快节点凑头
        if (head==null || head.next == null) {
            return null;
        }
        ListNode slow = head.next;
        ListNode quick = head.next.next;
  			//通过快慢指针，让两个指针在环中相遇
        while (quick!=slow) {
            if (quick == slow && quick!=null) {
                break;
            }
            slow = slow.next;
            if (quick==null||quick.next == null) {
                return null;
            }
            quick = quick.next;
            quick = quick.next;
        }
    			//计算环的长度
        int circleLength = 1;
        ListNode copySlow = slow.next;
        while (copySlow != slow) {
            copySlow = copySlow.next;
            circleLength++;
        }
				//快指针先走环的长度步
        quick = head;
        while (circleLength>0) {
            quick = quick.next;
            circleLength--;
        }
        slow = head;
  			//慢指针出发，相遇的节点就是环的入口
        while (quick!=slow) {
            quick = quick.next;
            slow = slow.next;
        }
        return quick;
    }
```
### 第739题-每日温度
请根据每日 气温 列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 0 来代替。

例如，给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]，你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0]。

提示：气温 列表长度的范围是 [1, 30000]。每个气温的值的均为华氏度，都是在 [30, 100] 范围内的整数。

##### 解题思路
其实跟滑动窗口最大值那个题的解题思路很像，就是维护一个还没有排序好的栈，每次把栈中比当前遍历元素小的，都出栈，然后计算等待天数，然后将当前元素入栈。
```java
   public int[] dailyTemperatures(int[] T) {
        if (T == null || T.length==0) {
            return null;
        }
        int[] result = new int[T.length];
        Stack<Integer> stack = new Stack<>();
        stack.add(0);
        for (int i = 1; i < T.length; i++) {
            //比栈顶元素小，栈
            if (stack.size() == 0 || T[i] <= T[stack.peek()]) {
                stack.push(i);
            } else {
               // 比栈顶元素大，就一直出栈
                while (stack.size() >0 && T[i] > T[stack.peek()]) {
                    int index = stack.pop();
                    result[index] = i - index;
                }
                stack.push(i);
            }
        }
        return result;
    }
```

### 347.前 K 个高频元素

给定一个非空的整数数组，返回其中出现频率前 k 高的元素。 

示例 1:

输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
示例 2:

输入: nums = [1], k = 1
输出: [1]

##### 解题思路
先使用一个HashMap来统计各元素的频率，然后取前k个元素建立小顶堆，然后对后面的元素进行遍历，如果频率高于堆顶元素，就与堆顶元素交换，然后对堆进行调整。
```java
public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer,Integer> map = new HashMap<Integer,Integer>();
        for(int i = 0;i<nums.length;i++) {
            Integer value = map.get(nums[i]);
            value = value == null ? 1 : value+1;
            map.put(nums[i],value);
        }
        Object[] array =  map.keySet().toArray();
        //进行堆排
        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = (Integer)array[i];
        }
        //建立小顶堆
        for (int i = result.length/2-1; i >= 0; i--) {
            adjustHeap(result,i,result.length,map);
        }
        for (int i = k; i < array.length ; i++) {
            Integer key = (Integer) array[i];
            if (map.get(key) >= map.get(result[0])) {
                result[0] = (Integer)array[i];
                adjustHeap(result,0,result.length,map);
            }
        }
        return result;
    }
    void adjustHeap(int[] array, int i,int length,HashMap<Integer,Integer> map) {
        while (2*i+1<length) {//左节点存在
            int left = 2*i+1;
            int right = 2*i+2;

            int min = map.get(array[i]) < map.get(array[left]) ? i : left;
            if (right<length && map.get(array[right]) < map.get(array[min])) {//右节点存在，并且还比最大值大
                min = right;
            }
            if (min == i) {//当前已经是最小值
                break;
            } else {//左节点或者右节点是最小值，那么就将当前节点与最小的节点交换
                swap(array,i,min);
                i = min;
            }
        }
    }
    void swap(int[] array, int a,int b) {
        int temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
```

## 49. 字母异位词分组
给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

示例:

输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
说明：

所有输入均为小写字母。
不考虑答案输出的顺序。

##### 解题思路
就是对字符串进行遍历，取出每个字符串，计算它对应的字典序最小的字符串(例如："ate","eat","tea"，他们字典序最小的字符串是"aet")，然后判断map中是否存在，不存在就创建一个List，将字符串添加进去，存在就在原List中添加该字符串。
```java
  public List<List<String>> groupAnagrams(String[] strs) {
        HashMap<String,List> map = new HashMap<String,List>();
        for (String str : strs) {
            char[] charArray = str.toCharArray();
            Arrays.sort(charArray);
            String sortedString = new String(charArray);
            List strList = map.get(sortedString);
            if (strList == null) {
                strList = new LinkedList();
            }
            strList.add(str);
            map.put(sortedString,strList);
        }
        ArrayList totalList = new ArrayList<>();
        totalList.addAll(map.values());
        return totalList;
    }
```

### 32.最长有效括号

给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。

示例 1:

输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"
示例 2:

输入: ")()())"
输出: 4
解释: 最长有效括号子串为 "()()"

##### 解题思路

判断一个字符是否在是有效括号，就是对字符串遍历，如果是(，就将(字符入栈，如果是）字符，就判断栈是否为有元素，有元素代表括号匹配上了，将当前括号和栈顶元素括号都标记为有效括号。标记的方式是将recordArray数组中相应下标置为1，然后统计最长有效括号就是统计recordArray数组中连续1的个数。

```java
		public int longestValidParentheses(String s) {
        Stack<Integer> stack = new Stack();

        int[] recordArray = new int[s.length()];
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c=='(') {
                stack.push(i);
            } else if (c==')' && stack.size()>0) {
                //说明这个位置可以匹配
                recordArray[i]=1;
                int leftIndex = stack.pop();
                recordArray[leftIndex] = 1;
            }
        }
        //统计recordArray张连续1的长度
        int max = 0;
        int currentSize =0;
        for (int i = 0; i < recordArray.length; i++) {
            if (recordArray[i] ==0) {
                currentSize=0;
            } else if (recordArray[i] ==1) {
                currentSize++;
            }
            max = max > currentSize ? max : currentSize;
        }
        return max;
    }
```

### 543. 二叉树的直径

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

 

示例 :
给定二叉树

          1
         / \
        2   3
       / \     
      4   5    
返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。

##### 解题思路
其实根节点的直径就是左节点深度+右节点深度，其他节点也是这个公示，所以可以递归求解出左右节点的深度，然后计算当前节点的直径，然后与左右节点的直径进行判断，返回最大值。
```java
    public int diameterOfBinaryTree(TreeNode root) {
        if (root==null){return 0;}
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);
        int rootDiameter = leftDepth+rightDepth+1;
        int leftDiameter =  diameterOfBinaryTree(root.left);
        int rightDiameter =  diameterOfBinaryTree(root.right);
        int maxDiameter = rootDiameter > leftDiameter ? rootDiameter : leftDiameter;
        maxDiameter = maxDiameter > rightDiameter ? maxDiameter:rightDiameter;
        return maxDiameter;
    }
    public int maxDepth(TreeNode root) {
        if (root==null) {return 0;}
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);
        return leftDepth>rightDepth?leftDepth+1 : rightDepth+1;
    }
```

### 79. 单词搜索
给定一个二维网格和一个单词，找出该单词是否存在于网格中。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

 

示例:

board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

给定 word = "ABCCED", 返回 true
给定 word = "SEE", 返回 true
给定 word = "ABCB", 返回 false
##### 解题思路
就是遍历数组，判断每个字符与字符串首字符是否相同，相同的话就继续判断周围的字符是否满足要求。需要注意的时，在判断时会把走过的路径添加到HashSet中去，防止一个位置的字符重复被使用。
```java
HashSet<String> set = new HashSet<>();
    public boolean exist(char[][] board, String word) {
        if (board==null||board[0]==null||word==null||word.length()==0) {
            return false;
        }
        char firstChar = word.charAt(0);
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if(board[i][j] == firstChar
                        && judgeTheChar(board, word,1,i,j) == true) {
                    return true;
                }
            }
        }
        return false;
    }

    boolean judgeTheChar(char[][] board,String word,int index,int i,int j) {

        String key = i + "-" + j;
        if (set.contains(key)) {
            return false;
        } else {
            set.add(key);
        }
        if (index>= word.length()) {
            return true;
        }
        char currentChar = word.charAt(index);
        //上
        if (i-1>=0 &&
                board[i-1][j] == currentChar && judgeTheChar(board, word,index+1,i-1,j) == true) {
            return true;
        }
        //下
        if (i+1<board.length &&
                board[i+1][j] == currentChar
                && judgeTheChar(board, word,index+1,i+1,j) == true) {
            return true;
        }
        //左
        if (j-1>=0 &&
                board[i][j-1] == currentChar
                && judgeTheChar(board, word,index+1,i,j-1) == true) {
            return true;
        }

        if (j+1< board[0].length &&
                board[i][j+1] == currentChar
                && judgeTheChar(board, word,index+1,i,j+1) == true) {
            return true;
        }
        set.remove(key);
        return false;
    }
```

### 96.不同的二叉搜索树
给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？

示例:

输入: 3
输出: 5
解释:
给定 n = 3, 一共有 5 种不同结构的二叉搜索树:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3

##### 解题思路
就是把一个二叉搜索树的组合种数，其实是左子树的组合数乘以右子树的组合数，假设n为4，f(n)代表组合种数，二叉树共有四个节点，那么二叉树肯定有根节点，组合主要分为以下几类：
1.左子树有0个节点，右子树有3个节点，f(0)*f(3)
2.左子树有1个节点，右子树有2个节点,f(1)*f(2)
3.左子树有2个节点，右子树有1个节点,f(2)*f(1)
4.左子树有3个节点，右子树有0个节点,f(3)*f(0)
所以，f(4)=f(0)*f(3)+f(1)*f(2)+f(2)*f(1)+f(3)*f(0)
```java
public int numTrees(int n) {
        int[] array = new int[n+1];
        array[0] = 1;
        for (int i = 1; i <= n; i++) {
            int num = 0;
            for (int j = 0; j <=i-1; j++) {
                num += array[j]*array[i-1-j];
            }
            array[i] = num;
        }
        return array[n];
    }
```

### 146.LRU 缓存机制
运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制 。
实现 LRUCache 类：

LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

##### 解题思路
LinkedHashMap本身是基于HashMap做了一些扩展，就是通过链表将所有键值对连接起来了，链接的就是添加键值对的顺序。(新添加的键值对在链表尾部，put方法对于已存在的key进行值覆盖时，是不会修改键值对在链表中的顺序的)
所以我们可以基于LinkedHashMap实现LRU。
LRU的get()方法
1.map中不存在这个key，返回-1
2.map中存在这个key，先remove这个键值对，再put操作添加这个键值对，再返回value。(这样可以修改键值对在链表中的顺序。)
LRU的put()方法
1.已存在这个key，先remove，再put。
2.不存在这个key，判断是否超过容量，超过将最后一个键值对移除，将键值对添加到map。
```java
LinkedHashMap<Integer, Integer> map = new LinkedHashMap();
        int capacity;

        public LRUCache(int capacity) {
            this.capacity = capacity;
        }

        public int get(int key) {
            Integer value = map.get(key);
            if (value == null) {
                return -1;
            }
            map.remove(key);
            map.put(key,value);
            return value;
        }

        public void put(int key, int value) {
            Integer oldValue = map.get(key);
            if (oldValue!=null) {
                //只是覆盖value的话，put方法不会改变键值对在链表中的顺序，所以需要先remove
                map.remove(key);
                map.put(key,value);
                return;
            }
            if (map.size()>=capacity) {
                map.remove(map.keySet().iterator().next());
            }
            map.put(key,value);
        }
```

### 84. 柱状图中最大的矩形

给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

![img](../static/histogram.png) 



以上是柱状图的示例，其中每个柱子的宽度为 1，给定的高度为 [2,1,5,6,2,3]。

![img](../static/histogram_area.png) 



图中阴影部分为所能勾勒出的最大矩形面积，其面积为 10 个单位。

示例:

输入: [2,1,5,6,2,3]
输出: 10

##### 解题思路

可以暴力求解，就对每个height[i]都计算包含它的元素的最大高度，这样复杂度是O(N^2)，也可以使用一个栈来计算遍历过程中比height[i]小的元素，

1.发现栈顶元素<height[i],将height[i]入栈。

2.发现栈顶元素==height[i]，对栈顶元素进行替换。

3.发现栈顶元素>height[i]时，那么进行出栈操作，并且计算出栈的元素组成的矩形面积。范围是[stack.peak()+1,i-1]

```java
public int largestRectangleArea(int[] heights) {
        if (heights==null||heights.length==0) {
            return 0;
        }
        int maxArea = 0;
        Stack<Integer> stack = new Stack();
        stack.add(0);
        for (int i = 1; i <= heights.length; i++) {
            int currentH;
            if (i==heights.length) {//这是最后新增了一个0元素，
                // 防止整个数组是全体递增的，这样会计算不出面积
                currentH =0;
            } else {
                currentH = heights[i];
            }

            if(currentH > heights[stack.peek()]) {
                stack.push(i);
            } else if (currentH == heights[stack.peek()]) {
                stack.pop();
                stack.push(i);
            } else {//当heights[i] < stack.peek()时，进行出栈计算
                while (stack.size()>0 && currentH<heights[stack.peek()]) {
                    Integer index = stack.pop();
                    //此时area的范围是[stack.peek()+1,i-1]
                    int leftIndex;
                    int rightIndex = i-1;
                    if (stack.size()>0) {
                        leftIndex = stack.peek() + 1;
                    } else {
                        leftIndex = 0;
                    }
                    int area = heights[index] * (rightIndex - leftIndex+1);
                    maxArea = maxArea > area ? maxArea : area;
                }
                stack.push(i);
            }
        }
        return maxArea;
    }
```

### 148. 排序链表

给你链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

进阶：

你可以在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序吗？

示例 1：

![img](../static/sort_list_1.jpg)


输入：head = [4,2,1,3]
输出：[1,2,3,4]

##### 解题思路

就是归并排序

```java
		ListNode preHead = new ListNode(1);
    ListNode sortList(ListNode start) {
        if (start.next == null || start==null) {return start;}
        ListNode quick = start;
        ListNode slow = start;
        while (quick!=null) {
            quick=quick.next;
            if (quick.next==null){break;}
            quick = quick.next;
            slow = slow.next;
        }
        //4 2 1 3
        ListNode otherStart = slow.next;
        slow.next = null;
        //对链表分解和合并后，新链表的头结点不一定还是start
        start = sortList(start);
        otherStart = sortList(otherStart);
        //开始merge
        //preHead用于我们来保存新组成的链表的头结点
        ListNode currentNode = preHead;
        while (start != null && otherStart!=null) {
            if (start.val<otherStart.val) {
                currentNode.next = start;
                currentNode=currentNode.next;
                start=start.next;
            } else {
                currentNode.next = otherStart;
                currentNode=currentNode.next;
                otherStart=otherStart.next;
            }
        }
        while (start!=null) {
            currentNode.next = start;
            currentNode=currentNode.next;
            start=start.next;
        }
        while (otherStart!=null) {
            currentNode.next = otherStart;
            currentNode=currentNode.next;
            otherStart=otherStart.next;
        }
        return preHead.next;
    }
```

### 617.合并二叉树
给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。

你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为 NULL 的节点将直接作为新二叉树的节点。

示例 1:

输入: 
	Tree 1                     Tree 2                  
          1                         2                             
         / \                       / \                            
        3   2                     1   3                        
       /                           \   \                      
      5                             4   7                  
输出: 
合并后的树:
	     3
	    / \
	   4   5
	  / \   \ 
	 5   4   7

##### 解题思路
```
		public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if(t1==null || t2==null) {
            return t1==null? t2 : t1;
        }
        t1.val = t1.val+t2.val;
        t1.left = mergeTrees(t1.left,t2.left);
        t1.right = mergeTrees(t1.right,t2.right);
        return t1;
    }
```

### 287.寻找重复数
给定一个包含 n + 1 个整数的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

示例 1:

输入: [1,3,4,2,2]
输出: 2
示例 2:

输入: [3,1,3,4,2]
输出: 3
说明：

不能更改原数组（假设数组是只读的）。
只能使用额外的 O(1) 的空间。
时间复杂度小于 O(n2) 。
数组中只有一个重复的数字，但它可能不止重复出现一次。

##### 解题思路
这个题主要是不能使用额外的辅助空间，不然可以使用一个数组或者HashMap记录遍历过程中出现的整数，然后判断当前数是否重复。本题只能使用数组本身来记录整数是否出现，所以可以将每个数nums[i]交换到以nums[i]作为下标的地方，如果此时以存在相同的数，说明重复，否则就对交换后的数继续遍历。

### 152. 乘积最大子数组
给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

 

示例 1:

输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
示例 2:

输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。

##### 解题思路
因为本题所有的都是整数，所以乘积肯定是越乘越大，只有碰到0会变成0，碰到负数会符号改变，所以主要分为以下几种情况：
##### 1.数组中没有0存在
1.1 负数的个数为偶数个
最大值就是所有元素的乘积
1.2 负数的个数为奇数个
那么最大值就是第一个负数后面的所有的元素的乘积，或者是最后一个负数前面所有元素的乘积
##### 2.假设数组中有0存在
那么其实就是根据0将数组切分成子数组了，对每个子数组计算最大值。
```java
public int maxProduct(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        if (nums.length == 1) {
            return nums[0];
        }
        int start = 0;
        int max = 0;
        int times = 0;
        //根据0对数组进行切分，对每个子数组计算最大乘积和
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] <0) {//统计负数的个数
                times++;
            }
            if (nums[i] == 0 || i == nums.length-1) {
                int end = nums[i] == 0 ? i-1 : i;
                int value = maxProduct(nums,start,end,times);
                start=i+1;
                max = max>value?max:value;
                times =0;
            }
        }
        return max;
    }
    public int maxProduct(int[] nums,int start,int end,int times) {
        if (start>end) {
            return 0;
        }
        if (start==end) {
            return nums[start];
        }
        if (times%2 == 0) {//负数为偶数个
            return calculateValue(nums,start,end);
        } else {//
            //第一个负数的下标
            Integer firstNegativeIndex=null;
            //最后一个负数的下标
            Integer lastNegativeIndex=null;
            for (int i = start; i <= end; i++) {
                if (nums[i] < 0 && firstNegativeIndex == null) {
                    firstNegativeIndex = i;
                }

                if (nums[i] < 0) {
                    lastNegativeIndex = i;
                }
            }
            int vaule1 = calculateValue(nums,start,lastNegativeIndex-1);
            int value2 = calculateValue(nums,firstNegativeIndex+1,end);
            return vaule1>value2?vaule1:value2;
        }
    }
    //计算start到end之间的元素乘积和
    int calculateValue(int[] nums, int start, int end) {
        if (start>end) {
            return 0;
        }
        int result = 1;
        for (int i = start; i <= end; i++) {
            result*=nums[i];
        }
        return result;
    }
```

### 139. 单词拆分
给定一个非空字符串 s 和一个包含非空单词的列表 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

拆分时可以重复使用字典中的单词。
你可以假设字典中没有重复的单词。
示例 1：

输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
示例 2：

输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。

##### 解题思路
就是对于字符串"abc"来说,可以拆分成"a"和"bc"，如果说字典中存在"a"，我们只需要递归调用函数对剩余字符"bc"进行判断，判断"bc"是否能分解。最坏复杂度应该是O(N^2)。这里优化的点就是每次判断时，如果计算得到子串"bc"不能被分解，我们应该将它添加到falseSet，避免重复计算。
```java
HashSet<String> falseSet = new HashSet<String>();
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s==null||s.length()==0) {return true;}
        if (falseSet.contains(s)) {
            return false;
        }
        HashSet<String> wordSet = new HashSet<>(wordDict);
        for (int i = 0; i < s.length(); i++) {
            //当前子串
            String subString = s.substring(0,i+1);
            //剩余子串
            String restString = s.substring(i+1,s.length());
            //如果剩余子串存在，那么就分解成功了
            if (wordSet.contains(subString) && wordBreak(restString, wordDict)) {
                return true;
            }
        }
        //分解不成功，将子串添加到falseSet，避免重复计算
        falseSet.add(s);
        return false;
    }
```

### 239. 滑动窗口最大值
给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。
返回滑动窗口中的最大值。
示例 1：
输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
##### 解题思路
其实是可以维护一个已排序好的队列，每次将刚进窗口的值，添加到排序好的队列中，像插入排序的原理一样，然后取最大值的时候就去队列头部取，然后如果是超出窗口左边界的，就丢掉，没有超过就使用。但是主要问题在于每次插入时的平均时间复杂度是k/2,总时间复杂度就是Nk/2，当k接近于n时，这个复杂度就是O(N^2)了，所以在往队列中插入时，要有排除机制，假设队列是[6,5,4,2]四个数，此时要插入的元素是3，那么在插入时可以把2直接丢掉，因为元素2是3之前的元素，下标更小，值也比3小，在后面的遍历过程中是不可能再成为最大值的，所以通过把2删除掉，这样就可以减少插入时，比次数，将时间复杂度降低为O(N).
```java
public int[] maxSlidingWindow(int[] nums, int k) {
        int[] result = new int[nums.length-k+1];
        LinkedList<Integer> maxQueue = new LinkedList<>();
        for (int i = 0; i < nums.length; i++) {
            //将队列中小于当前数的元素出队列
            while (maxQueue.size()>0 && nums[i] > nums[maxQueue.getLast()]) {
                maxQueue.removeLast();
            }
            maxQueue.add(i);
            //窗口的左边界
            int left = i - k+1;
            //将队列中出边界的最大值移除
            if (maxQueue.getFirst() <left) {
                maxQueue.removeFirst();
            }
            //左边界全部到数组中时，才需要计算最大值
            if (left>=0) {
                result[left] = nums[maxQueue.getFirst()];
            }
        }
        return result;
    }

```



### 124. 二叉树中的最大路径和
给定一个非空二叉树，返回其最大路径和。

本题中，路径被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。该路径至少包含一个节点，且不一定经过根节点。

示例 1：

输入：[1,2,3]

       1
      / \
     2   3

输出：6
示例 2：

输入：[-10,9,20,null,null,15,7]

   -10
   / \
  9  20
    /  \
   15   7

输出：42
##### 解题思路
这个题由于不要求路径开始和路径结束的节点是叶子节点，所以是有点复杂，我们可以先定义一个函数，这个函数可以计算每个节点单条路径的长度，就是可以是节点本身，也可以是节点+左子树中的节点连接，也可以是节点+左子树中的节点连接，但是节点不能既连接左节点，由连接右节点。在遍历过程中，可以得到(根节点，根节点+左子树路径最大值，根节点+右子树路径最大值)三者的最大值，然后进行返回。在遍历的过程中，由于我们知道左子树路径最大值和右子树路径最大值，我们可以计算以该节点为根节点的路径最大值，然后与totalMax进行比较和替换。

```java
//因为最大路径出现时，肯定是有一个根节点的，所以在计算单条路径时，
    // 可以同时计算最大路径和，然后存储到totalMax
    Integer totalMax = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        siglePath(root);
        return totalMax;
    }

    //这里计算的是单条路径长度,就是根节点+左路+右路的
    // 根节点，根节点+左节点中的路径，根节点+右节点中的路径
    //但是不能是左节点中的路径+根节点+右节点的路径
    public int siglePath(TreeNode root) {
        if (root == null) {
            return 0;
        }
        //左边单条路径的长度
        int leftPath = siglePath(root.left);
        leftPath = leftPath>0?leftPath:0;
        int rightPath = siglePath(root.right);
        rightPath = rightPath>0?rightPath:0;
        //根节点+左路大，还是根节点+右路大
        int max = leftPath > rightPath ?
                leftPath + root.val : rightPath + root.val;
        //实时计算左路单条路径+根节点+右路单条路径哪个大
        totalMax = leftPath+rightPath+root.val > totalMax ? leftPath+rightPath+root.val : totalMax;
        return max;
    }
```
### 461. 汉明距离
两个整数之间的汉明距离指的是这两个数字对应二进制位不同的位置的数目。
给出两个整数 x 和 y，计算它们之间的汉明距离。
注意：
0 ≤ x, y < 231.

示例:

输入: x = 1, y = 4

输出: 2

解释:
1   (0 0 0 1)
4   (0 1 0 0)
    	 ↑     ↑
上面的箭头指出了对应二进制位不同的位置。

##### 解题思路

```java
public int hammingDistance(int x, int y) {
        int result = x^y;
        int num=0;
        while(result>0) {
            if((result&1) == 1) {
                num++;
            }
            result = result>>1; 
        } 
        return num;
    }
```
