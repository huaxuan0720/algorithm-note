## LeetCode刷题笔记

### Top-K问题

#### 解法一：排序加选择

#### 解法二：堆结构

#### 解法三：利用快排的Partition和递归进行选择

```java
import java.util.*;

public class Solution {
    private static void shuffle(int[] array) {
        for (int i = 0; i < array.length; i++) {
            int position = (int) (Math.random() * array.length);
            int temp = array[position];
            array[position] = array[i];
            array[i] = temp;
        }
    }

    // Solution1
    private static int kthMin1(int[] array, int k) {
        Arrays.sort(array);
        return array[k];
    }

    // Solution2
    private static int kthMin2(int[] array, int k) {
        PriorityQueue<Integer> heap = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        }
        );
        for (int i = 0; i <= k; i++) {
            heap.add(array[i]);
        }
        for (int i = k + 1; i < array.length; i++) {
            if (array[i] < heap.peek()) {
                heap.remove();
                heap.add(array[i]);
            }
        }
        return heap.peek();
    }

    // Solution3
    private static int kthMin3(int[] array, int k) {
        int left = 0;
        int right = array.length - 1;
        int position = partition(array, left, right);
        while (position != k) {
            if (position > k) {
                right = position - 1;
            } else {
                left = position + 1;
            }
            position = partition(array, left, right);
        }
        return array[position];
    }

    public static int partition(int[] array, int lo, int hi) {
        int key = array[lo];
        int i = lo;
        int j = hi;
        while (i < j) {
            // 注意这个计算的顺序问题，需要从远端开始
            while (array[j] >= key && i < j) j--;
            while (array[i] <= key && i < j) i++;
            swap(array, i, j);
        }
        swap(array, lo, i);
        return j;
    }

    private static void swap(int[] array, int pos1, int pos2) {
        int temp = array[pos1];
        array[pos1] = array[pos2];
        array[pos2] = temp;
    }


    public static void main(String[] args) {
        int[] array = new int[100];
        for (int i = 0; i < 100; i++) {
            array[i] = (int) (Math.random() * 10000);
        }
        shuffle(array);
        int a = kthMin1(array, 10);
        shuffle(array);
        int b = kthMin2(array, 10);
        shuffle(array);
        int c = kthMin3(array, 10);
        System.out.println(a + " " + b + " " + c);

    }
}
```



#### 解法四：BFPRT算法（本质是对解法三的改进）

