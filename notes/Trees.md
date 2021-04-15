## 树

### 为什么需要树

### 树的类型

### 应用最多的树：二叉树

### 二叉树的典型：搜索树

### 二叉树的规范：平衡树

### 平衡树的条件放松的版本：红黑树

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

### 



