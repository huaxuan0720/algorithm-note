## 树

### 为什么需要树

### 树的类型

### 应用最多的树：二叉树

#### 二叉树的遍历：前序，中序，后序，层次



### 二叉树的典型：搜索树

### 二叉树的规范：平衡树

### 平衡树的条件放松的版本：红黑树

#### 为什么需要红黑树？

在平衡树中，我们经常需要对树的结构进行调整，以满足平衡树的任何一个节点的左右子树的高度差不超过1的情况，这样在一些很特殊的情况下会频繁地对树的结构进行调整，这会导致很多时间消耗在调整树的过程中。

因此，可以考虑对树的高度上的要求进行一点放松，使其不那么严格，就是对树的节点进行颜色标记，使其变成红黑树

### B树和B+树



## 树的典型题目

### 搜索二叉树后序遍历重构

给定一个搜索二叉树的后序遍历的数组序列。根据该序列重构该搜索二叉树。

#### 解法

这个问题解法很简单，利用搜索二叉树的后序遍历的顺序的特点即可构造一个递归算法。

该序列的最后一个节点一定是整棵树的根节点，前面的数据则可以分为两个部分，根节点的左子树包含的节点和根节点的右子树包含的节点，然后找到左子树和右子树的分割的位置，该位置的左侧都是左子树的后序遍历的顺序，右侧都是右子树的后序遍历的序列。利用递归就很容易实现了。

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```



```java
import java.util.HashMap;
import java.util.Random;

public class Solution {
    public static TreeNode process(int[] array, int L, int R) {
        if (L > R) {
            return null;
        }
        if (L == R) {
            return new TreeNode(array[L]);
        }
        int mid = L - 1;
        for (int i = L; i < R; i++) {
            if (array[i] < array[R]) {
                mid = i;
            }
        }
        TreeNode node = new TreeNode(array[L]);
        node.left = process(array, L, mid);
        node.right = process(array, mid + 1, R - 1);
        return node;
    }
}

```

该算法还有提升的空间，那就是我们在搜索分割点的位置的时候采用的是从后向前遍历的方法，此方法的效率不高，因此可以换成效率更高的二分搜索。

```java
import java.util.HashMap;
import java.util.Random;

public class Solution {
    public static TreeNode process(int[] array, int L, int R) {
        if (L > R) {
            return null;
        }
        if (L == R) {
            return new TreeNode(array[L]);
        }
        int mid = L - 1;
        int left = L, right = R - 1;

        while(left <= right){
            int middle = left + ((right - left) >> 1);
            if (array[middle] < array[R]){
                mid = middle;
                left = middle + 1;
            } else {
                right = middle - 1;
            }
        }

        TreeNode node = new TreeNode(array[L]);
        node.left = process(array, L, mid);
        node.right = process(array, mid + 1, R - 1);
        return node;
    }
}

```

### 二叉树的前序中序后序遍历

给定一棵二叉树，其中不包含重复的节点，并且给定了这棵树的中序遍历的结果数组和先序遍历的结果数组，返回对应的后序遍历的结果数组。

#### 解法一：

直接使用两个数组建立对应的二叉树，然后进行后序遍历即可。这招那个方法的成本比较高，因为涉及到二叉树的建立，在内存上的开销会比较大。

代码略

#### 解法二：

直接利用前序遍历和中序遍历的特点去分割数组，然后在原地调整数组。

```java
class Solution{
    public static int[] preIn2Pos(int[] pre, int[] in){
        if (pre == null || in==null||pre.length!=in.length){
            return null;
        }
        int N = pre.length;
        int[] pos = new int[N];
        process1(pre, 0, N - 1, in, 0, N - 1, pos, 0, N - 1);
        return pos;
    }

    // 递归解决问题，每一个数组都给定一个起始位置和结束位置（含）
    private static void process1(int[] pre, int l1, int r1,
                                 int[] in, int l2, int r2,
                                 int[] pos, int l3, int r3){
        if (l1 > r1){
            return;
        }
        if (l1 == r1){
            pos[l3] = pre[l1];
            return;
        }
        pos[r3] = pre[l1];
        int mid = l2;
        for(; mid <= r2; mid ++){
            if (in[mid] == pre[l1]){
                break;
            }
        }
        int leftSize = mid - l2;
        process1(pre, l1 + 1, l1 + leftSize, in, l2, mid - 1, pos, l3, l3 + leftSize - 1);
        process1(pre, l1 + leftSize + 1, r1, in, mid + 1, r2, pos, l3 + leftSize, r3 - 1);
    }
    public static void main(String[] args) {
        int[] pos = preIn2Pos(new int[]{1, 2, 4, 5, 3, 6, 7}, new int[]{4, 2, 5, 1, 6, 3, 7});
        for (int i: pos){
            System.out.println(i);
        }
    }
}
```

#### 解法三：

上面的解法利用了中序遍历和前序遍历的特点，实际上还有一定的改进空间。在对中序遍历的结果数组进行划分的时候，我们采用的是从前向后遍历的方法，实际上我们可以建立一张表格，记录下每一个数字的位置，在对中序遍历的结果数组进行划分的时候，直接在表格中取值即可。

```java
class Solution{
    public static int[] preIn2Pos2(int[] pre, int[] in) {
        if (pre == null || in == null || pre.length != in.length) {
            return null;
        }
        int N = pre.length;

        HashMap<Integer, Integer> inMap = new HashMap<>();
        for (int i = 0; i < N; i++) {
            inMap.put(in[i], i);
        }

        int[] pos = new int[N];
        process2(pre, 0, N - 1, in, 0, N - 1, pos, 0, N - 1, inMap);
        return pos;
    }

    private static void process2(int[] pre, int l1, int r1,
                                 int[] in, int l2, int r2,
                                 int[] pos, int l3, int r3,
                                 HashMap<Integer, Integer> inMap) {
        if (l1 > r1) {
            return;
        }
        if (l1 == r1) {
            pos[l3] = pre[l1];
            return;
        }
        pos[r3] = pre[l1];
        int mid = inMap.get(pre[l1]);
        int leftSize = mid - l2;
        process2(pre, l1 + 1, l1 + leftSize, in, l2, mid - 1, pos, l3, l3 + leftSize - 1, inMap);
        process2(pre, l1 + leftSize + 1, r1, in, mid + 1, r2, pos, l3 + leftSize, r3 - 1, inMap);
    }
}
```