```java
import java.util.*;

public class Solution {
    private static void shuffle(int[] array) {
        for (int i = 0; i < array.length; i++) {
            int position = (int) (Math.random() * array.length);
            int temp = array[position];
            array[position] = array[i];
            array[i] = temp;
        }
    }

    // Solution1
    private static int kthMin1(int[] array, int k) {
        Arrays.sort(array);
        return array[k];
    }

    // Solution2
    private static int kthMin2(int[] array, int k) {
        // 建立一个大根堆
        PriorityQueue<Integer> heap = new PriorityQueue<>(new Comparator<Integer>() {
            // 定义大根堆的比较器
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        }
        );
        for (int i = 0; i <= k; i++) {
            heap.add(array[i]);
        }
        for (int i = k + 1; i < array.length; i++) {
            if (array[i] < heap.peek()) {
                heap.remove();
                heap.add(array[i]);
            }
        }
        return heap.peek();
    }

    // Solution3
    private static int kthMin3(int[] array, int k) {
        int left = 0;
        int right = array.length - 1;
        int position = partition(array, left, right);
        while (position != k) {
            if (position > k) {
                right = position - 1;
            } else {
                left = position + 1;
            }
            position = partition(array, left, right);
        }
        return array[position];
    }

    public static int partition(int[] array, int lo, int hi) {
        int key = array[lo];
        int i = lo;
        int j = hi;
        while (i < j) {
            // 注意这个计算的顺序问题，需要从远端开始
            while (array[j] >= key && i < j) j--;
            while (array[i] <= key && i < j) i++;
            swap(array, i, j);
        }
        swap(array, lo, i);
        return j;
    }

    private static void swap(int[] array, int pos1, int pos2) {
        int temp = array[pos1];
        array[pos1] = array[pos2];
        array[pos2] = temp;
    }

    // Solution 4
    /**
     * 通过BRPRT算法获得Top-K问题的解，时间复杂度为O(N)
     *
     * @param arr
     * @param k
     */
    private static int[] getMinKNumsByBFPRT(int[] arr, int k) {
        if (k < 1 || k > arr.length) {
            return arr;
        }
        int minKth = getMinKthByBFPRT(arr, k);
        int[] res = new int[k];
        int index = 0;
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] < minKth) {
                res[index++] = arr[i];
            }
        }
        for (; index < res.length; index++) {
            res[index] = minKth;
        }
        return res;
    }

    private static int getMinKthByBFPRT(int[] arr, int k) {
        int[] copyArr = copyArray(arr);
        return select(copyArr, 0, copyArr.length - 1, k);
    }

    private static int[] copyArray(int[] arr) {
        int[] res = new int[arr.length];
        for (int i = 0; i < res.length; i++) {
            res[i] = arr[i];
        }
        return res;
    }

    private static int select(int[] arr, int begin, int end, int i) {
        if (begin == end) {
            return arr[begin];
        }
        int pivot = medianOfMedians(arr, begin, end);
        int[] pivotRange = partition(arr, begin, end, pivot);
        if (i >= pivotRange[0] && i <= pivotRange[1]) {
            return arr[i];
        } else if (i < pivotRange[0]) {
            return select(arr, begin, pivotRange[0] - 1, i);
        } else {
            return select(arr, pivotRange[1] + 1, end, i);
        }
    }

    private static int medianOfMedians(int[] arr, int begin, int end) {
        int num = end - begin + 1;
        int offset = num % 5 == 0 ? 0 : 1;
        int[] mArr = new int[num / 5 + offset];
        for (int i = 0; i < mArr.length; i++) {
            int beginI = begin + i * 5;
            int endI = beginI + 4;
            mArr[i] = getMedian(arr, beginI, Math.min(end, endI));
        }
        return select(mArr, 0, mArr.length - 1, mArr.length / 2);
    }

    private static int[] partition(int[] arr, int begin, int end, int pivotValue) {
        int small = begin - 1;
        int cur = begin;
        int big = end + 1;
        while (cur != big) {
            if (arr[cur] < pivotValue) {
                swap(arr, ++small, cur++);
            } else if (arr[cur] > pivotValue) {
                swap(arr, cur, --big);
            } else {
                cur++;
            }
        }
        int[] range = new int[2];
        range[0] = small + 1;
        range[1] = big - 1;
        return range;
    }

    private static int getMedian(int[] arr, int begin, int end) {
        insertionSort(arr, begin, end);
        int sum = end + begin;
        int mid = (sum / 2) + (sum % 2);
        return arr[mid];
    }

    private static void insertionSort(int[] arr, int begin, int end) {
        for (int i = begin + 1; i != end + 1; i++) {
            for (int j = i; j != begin; j--) {
                if (arr[j - 1] > arr[j]) {
                    swap(arr, j - 1, j);
                } else {
                    break;
                }
            }
        }
    }



    public static void main(String[] args) {
        int[] array = new int[20];
        for (int i = 0; i < 20; i++) {
            array[i] = (int) (Math.random() * 10000);
        }
        shuffle(array);
        int a = kthMin1(array, 10);
        shuffle(array);
        int b = kthMin2(array, 10);
        shuffle(array);
        int c = kthMin3(array, 10);
        shuffle(array);
        int d = getMinKthByBFPRT(array, 10);
        System.out.println(a + " " + b + " " + c + " " + d);

    }
}

```

#### 统计单词的词频



### 自改写的堆结构

请实现如下的结构

```java
TopRecord{
    // 构造时事先指定好k的大小，构造后就固定不变了
    public TopRecord(int k);
    
    // 向该结构中加入一个字符串，可以重复加入
    public void add(String str);
    
    // 返回之前加入的所有的字符串中，词频最大的k个
    public List<String> top();
}
```

算法要求：

add方法，复杂度为$O(log k)$

top 方法，复杂度为$O(k)$

#### 解法

这一题我们需要对三组数据进行统计，具体的含义见代码。

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

public class TopKRealTime {
    // 节点数据的定义
    public static class Node {
        public String str;
        public int times;

        public Node(String s, int t) {
            str = s;
            times = t;
        }
    }

    // 堆结构，注意，这个堆是一个小根堆
    private Node[] heap;

    // 堆目前的节点数目，也指向堆中的第一个可以插入节点的位置
    private int heapSize;

    // hash表，记录字符串对应的词频
    private HashMap<String, Node> strNodeMap;

    // hash表，记录每一个节点在堆中的位置
    private HashMap<Node, Integer> nodeIndexMap;

    public TopKRealTime(int k) {
        heap = new Node[k];
        heapSize = 0;
        strNodeMap = new HashMap<>();
        nodeIndexMap = new HashMap<>();
    }

