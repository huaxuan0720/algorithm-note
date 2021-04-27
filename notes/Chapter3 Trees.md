## 树

### 为什么需要树

### 树的类型

### 应用最多的树：二叉树

#### 二叉树的遍历（递归）：前序，中序，后序，

#### 二叉树的遍历：层次

#### 二叉树的遍历（使用栈，非递归）

##### 前序使用栈

##### 中序使用栈

```java
class Solution {
    static PrintStream out = System.out;
    public static void inOrder(TreeNode head){
        out.println("in-order");
        if (head != null){
            Stack<TreeNode> stack = new Stack<>();
            while (!stack.isEmpty() || head != null){
                if (head != null){
                    stack.push(head);
                    head = head.left;
                } else {
                    head = stack.pop();
                    out.print(head.val + " ");
                    head = head.right;
                }
            }
        }
        out.println();
    }
}
```

##### 后序使用栈

```java
class Solution {
    static PrintStream out = System.out;

    public static void postOrder1(TreeNode head) {
        out.println("post-order");
        if (head != null) {
            Stack<TreeNode> s1 = new Stack<>();
            Stack<TreeNode> s2 = new Stack<>();
            s1.push(head);
            while (!s1.isEmpty()) {
                head = s1.pop();
                s2.push(head);
                if (head.left != null) {
                    s1.push(head.left);
                }
                if (head.right != null) {
                    s1.push(head.right);
                }
            }
            while (!s2.isEmpty()) {
                out.print(s2.pop() + " ");
            }
        }
        out.println();
    }

    public static void postOrder2(TreeNode head) {
        out.println("post-order");
        if (head != null) {
            Stack<TreeNode> stack = new Stack<>();
            stack.push(head);
            TreeNode c = null;
            TreeNode h = head; // h永远指向上一个输出的节点
            while (!stack.isEmpty()) {
                c = stack.peek();
                if (c.left != null && h != c.left && h != c.right) { // 如果h不是栈顶节点的右子节点，也不是左子节点，那么很明显，我们还没有遍历过栈顶元素的左子树，那么就将左子树的根节点入栈
                    stack.push(c.left);
                } else if (c.right != null && h != c.right) { // 如果上一个输出的节点不是当前节点的右子树，那么就将右子树的根节点进栈。
                    stack.push(c.right);
                } else { // 如果都不满足，说明左右子树都被遍历过了，弹出栈顶的元素并输出
                    out.print(stack.pop() + " ");
                    h = c;
                }
            }
        }
        out.println();
    }
}
```

###### 解法一

参考上面的第一种后序遍历的方式，我们可以发现，这种方法其实是参考的前序遍历的方法。在前序遍历中，我们首先将右子树的根节点入栈，再将左子树的根节点入栈，这样在出栈的时候就可以反过来。那么如果输出当前节点，然后先左子树的根节点入栈，再右子树的根节点入栈，我们就可以实现中，右，左的顺序依次输出，但是这个顺序正好和需要的顺序相反，因此需要一个额外的栈进行逆序操作。

###### 解法二

#### 二叉树中两个节点之间的最远距离

```java
class Solution {
    static PrintStream out = System.out;

    public static class Info {
        public int height;
        public int maxDistance;

        public Info(int height, int maxDistance) {
            this.height = height;
            this.maxDistance = maxDistance;
        }
    }

    public static int function(TreeNode root) {
        return process(root).maxDistance;
    }

    private static Info process(TreeNode node) {
        if (node == null) {
            return new Info(0, 0);
        }
        Info leftInfo = process(node.left);
        Info rightInfo = process(node.right);

        int height = Math.max(leftInfo.height, rightInfo.height) + 1;

        int maxDistance = Math.max(leftInfo.height + rightInfo.height + 1, Math.max(leftInfo.maxDistance, rightInfo.maxDistance));
        return new Info(height, maxDistance);
    }
}
```



### 二叉树的典型：搜索树

### 二叉树的规范：平衡树

#### 判断二叉树是否平衡

```java
class Solution {
    static PrintStream out = System.out;

    public static class Info {
        public int height;
        public boolean isBalanced;

        public Info(int height, boolean isBalanced) {
            this.height = height;
            this.isBalanced = isBalanced;
        }
    }

    private static boolean function(TreeNode root){
        return process(root).isBalanced;
    }
    
    private static Info process(TreeNode node) {
        if (node == null) {
            return new Info(0, true);
        }
        Info leftInfo = process(node.left);
        Info rightInfo = process(node.right);

        int height = Math.max(leftInfo.height, rightInfo.height) + 1;
        boolean isBalanced = true;

        if (!leftInfo.isBalanced || !rightInfo.isBalanced || Math.abs(leftInfo.height - rightInfo.height) > 1) {
            isBalanced = false;
        }
        return new Info(height, isBalanced);
    }
}
```





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

### 二叉树的中序遍历的后序节点

```java
class TreeNode{
    int value;
    TreeNode left;
    TreeNode right;
    TreeNode parent;
}
```

定义一个由上述节点构成的二叉树，给定一个节点数据，返回中序遍历中该节点的直接后驱节点。

#### 解法

一共存在两种情况：

- 如果当前节点包含一棵右子树，那么右子树的最左的节点就是当前节点的直接后驱节点
- 如果当前节点不包含右子树，那么我们需要向上遍历，如果当前的节点是其父亲的左子树，那么毫无疑问，我们就可以确定的是，其父亲节点就是当前节点的直接后驱节点。如果当前节点是其父亲的右子树，那么我们就必须继续向上找，将当前节点设置为其父亲节点，然后继续上找，直到当前节点是其父亲节点的左子树的根节点或者其父亲节点为空。

```java
class Solution {
    static PrintStream out = System.out;

    static class TreeNode {
        int value;
        TreeNode left;
        TreeNode right;
        TreeNode parent;

        TreeNode(int value) {
            this.value = value;
            left = null;
            right = null;
            parent = null;
        }
    }

    public static TreeNode getSuccessorNode(TreeNode node) {
        if (node == null) {
            return null;
        }
        if (node.right != null) {
            return getLeftMost(node.right);
        }

        TreeNode parent = node.parent;
        while (parent != null && parent.right == node) {
            node = parent;
            parent = node.parent;
        }
        return parent;
    }

    private static TreeNode getLeftMost(TreeNode node) {
        if (node == null) {
            return null;
        }
        while (node.left != null) {
            node = node.left;
        }
        return node;
    }
}
```