    public void add(String str) {
        Node curNode = null;
        int preIndex = -1; // 记录str之前在堆上的位置

        // 查词频表，看看有没有关于这个str的记录
        if (!strNodeMap.containsKey(str)) { // 如果str之前没有出现过
            curNode = new Node(str, 1);
            strNodeMap.put(str, curNode);
            nodeIndexMap.put(curNode, -1);
        } else { // 如果str之前出现过
            curNode = strNodeMap.get(str);
            curNode.times += 1;
            preIndex = nodeIndexMap.get(curNode);
        }

        // 词频表修改完毕
        if (preIndex == -1) { // 不在堆上
            if (heapSize == heap.length) { // 堆已经满了
                if (heap[0].times < curNode.times) {
                    // 移除目前堆中词频最小的字符串，并将当前的节点放到堆顶的位置
                    nodeIndexMap.put(heap[0], -1);
                    nodeIndexMap.put(curNode, 0); // 将这个节点的在堆中的位置放到hash表中
                    heap[0] = curNode;

                    // 堆顶的数据更新之后，可能需要对堆的结构进行调整
                    heapify(0, heapSize);
                }
            } else { // 堆没有满
                // 将当前的节点放在堆的最后的一个位置
                nodeIndexMap.put(curNode, heapSize);
                heap[heapSize] = curNode;

                // 插入新的节点之后，需要对堆结构进行调整
                heapInsert(heapSize++);
            }
        } else { // str 已经在堆上了
            heapify(preIndex, heapSize);
        }
    }

    // 直接遍历取出数据即可
    public List<String> top() {
        ArrayList<String> ans = new ArrayList<>();
        for (int i = 0; i < heapSize; i++) {
            ans.add(heap[i].str);
        }
        return ans;
    }

    // 自顶向下进行调整
    private void heapify(int index, int heapSize) {
        int l = index * 2 + 1;
        int r = index * 2 + 2;
        int smallest = index;
        while (l < heapSize) {
            if (heap[l].times < heap[index].times) {
                smallest = l;
            }
            if (r < heapSize && heap[r].times < heap[smallest].times) {
                smallest = r;
            }
            if (smallest != index) {
                swap(smallest, index);
            } else {
                break;
            }
            index = smallest;
            l = index * 2 + 1;
            r = index * 2 + 2;
        }
    }

    private void swap(int index1, int index2) {
        nodeIndexMap.put(heap[index1], index2);
        nodeIndexMap.put(heap[index2], index1);
        Node temp = heap[index1];
        heap[index1] = heap[index2];
        heap[index2] = temp;
    }

    // 自底向上的调整
    private void heapInsert(int index) {
        while (index != 0) {
            int parent = (index - 1) / 2;
            if (heap[index].times < heap[parent].times) {
                swap(parent, index);
                index = parent;
            } else {
                break;
            }
        }
    }
}

```



### 逆序对的计算

#### 解法一：暴力求解

两层循环，分别计算一前一后的数值对是否是逆序对，统计是否是一个有效的逆序对，是就加1，不是就加0，最后返回统计的结果。

#### 解法二：归并排序





### 天际线及其相关问题

在一个工厂中，每种工作都有难度和报酬，定义如下：

```java
class Job{
    public int money;
    public int hardness;
}
```

给定一个Job类型的数组jobArr，表示所有岗位，每一个岗位都可以提供任意份工作，选工作的标准是在难度不超过自身能力值的情况下，选择报酬最高的岗位。

给定一个int类型的数组arr，表示所有人的能力，返回一个int类型的数组，表示每个人按照标准选择工作之后所能获得的最高的报酬。

#### 解法

实际上，这一题的思路并不难，我们可以将jobArr按照难度递增的顺序进行排序，如果两个任务的难度相同，则按照给的报酬的大小进行降序排序。排完序之后，在相同的难度上，只保留报酬最高的那一份工作，剩下的舍弃。这是因为在难度相同的情况下，我们希望得到更高的报酬。

这样我们就得到了一个新的job序列，不妨记作jobArr2。对jobArr2内部的工作序列，如果存在工作难度增大，相应的报酬却减小的情况，则舍弃掉这一份工作。最后我们可以得到一个新的数组，记作jobArr3。

jobArr3内部的数据，是严格的升序的，难度越高，报酬越高。

循环遍历工人的数组，可以利用二分的方法计算结果。

#### 更多的思考

如果每一份工作都不是无限提供，而是只有一个，那么，我们依然可以根据难度进行排序，对每一个难度，我们为每一个难度增加一个大根堆，保持每一个难度当前的工作都是最高的报酬，然后和之前的做法一致，利用二分的方法求得合适的难度，然后向前找报酬最高的工作。

（可以设置每一个节点保存前面所有的报酬的最大值和难度，并按照要求更新）

#### 

### 最长公共子序列

**注意，公共子序列和公共子串是不一样的问题，公共子序列不要求连续，公共字串必须要求连续。**





### 前缀树

给定一个字符串类型的数组arr，如

```java
String[] arr = {"b\st", "d\", "a\d\e", "a\b\c"};
```

把这些路径中蕴含的目录结构给打印出来，子目录直接列在父目录下面，并比父目录向右缩进两个。

```txt
a
  b
    c
  d
    e
d
  st
d
```

同一级的目录需要按照字符串的升序排列

#### 解答

本质上，这个问题是一个建立前缀树的问题，按照题目的要求建立好前缀树然后进行DFS，深度优先搜索即可。

之所以采用TreeMap结构保存指向的下一个节点是因为TreeMap可以按照存储的key值对内容进行排序。

```java
import java.util.TreeMap;

public class FolderTree {
    // 定义前缀树的节点结构
    public static class Node {
        // 上一个节点是通过哪一条路到这一个节点
        public String path;

        // key: node 下级的路径名称， value： node在key这一条路上对应的节点是什么
        public TreeMap<String, Node> nextMap;

        public Node(String p) {
            this.path = p;
            nextMap = new TreeMap<>();
        }
    }

    // folderPaths -> ["a\b\c", "a\b\c", "a\d\e"]
    public static void print(String[] folderPaths) {
        if (folderPaths == null || folderPaths.length == 0) {
            return;
        }

        // 根据所有的路径的字符串，将前缀树生成好，头节点(根节点）是head
        Node head = generateFolderTree(folderPaths);

        // 打印前缀树
        printProcess(head, 0);
    }

    private static Node generateFolderTree(String[] folderPaths) {
        Node head = new Node(""); // 系统的根目录，前缀树的根节点
        for (String folderPath : folderPaths) { // 对每一个绝对路径
            String[] paths = folderPath.split("\\\\");
            Node cur = head;
            for (int i = 0; i < paths.length; i++) {
                if (!cur.nextMap.containsKey(paths[i])) {
                    cur.nextMap.put(paths[i], new Node(paths[i]));
                }
                cur = cur.nextMap.get(paths[i]);
            }
        }
        return head;
    }

    // 递归打印
    public static void printProcess(Node node, int level) {
        if (level != 0) {
            System.out.println(get4nSpace(level) + node.path);
        }
        for (Node next : node.nextMap.values()) {
            printProcess(next, level + 1);
        }
    }

    private static String get4nSpace(int n) {
        StringBuilder res = new StringBuilder();
        for (int i = 1; i < n; i++) {
            res.append("    ");
        }
        return res.toString();
    }
}

```



### 子数组的最大累加和

给定一个数组arr，返回子数组的最大的累加和。[LeetCode原题](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/submissions/)

#### 解法一：简单粗暴

计算出所有的子数组，然后求和，统计所有的和的最大值。

#### 解法二：

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int max = Integer.MIN_VALUE;
        int current = 0;
        for (int n: nums){
            current += n;
            max = Math.max(max, current);
            current = Math.max(0, current);
        }
        return max;
    }
}
```

#### 思考

如果给定的是一个二维的数组，求解其中所有的矩形围成的区域的数值之和的最大值。

我们同样可以采用暴力求解的方法，但是这样的复杂度太高

我们可以将二维数组按照行进行划分，第一次求解第0行到第0行之间的子二维数组的最大值，此时二维数组退化成了一个一维数组。再来求解第0行到第1行之间的子二维数组的最大值，此时我们需要将第1行的数据加到第0行上，相当于我们对这个范围内的所有的列进行了相加，我们就将一个二维数组转换成了一个一维数组，对这个数组我们求解对应的子数组的最大和，因为我们已经将列的数据进行了相加，实际上我们求解的数据是一个矩形区域。

接着我们进行第0行到第2行的数据求解，一直到第0行到第N-1行。

就这样我们完成了所有的以第0行的数据为矩形上边界的数据的统计，接着我们就可以开始以第1行的数据为矩形上边界的数据统计，首先计算第1行到第1行的数据统计，接着第1行到第2行，就这样依次进行下去，直到所有的数据都统计完成，最后返回所有的统计结果的最大值即可。

实际上，二维数组的问题就是将列结果进行相加，转化成一个一维数组的问题。

```java
class Solution{
    public static int maxSum(int[][] m){
        if (m == null || m.length == 0 || m[0].length == 0){
            return 0;
        }
        int max = Integer.MIN_VALUE;
        int cur = 0;
        int[] s = null;
        for (int i = 0;i<m.length;i++){ // 开始的行号
            s = new int[m[0].length];
            for (int j = i;j<m.length;j++){ // 对行号之后的所有行
                cur = 0;
                for (int k =0;k<s.length;k++){ // 对列进行求和并求解子数组的最大和
                    s[k] += m[j][k];
                    cur += s[k];
                    max = Math.max(max, cur);
                    cur = Math.max(0, cur);
                }
            }
        }
        return max;
    }

}
```



### 搜索二叉树转换成双向链表

双向链表节点结构和二叉树的节点结构是一样的，如果吧last认为是left，next认为是right的话。

给定一个搜索二叉树的根节点root，请转化成一条有序的双向链表，返回链表的头结点。

#### 解法：

就是将树的左子树递归处理，对右子树进行递归处理，将返回的左子树的链表和右子树的链表进行连接处理即可。

```java
import javax.naming.PartialResultException;
import java.sql.Array;
import java.util.*;

public class Solution {
    // 整棵树，串成双向链表，返回链表的头和尾
    public static class Info{
        public TreeNode start;
        public TreeNode end;
        public Info(TreeNode start, TreeNode end){
            this.start = start;
            this.end = end;
        }
    }
    
    // 以x唯有的整棵搜索二叉树，请全部以有序双向链表的方式，连接好并且返回
    public static Info process(TreeNode x){
        if (x == null){
            return new Info(null, null);
        }
        Info leftInfo = process(x.left);
        Info rightInfo = process(x.right);
        if (leftInfo.end != null){
            leftInfo.end.right = x;
        }
        x.left = leftInfo.end;
        x.right = rightInfo.start;
        if (rightInfo.start != null){
            rightInfo.start.left = x;
        }
        return new Info(
            	// 整棵树的头
                leftInfo.start != null? leftInfo.start: x,
            	// 整棵树的尾
                rightInfo.end != null ? rightInfo.end: x
        );
    }
}

```





### 求解完全二叉树的节点数目（复杂度小于**O(N)**）

### 解法：

这一题可以根据完全二叉树的性质去求解。完全二叉树的特点是除了最后的一层节点之外，其他的层的节点都是满的，最后一层的节点都是全部从左向右排列，也就是尽可能向左靠齐。

对于当前的节点，首先计算以当前节点为根节点的树的最大深度d1，然后计算右子树的最大深度d2，如果两者相等，那么可以判定左子树一定的满的，且左子树的深度一定是d1-1，这样就可以直接计算出左子树的节点数目，接着对右子树进行同样的操作。如果两个不等，那么一定可以确定右子树是满的，且深度就是d2，右子树的节点数目就可以直接计算出来，然后对左子树进行同样的操作，最后返回所有的节点数目。

```java
public class Solution {
    public static int nodeSum(TreeNode head) {
        if (head == null) {
            return 0;
        }
        return bs(head, 1, mostLeftLevel(head, 1));
    }

    // node在第level层，h是总的深度（h永远不变，相当于是一个全局变量）
    // 以node为根节点的完全二叉树，计算节点数目
    private static int bs(TreeNode node, int level, int h) {
        if (level == h) {
            return 1;
        }
        // 如果右子树的深度达到了整棵树的最大深度，那么左子树一定是满的，所以对右子树进行递归，反之则对左子树进行递归
        if (mostLeftLevel(node.right, level + 1) == h) {
            return (1 << (h - level)) + bs(node.right, level + 1, h);
        } else {
            return (1 << (h - level - 1)) + bs(node.left, level + 1, h);
        }
    }

    // 如果node在第level层
    // 求以node为根节点的子树，最大深度是多少
    // node为根节点的子树，是一棵完全二叉树
    private static int mostLeftLevel(TreeNode node, int level) {
        while (node != null) {
            level += 1;
            node = node.left;
        }
        return level - 1;
    }
}
```

#### 复杂度分析

可以发现，我们每次都只对一侧的树进行递归，但是每一次都需要对子树的深度进行计算，由于深度h和节点总数目满足于$h \propto O(logN)$，因此，第一次递归的需要的时间是$logN$，第二次递归的时间是$log(N/2) = logN - 1$，第三次就是$logN - 2$，因此总的时间复杂度就是
$$
logN +(logN - 1) +(logN - 2)+... \propto O((logN)^2)
$$

### 卡特兰数

### LRU（Least Recently Used Algorithm）

LRU是一种常见的内存设计中的页面置换算法，它是基于这样的一个假设，如果一个数据很久都没有被更新，那么在未来的一段时间内，该数据被更新的概率也会很小，如果一个数据经常被更新，那么在未来的一段时间内，该数据被更新的概率会很大。

因此，对于LRU算法来说，我们需要时刻按照数据的最近的更新的时间进行有效的管理。

本质上，该结构可以由一个双向链表实现，当我们对其中的一个数据进行更新之后，将这个数据移动到链表的头部（或者尾部，具体看个人的实现，下同），这样就可以使得双向链表的头部一直都是最近被更新的节点，链表的尾部一直都是最远的没有被更新的节点。

```java
public class Solution {
    // 节点数据，保存一个键值对，可以泛型
    public static class Node<K, V> {
        public K key;
        public V value;

        public Node<K, V> last;
        public Node<K, V> next;

        public Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }

    // 双向链表
    public static class NodeDoubleLinkedList<K, V> {
        private Node<K, V> head;
        private Node<K, V> tail;

        // 构造函数
        public NodeDoubleLinkedList() {
            head = null;
            tail = null;
        }

        // 新增节点
        public void addNode(Node<K, V> newNode) {
            if (newNode == null) {
                return;
            }
            // newNode != null
            if (head == null) { // 双向链表中一个节点都没有
                head = newNode;
                tail = newNode;
            } else { // 将新增的节点加到最尾部
                tail.next = newNode;
                newNode.last = tail;
                tail = newNode;
            }
        }
        
        // 将指定的节点移动到链表的尾部，表示最近被更新过
        public void moveNodeToTail(Node<K, V> node) {
            if (this.tail == null) {
                return;
            }

            if (this.head == node) { // 当前的node是head节点
                this.head = node.next;
                this.head.last = null;
            } else { // 当前的节点是中间的节点
                node.last.next = node.next;
                node.next.last = node.last;
            }
            // 统一更新指针
            node.last = this.tail;
            node.next = null;
            this.tail.next = node;
            this.tail = node;
        }

        // 移除head的节点，即移除最久远的未被使用的节点，并返回（返回的节点用于更新hash表）
        public Node<K, V> removeHead() {
            if (this.head == null) {
                return null;
            }
            Node<K, V> res = this.head;
            if (this.head == this.tail) { // 如果链表中只有一个节点，重置链表即可
                this.head = null;
                this.tail = null;
            } else {
                this.head = res.next;
                res.next = null;
                this.head.last = null;
            }
            return res;
        }
    }

    public static class MyCache<K, V> {
        private HashMap<K, Node<K, V>> keyNodeMap;

        private NodeDoubleLinkedList<K, V> nodeList;

        private final int capacity;


        public MyCache(int capacity) {
            if (capacity < 1) {
                throw new RuntimeException("should be more than 0");
            }
            keyNodeMap = new HashMap<>();
            nodeList = new NodeDoubleLinkedList<>();
            this.capacity = capacity;
        }

        public V get(K key) {
            if (keyNodeMap.containsKey(key)) {
                Node<K, V> res = keyNodeMap.get(key);
                nodeList.moveNodeToTail(res);
                return res.value;
            }
            return null;
        }

        public void set(K key, V value) {
            if (keyNodeMap.containsKey(key)) {
                Node<K, V> node = keyNodeMap.get(key);
                node.value = value;
                nodeList.moveNodeToTail(node);
            } else {
                Node<K, V> newNode = new Node<>(key, value);
                keyNodeMap.put(key, newNode);
                nodeList.addNode(newNode);
                if (keyNodeMap.size() == capacity + 1) {
                    removeMostUnusedNode();
                }
            }
        }

        private void removeMostUnusedNode() {
            Node<K, V> headNode = nodeList.removeHead();
            keyNodeMap.remove(headNode.key);
        }
    }
}

```



### 宽度优先遍历

![image-20210413223917539](..\images\width-first-search.png)

典型的宽度优先遍历的题目，因为题目中涉及的是求最短的路径。宽度优先遍历是一定可以找到最短的路径的，如果是深度优先遍历的话，需要计算出所有的路径才能得出结果。

对于上面的题目，我们首先将上面的list中的数据进行预处理，建立起每一个字符串之间的联系，这个联系表示的是只变换其中的一个字符就可以将两个字符串连接起来。这样我们就可以很方便的进行当前节点的下一个子节点的寻找，只需要向hash表寻找即可。

#### 数据预处理

##### 方式一：传统方法

首先拿出位置0的字符串，然后从位置1开始，逐个进行比较，如果两者只相差1个距离，那么就将位置0的字符串对应的hash表的数组内将位置1的字符串加进去，同时也将位置1的字符串对应的数组内加入位置0的字符串，然后拿位置0的字符串和位置2的字符串进行比较，以此类推。下一个大循环中，拿出位置1的字符串，将位置1的字符串和位置2的字符串进行比较，接着拿位置3的字符串比较，一直循环下去即可建立起一张hash表格。

时间复杂度是$O(N^2 \times k)$，N是数组的长度，k是每一个字符串的长度。

##### 方式二：启发式

如果字符串中只包含了小写字母，那么我们可以对每一个字符串进行遍历，对每一个遍历到的字符串，对每一个位置上的字符进行更改，例如abc，对第0个位置的字符进行更改，可以更改成bbc，然后检查bbc是否在原始的数组中（此前可以把数组中的所有数据放进hash表里，以提升检索速度），如果在的话，就将bbc放进abc对应的数组中，同时也将abc放进bbc对应的数组中，如果不在的话，就什么也不做。然后再将第0个字符边长cbc，接着进行判断，接着是dbc，以此类推。对第0个字符的更改25次，再对第1个位置的字符更改25次，也进行相同的判断，以此类推。最后也可以建立起一张符合要求的hash表。

时间复杂度是$O(25NK)$，N是数组的长度，k是每一个字符串的长度，25表示的是具体的变换次数。

对于上面的两个预处理方法，需要根据具体的题目进行选择。

```java
public class Solution {
    public static List<List<String>> findMinPaths(String start, String end, List<String> list) {
        list.add(start);
        // 预处理，建立起每一个节点的下一个节点的hash表
        HashMap<String, ArrayList<String>> nexts = getNexts(list);

        // 计算从其实节点到所有节点的最近的距离，采用的是宽度优先遍历的方法
        HashMap<String, Integer> distances = getDistances(start, nexts);

        // 利用深度优先遍历，来计算所有到达目标节点的最短的路劲（可能不止一条）
        LinkedList<String> pathList = new LinkedList<>();
        List<List<String>> res = new ArrayList<>();
        getShortestPaths(start, end, nexts, distances, pathList, res);
        return res;
    }

    private static HashMap<String, ArrayList<String>> getNexts(List<String> words) {
        Set<String> dict = new HashSet<>(words);

        HashMap<String, ArrayList<String>> nexts = new HashMap<>();
        for (int i = 0; i < words.size(); i++) {
            nexts.put(words.get(i), getNext(words.get(i), dict));
        }
        return nexts;
    }

    private static ArrayList<String> getNext(String word, Set<String> dict) {
        ArrayList<String> res = new ArrayList<>();
        char[] chs = word.toCharArray();
        for (char cur = 'a'; cur <= 'z'; cur++) {
            for (int i = 0; i < chs.length; i++) {
                if (chs[i] != cur) {
                    char tmp = chs[i];
                    chs[i] = cur;
                    String ans = String.valueOf(chs);
                    if (dict.contains(ans)) {
                        res.add(ans);
                    }
                    chs[i] = tmp;
                }
            }
        }
        return res;
    }

    private static HashMap<String, Integer> getDistances(String start, HashMap<String, ArrayList<String>> nexts) {
        HashMap<String, Integer> distances = new HashMap<>();
        distances.put(start, 0);
        Queue<String> queue = new LinkedList<>();
        queue.add(start);
        HashSet<String> set = new HashSet<>();
        set.add(start);
        while (!queue.isEmpty()) {
            String cur = queue.poll();
            for (String next : nexts.get(cur)) {
                if (!set.contains(next)) {
                    distances.put(next, distances.get(cur) + 1);
                    queue.add(next);
                    set.add(next);
                }
            }
        }
        return distances;
    }

    // 深度优先遍历
    private static void getShortestPaths(String cur, String to,
                                         HashMap<String, ArrayList<String>> nexts,
                                         HashMap<String, Integer> distances,
                                         LinkedList<String> path,
                                         List<List<String>> res) {
        path.add(cur);
        if (to.equals(cur)) {
            res.add(new LinkedList<>(path));
        } else {
            for (String next : nexts.get(cur)) {
                if (distances.get(next) == distances.get(cur) + 1) {
                    getShortestPaths(next, to, nexts, distances, path, res);
                }
            }
        }
        path.pollLast();
    }
```

### 深度优先遍历

### 纸条折叠

![img](..\images\paper-folding)

![img](..\images\paper-folding2)

![img](..\images\paper-folding3.png)



### 二维数组

![image-20210418220845637](..\images\image-20210418220845637.png)

#### 解法一：

使用两个set分别记录需要清0的行号和列号，然后对所有的数据进行遍历，如果遍历到的数据的行号或者列号记录在需要清0的集合中，则对这一个数据进行清0。

需要注意的是，这一个解法并不是空间复杂度为**O(1)**的方法。

#### 解法二：

考虑到我们需要用两个集合来记录需要变成0的行号和列号，不如考虑用第0行和第0列来作为上面的集合使用。例如，假设第2行第3列的数据是0，那么，我们将第0行的第3列的数据变成0，第2行第0列的数据变成0。

但是这样的话存在一个问题，这样的处理方式会改变第0行和第0列的数据，如果第2行第3列和第0行第3列的数据都是0的话，会无法区分第0行第3列的这个0是变化之后的数据还是变化之前的数据，同理，我们也无法对列的数据进行判断，所以我们需要对第0行的数据和第0列的数据进行单独的处理。

首先我们对第0行的数据进行遍历，如果中间存在0，那么可以知道的是，第0行的数据是需要全部变成0的，我们将这个标记记作row_zero。同理我们对第0列的数据也进行相同的处理，记作col_zero。然后我们就完全可以对剩下的行和列进行和解法一类似的处理了，因为我们已经对所需要用来做记录的行和列进行了处理。如果在i行j列的数据上是0，那么就将i行0列和0行j列的数据标记成0，表示这一行和这一列需要全部变成0。

首先对剩下的行和列进行标记处理，这需要一次全局的遍历。接着再次对剩下的行和列进行遍历，这一次遍历是对数据进行更改，当遍历到i行j列的时候，如果i行0列和0行j列中有一个数据是0的话，这一个数据即变成0，反之，如果i行0列和0行j列都不为0，则这一个数据不做改变。

这一个步骤之后，我们就将所有的0行和0列之外的所有的数据进行了改变，现在就需要对第0行和第0列的数据进行单独的更改。

前面我们对第0行和第0列的数据进行了标记，如果第0行的数据中存在0，那么标记行的标记位更改为true，反之就是false，同样，对第0列的数据也是这样的处理。所以我们此时检查对应的标记位，如果标记行的标记是true，那么就将第0行全部变成0，同样，如果标记列的标记是true，那么第0列的数据就全部变成0。

就这样，我们就完成了对应的全部的数据更改。

```java
public class Solution {
    public static void setZeros(int[][] matrix) {
        boolean row0Zero = false;
        boolean col0Zero = false;
        int i = 0;
        int j = 0;
        for (i = 0; i < matrix[0].length; i++) {
            if (matrix[0][i] == 0) {
                row0Zero = true;
                break;
            }
        }

        for (i = 0; i < matrix.length; i++) {
            if (matrix[i][0] == 0) {
                col0Zero = true;
                break;
            }
        }

        for (i = 0; i < matrix.length; i++) {
            for (j = 0; j < matrix[0].length; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }

        for (i = 0; i < matrix.length; i++) {
            for (j = 0; j < matrix[0].length; j++) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }

        if (row0Zero) {
            for (i = 0; i < matrix[0].length; i++) {
                matrix[0][i] = 0;
            }
        }
        if (col0Zero) {
            for (i = 0; i < matrix.length; i++) {
                matrix[i][0] = 0;
            }
        }
    }
}
```

#### 解法三

假设我们不区分第0行和第0列，此时第0行第3列的数据是0，按照上述一样的做法，我们将第0行第0列和第0行第3列数据变成0，此时我们会发现，这样我们会无法区别究竟是第0行还是第0列的数据将0行0列的数据变成0的，又或者是原本这个位置的数据就是0。因为按照上面相同的做法，这样会将第0列的数据也全部清0，如果此时第0列的数据不包含0，那么就会出错。

因此，最根源的问题在于第0行0列的位置，牵扯到了两个特殊的行，正是我们用以标记数据的行。因此，只要我们可以区分0行和0列的数据即可，这也就是说我们可以只用一个bool类型的变量就可以进行区别。

和上面的情况类似，我们对第0列的数据进行遍历，如果第0列的数据包含0，那么这个标记位就是true，反之就是false。

然后我们就可以对全局的数据进行解法二一样的处理，需要注意的是，在第二次全局遍历更改数据的时候，我们需要把第0行的数据放在最后更新。因为如果我们不是在最后更新的话，遍历到第0行的时候，如果第0行包含0，那么0行0列的数据久一定是0，更新之后0行就全部变成了0，此时0行就失去了作为标记的作用。

```java
public class Solution {
    public static void setZeros(int[][] matrix) {
        boolean col0Zero = false;
        int i = 0;
        int j = 0;
        for (i = 0; i < matrix.length; i++) {
            for (j = 0; j < matrix[0].length; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    if (j == 0) {
                        col0Zero = true;
                    } else {
                        matrix[0][j] = 0;
                    }
                }
            }
        }

        // 注意需要将第0行的数据放在最后更改
        for (i = matrix.length - 1; i >= 0; i--) {
            for (j = 1; j < matrix[0].length; j++) {
                if (matrix[0][j] == 0 || matrix[0][i] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }
        if (col0Zero) {
            for (i = 0; i < matrix.length; i++) {
                matrix[i][0] = 0;
            }
        }
    }
}
```




















