## LeetCode刷题笔记

### 数组接水问题

[原题链接](https://leetcode-cn.com/problems/volume-of-histogram-lcci)

给定一个直方图(也称柱状图)，假设有人从上面源源不断地倒水，最后直方图能存多少水量?直方图的宽度为 1。

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)

上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的直方图，在这种情况下，可以接 6 个单位的水（蓝色部分表示水）。 感谢 Marcos 贡献此图。

示例:

```txt
输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6
```

#### 最基础的解法

在每一个位置都计算出左边最大的数字，和右边最大的数字，取这两个数字的较小者，为该位置可以积水的上限。在这个位置上，对其高度和上限进行判断，如果该位置的高度高于上限，则无法积水，反之则可以积水，积水的高度为上限和高度之间的差值。

这个方法是最基础的方法，但是其时间复杂度较高，因为每次我们都需要向前和向后寻找最大值和最小值。此方法的时间复杂度是**O(N^2)**。空间复杂度是**O(1)**， 因为使用的都是常数项级别的变量数目。

#### 改进一些的做法

由于在每一个位置都要向前和向后循环去寻找左边和右边的最大值，我们就可以对数据进行一些预处理。我们使用两个和原数组相同长度的数组，分别保存每一个位置上的左边的最大值和右边的最大值，这样我们再依次循环到原数组的位置时，就可以直接在左边最大值的数组和右边最大值的数组中直接取数据。

这样的方法避免了重复地去查找左右两侧的最大值，所以在时间复杂度上可以减小到**O(3N)**。因为只需要遍历有限次的数据，空间复杂度则是**O(2N)**，因为我们需要额外的两个和原数组等长的数据来保存左右两侧的最大值。

#### 更加优化的一个做法

在之前的方法中，我们在求解左侧的最大值的数组时，需要从前向后遍历数组，在求右侧的最大值数组时，需要从后向前便利。然后再从前向后或者后向前循环一次求解答案。那那么我们可以将其中的一个循环省掉吗，答案是可以。具体的代码如下。

```java
class Solution {
    public int trap(int[] height) {
        if (height.length < 3) return 0;
        int[] right = new int[height.length];
        right[height.length - 1] = 0;
        for (int i = height.length - 2; i >= 0; i--) {
            right[i] = Integer.max(height[i + 1], right[i + 1]);
        }
        int left = 0;
        int total = 0;
        for (int i = 0; i < height.length; i++) {
            int minimum = Integer.min(left, right[i]);
            total += Integer.max(0, minimum - height[i]);
            left = Integer.max(left, height[i]);
        }
        return total;
    }
}
```

在代码中，我们首先求解右侧的最大值的数组，然后再用一个变量记录下左侧的最大值，从前向后循环，即可求解出最大值。

这种方法省掉了一个最大值的数组，空间复杂度降低到了**O(N)**，时间复杂度降低到了**O(2N)**。

#### 进一步思考

我们能不能将最大值数据完全省略掉呢？答案是可以的。

具体的代码如下。

```java
class Solution {
    public int trap(int[] height) {
        if (height == null || height.length < 3){
            return  0;
        }
        int N = height.length;
        int left = 1;
        int right = N - 1;
        int leftMax = height[0];
        int rightMax = height[N - 1];

        int water = 0;
        while(left <= right){
            if (leftMax < rightMax){
                water += Integer.max(0, leftMax - height[left]);
                leftMax = Integer.max(leftMax, height[left]);
                left += 1;
            }
            else {
                water += Integer.max(0, rightMax - height[right]);
                rightMax = Integer.max(rightMax, height[right]);
                right -= 1;
            }
        }
        return water;
    }
}
```

具体的算法原理就是我们没有必要计算出最大值的数组。我们需要从两端开始遍历，从两端开始向中间位置靠拢，在遍历两端的数据时，优先选择数值小的一方进行数据更新。

通过上述的代码优化，我们在时间复杂度为**O(N)**空间复杂度为**O(1)**的情况下求得了所需要的答案。

### 随机数重新构造问题

#### 解法

规定一个随机函数f，此函数内部不可见，该函数等概率的返回1~5中的一个数字，现在要求利用该随机函数重新构造出一个等概率返回1~7中的一个数字的函数。

关于重构随机数函数的问题，可以将问题归类到二进制的问题中。

在上述问题的情况下，我们可以规定当随机函数f返回1和2的时候，我们认为该函数返回的是0，当随机函数f返回的是4和5的时候，我们认为该函数返回的是1，当随机函数f返回的是3的时候，我们重新利用该函数f构造。这样我们就获得了一个可以等概率返回1和0的二进制随机数生成器。接下来的步骤就很简单了，计算目标区间的数值最小需要多少位二进制标表示，然后利用该二进制生成器生成即可。

Java代码如下：

```java
import java.util.HashMap;
import java.util.Random;

public class Solution {
    public static int f(){
        return (int) (Math.random() * 5) + 1;
    }

    public static int randomBinary(){
        int number = f();
        if (number < 3){
            return 0;
        }
        if (number > 3){
            return 1;
        }
        return randomBinary();
    }

    public static int myRandom(){
        int ans = (randomBinary() << 2) + (randomBinary() << 1) + randomBinary();
        while (ans > 6){
            ans = (randomBinary() << 2) + (randomBinary() << 1) + randomBinary();
        }
        return ans + 1;
    }
    public static void main(String[] args) {
        HashMap<Integer, Integer> count = new HashMap<>();
        for (int i = 0;i<1000000;i++){
            Integer a = myRandom();
            if (count.containsKey(a)){
                count.put(a, count.get(a) + 1);
            }
            else {
                count.put(a, 1);
            }
        }
        System.out.println(count);
    }
}
```



代码运行结果如下：

```txt
{1=142177, 2=142039, 3=143257, 4=142426, 5=143033, 6=143528, 7=143540}
```

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

### 数组构造问题

输入一个int类型的整数N，构造一个长度为N的数组array，使得对于任意的**0<=i<j<k<N**，都满足如下的条件：$array[i] + array[j] != 2 * array[k]$。

#### 解法

这种构造数组的问题，可以思考的方向一般有两个，一个是先构造一个有序的数组，在按照一定的规则进行调整。另外一个是首先构造一个满足要求的长度较小的数组，在按照一定的规则去进行扩容，直到满足长度的要求。

这一题的思路就是上述提到的方法之二，先构造一个长度较小的数组，在按照一定的规则进行扩容，直到长度符合要求。

假设我们已经构造出了一个长度为3的最小的满足要求的数组，记为a[0], a[1], a[3]，满足要求a[0] + a[2] != 2 a[1]。很明显，只要我们对数组里面的数据进行相同的数值变化f，只要这个f函数是一个有效的非0的线性变换，那么对这个数组中的所有数进行这个函数变换f，那么变换之后，依然有，$f(a[0]) + f(a[2]) != 2 * f(a[0])$。所以，我们可以构造两个函数变换，第一个是$f(x) = 2x$，第二个是$f(x) = 2x + 1$。对上述的数组的数据分别应用这两个函数变幻，就可以得到两个数组a和b，那么我们将b的数据粘贴在a数组的后面，就可以将原来长度为3的数组扩容成长度为6的数组。

理论上，我们可以选择很多个不同的函数变换的对，只要可以证明变换之后是满足要求的，并且拼在一起之后依然满足要求即可。

### 按序的数值对的第k项

长度为N的数组arr，一定可以组成$N^2$个数值对，例如数组$arr=[3, 1, 2]$，可以组成9组数值对，如$(3, 3), (3, 1), (3, 2), (1, 3), (1, 1), (1, 2), (2, 3), (2, 1), (2, 2)$。也就是任意两个数都有数值对，而且自己和自己也可以组成数值对。

按照如下的规则堆数值对进行排序，按照第一维的数据从小到大进行排序，如果第一位数字相同，则按照第二维的数字进行排序，所以上面的数值对的排序结果是$(1, 1), (1, 2), (1, 3), (2, 1), (2, 2), (2, 3), (3, 1), (3, 2), (3, 3)$。

给定一个数组arr，和一个整数k（从0开始计数），返回第k小的键值对。‘

#### 基础解法

最最基础的办法是我们可以将这些数值对全部生成出来，保存在一个数组中，然后对这个数组进行排序，取第K个数值对即可。这样的算法的时间复杂度是$O(N^2 + N^2logN^2)$，第一个$N^2$表示的是生成数值对的时间，后一项是对数值对的排序的时间复杂度。空间复杂度是$O(N^2)$，表示的是保存数值对的数组的长度。

#### 最基础的思考

假设给定的数组中不含重复的数字，那么这一题就很简单，我们只需要对数组进行排序，然后对K进行整除和取模即可

#### 更通用的情况

假设给定的数组中包含重复的数字，我们可以知道的就是排序之后的数字的顺序就是我们数值对的第一维的数字的分布顺序。这一点和上述的基础的思考的解法是一致的。

难点就在于我们如何计算出数值对的第二维数值。

为什么我们不能用整除和取模的方法去计算第二维的数据？我们可以用数组$[1, 1, 2, 3]$来看。假设我们需要取第6个数值对，那么，排序之后，我们有

$(1, 1), (1, 1), (1, 1), (1, 1), (1, 2), (1, 2), (1, 3), (1, 3), ...$

6对4进行整除，得到数值对的第一维的数值的下标1， 对应的数值就是1，6对4进行取模，得到结果2，如果按照上面的方法，我们取数组的第三个数值2，也就是2，但是观察上面的数值对的序列结果可以发现，我们的答案并不是(1, 2)，而是（1， 3）。

其实画个表格就很容易看出规律来，在此不赘述。

假设我们的序列是$[1, 1, 2, 3, 3, 4, 5, 5, 5, 6]$，k取值83。

那么按照上面的求解第一维数值的方法，我们可以计算出第一维数值的下标是



```java
import java.util.*;

public class Solution {
    public static int[] kthMinPair(int[] arr, int k) {
        int length = arr.length;
        if (k >= length * length) {
            return null;
        }
        Arrays.sort(arr);

        int firstNum = arr[k / length];
        int lessFirstNumCount = 0;
        int equalFirstNumCount = 0;
        for (int i = 0; i < length && arr[i] <= firstNum; i++) {
            if (arr[i] < firstNum) {
                lessFirstNumCount += 1;
            } else {
                equalFirstNumCount += 1;
            }
        }
        int rest = k - (lessFirstNumCount * length);
        return new int[]{firstNum, arr[rest / equalFirstNumCount]};
    }

    public static int[] kthMinPairWithSort(int[] arr, int k) {
        ArrayList<int[]> arrays = new ArrayList<>();
        for (int i = 0; i < arr.length; i++) {
            for (int j = 0; j < arr.length; j++) {
                arrays.add(new int[]{arr[i], arr[j]});
            }
        }
        arrays.sort((o1, o2) -> {
            if (o1[0] > o2[0]) return 1;
            if (o1[0] < o2[0]) return -1;
            else return o1[1] - o2[1];
        });
        return arrays.get(k);
    }

    public static void main(String[] args) {
        int[] arr = new int[100];
        for (int i = 0; i < 100; i++) {
            arr[i] = (int) (Math.random() * 100);
        }
        int k = (int) (Math.random() * 10000);
        System.out.println(Arrays.toString(arr));
        System.out.println(k);
        System.out.println(Arrays.toString(kthMinPair(arr, k)));
        System.out.println(Arrays.toString(kthMinPairWithSort(arr, k)));
    }
}

```

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

### 字符串摘要

如果两个字符串（只包含小写字符a~z），所含字符种类完全一样，就算做一类，例如字符串abcd和abdcaa是一类，因为他们都含有相同的字符集abcd构成。

现在给定一个字符串数组，统计一共出现过多少了字符串的类。

#### 解法一：

这一题本身不难，我们只要对所有的字符串进行字符的统计，注意这里只需要统计那些字符出现过即可，并将这些出现过的字符按序拼成一个字符串，作为一个摘要，放入一个set中，对所有的字符串进行摘要处理之后，返回set的长度即可。

```java
class Solution{
    public static int types(String[] arr){
        HashSet<String> types= new HashSet<>();

        for (String s: arr){
            char[] chs = s.toCharArray();
            boolean[] map = new boolean[26];
            for (char c: chs){
                map[c - 'a'] = true;
            }
            StringBuilder key = new StringBuilder();
            for (int i = 0;i<map.length;i++){
                if (map[i]){
                    key.append(String.valueOf(i + 'a'));
                }
            }
            types.add(key.toString());
        }
        return types.size();
    }
}
```

#### 解法二：

考虑到小写字符只有26个，那么我们可以考虑用一个int类型的数据来保存字符的出现的情况，int类型的长度是32位，是足够用来保存状态的。

```java
class Solution{
    public static int types(String[] arr){
        HashSet<Integer> types = new HashSet<>();
        for (String s: arr){
            char[] chars= s.toCharArray();
            int key = 0;
            for (char c: chars){
                key = key | (1 << (c - 'a'));
            }
            types.add(key);
        }
        return types.size();
    }
}
```

#### 更多的思考

如果考虑到大小写字符的集合，那么我们就可以用一个long类型的数据来保存，甚至更多的Base64集合。

### 数组的子序列求和问题（动态规划）

给定一个非负的数组array，和一个正整数m。计算array的所有的子序列的累加和与m进行取模运算之后的最大值。

一个长度为n的数组的子序列有 $2^n$ 个。

#### 最最基础的解法

使用深度递归，求得每一个子序列的和，然后进行取模和比较，取最大的值即可。

代码如下max1函数。

```java
public static int ans = 0;

    public static int max1(int[] arr, int m){
        helper(arr, 0, 0, m);
        return ans;
    }

    public static void helper(int[] arr, int depth, int sum, int m){
        if (depth == arr.length){
            ans = Math.max(ans, sum % m);
            return;
        }
        helper(arr, depth + 1, (sum + arr[depth]) % m, m);
        helper(arr, depth + 1, (sum) % m, m);
    }
}
```



#### 如果数组中的每一个数字都不大，例如都小于20

假设我们的数组是[5, 1, 2]。

我们可以计算出数组中所有的元素的和，此处为8。

可以构造成一个3行9列的二位数组，此处的3行表示的是数组的长度，9列表示的是0~8之间的所有的子序列的和。

整个二位数组是一个Boolean类型的数组，那么我们可以构造出数组的每一个各自的含义，即我们有$dp[i][j]$的含义是利用数组的前i项能否构造出某一个子序列，该子序列的和是j。

明白了数组的每一个格子的含义之后，我们就应该明确状态转移方程。

联系到题目的具体含义，我们其实很容易想到状态转移的方程
$$
dp[i][j] = dp[i - 1][j] | dp[i - 1][j - arr[i]]
$$

$dp[i - 1][j]$ 表示的是我们当前在i的位置上，但是我们不想用arr[i]的数据，那么我们就直接将$dp[i - 1][j]$。剩下的就是我们想要使用arr[i]的数据，那么我们之前的i-1个数据就必须提供的和是j - arr[i]。那么两者只要有一种情况可以凑出j的数据，就证明利用前i个数据，我们可以凑出子序列的和j。

理解上述的状态转移的方程之后，建立好表格，我们就直接按序遍历一遍即可。

代码如下的max2函数。

```java
class Solution{
    public static int ans = 0;

    public static int max1(int[] arr, int m){
        helper(arr, 0, 0, m);
        return ans;
    }

    public static void helper(int[] arr, int depth, int sum, int m){
        if (depth == arr.length){
            ans = Math.max(ans, sum % m);
            return;
        }
        helper(arr, depth + 1, (sum + arr[depth]) % m, m);
        helper(arr, depth + 1, (sum) % m, m);
    }

    public static int max2(int[] arr, int m){
        int sum = 0;
        int N = arr.length;
        for (int i = 0;i<N;i++){
            sum += arr[i];
        }
        boolean[][] dp = new boolean[N][sum + 1];
        // 填上第一列的数据
        for (int i = 0; i<N;i++){
            dp[i][0] = true;
        }
        // 处理第一行的数据
        dp[0][arr[0]] = true;
        
        // 对剩下的所有的数据进行处理
        for (int i = 1;i<N;i++){
            for (int j = 1;j<=sum;j++){
                // 当我不使用当前的数据也想凑出目标的j
                dp[i][j] = dp[i - 1][j];
                // 当我使用当前的数据想要凑出目标的j时，我们至少需要保证有足够的数据空间
                if (j - arr[i] >= 0){
                    dp[i][j] |= dp[i - 1][j - arr[i]];
                }
            }
        }
        // 遍历最后一行的数据，求得最大的取模之后的结果并返回
        int ans = 0;
        for (int i = 0;i<=sum;i++){
            if (dp[N - 1][i]){
                ans = Math.max(ans, i % m);
            }
        }
        return ans;
    }


    public static void main(String[] args) {
        int nums[] = new int[]{7, 5, 6, 4, 2, 3, 5, 2, 5};
        int a = max1(nums, 107);
        int b = max2(nums, 107);
        System.out.println(a + " " + b);
    }
}
```

这一种解法适用于数字较小的情况，当我们给定的数组中的数值都很大的时候，很明显，我们上面的解法得到的累加和会变得很大，这样会导致很多无效的循环，因此，当我们给定的数据都很大的时候，上面的算法并不是一种高效的方法。

#### 如果给定的取模数M不是很大的时候

如果给定的数组里的数值都很大，但是给定的m不是很大的时候，例如107，那么我们可以考虑将上面的dp表格的列设置为$0 \to m-1$。表示的是所有的模m之后的情况。

那么上面的代码需要进行一些小的改动。如下面的max3函数。

```java
import java.util.HashSet;

public class Solution {
    public static int max3(int[] arr, int m){
        int N = arr.length;
        boolean[][] dp = new boolean[N][m];
        // 初始化第0列
        for (int i = 0; i< N;i++){
            dp[i][0] = true;
        }
        // 初始化第0行
        dp[0][arr[0] % m] = true;
        
        // 依次计算剩下的格子
        for (int i = 1;i<N;i++){
            for (int j = 1;j<m; j++){
                // 当我不使用当前的arr[i]的数据时
                dp[i][j] = dp[i - 1][j];
                
                // 当我使用当前的arr[i]的数据时
                if (j - arr[i] >= 0){
                    dp[i][j] |= dp[i - 1][j - arr[i]];
                } else {
                    dp[i][j] |= dp[i - 1][j - arr[i] + m];
                }
            }
        }
        // 返回最后一行的最后一个是True的下标
        int ans = 0;
        for (int i = 0;i<m;i++){
            if (dp[N - 1][i]){
                ans = i;
            }
        }
        return ans;
    }
}

```

#### 当数组长度不是很大，数组的累加和非常大，m也非常大的时候

假设数组的累加和接近于$10^9$，m也接近于$10^9$。数组的长度不超过**35**。

此时我们可以考虑分而治之的方法，我们取数组的前一半的数据进行求解，求解出所有的和m取模之后的数组，并保持有序，对后面的一半的数据进行求解，同样求解出一个有序的和m取模之后的数组。

最后我们对两个数组里面的值进行两两比较，当在第一个数组中找到一个值为a的值，那么我们可以在第二个数组中利用二分的方法，求得不超过m-1-a的最大值b，然后保存下a+b的值，依次遍历，我们就可以求解出最后的结果。

```java
class Solution{
    // 如果arr的累加和很大，m也很大
    // 但是arr的长度不是很大
    public static int max4(int[] arr, int m){
        if (arr.length == 1){
            return arr[0] % m;
        }
        int mid = (arr.length - 1) / 2;
        TreeSet<Integer> sortSet1 = new TreeSet<>();
        process4(arr, 0, 0, mid, m, sortSet1);
        TreeSet<Integer> sortSet2 = new TreeSet<>();
        process4(arr, mid + 1, 0, arr.length - 1, m, sortSet2);
        int ans = 0;
        for (Integer left : sortSet1){
            ans = Math.max(ans, left + sortSet2.floor(m - 1 - left));
        }
        return ans;
    }

    // 求解所有的子序列的和并放到有序集合中
    public static void process4(int[] arr, int index, int sum, int end, int m, TreeSet<Integer> treeSet){
        if (index == end + 1){
            treeSet.add(sum % m);
        } else {
            process4(arr, index + 1, sum, end, m, treeSet);
            process4(arr, index + 1, sum + arr[index], end, m, treeSet);
        }
    }
}
```

### 字符串循环交换

给定一个字符串str和一个长度leftSize，请把str左侧leftSize的部分和右边的部分进行整体的交换。要求额外的空间复杂度是**O(1)**。

例如str是abcdef，leftSize是4，那么交换之后的结果是efabcd。

#### 解法一：

**经典解法**：首先将左侧的leftSize的部分进行逆序操作，在对剩下的右侧的部分进行逆序操作，最后对整体进行逆序操作。

```java
class Solution{
    public static void rotate(char[] chars, int leftSize){
        reverse(chars, 0, leftSize - 1);
        reverse(chars, leftSize, chars.length - 1);
        reverse(chars, 0, chars.length - 1);
    }

    public static void reverse(char[] chars, int left, int right){
        while(left < right){
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;
            left += 1;
            right -= 1;
        }
    }
    
    public static void main(String[] args) {
        char[] chars = new char[]{'a', 'b', 'c', 'd', 'e', 'f', 'g'};
        rotate(chars, 4);
        System.out.println(chars);

    }
}
```

#### 解法二

```java
class Solution{
    public static void rotate1(char[] chars, int leftSize){
        if (leftSize >= chars.length){
            return;
        }
        int left = 0; // 记录左侧的需要交换的字符序列的左侧的起始位置
        int right = chars.length - 1; // 记录右侧的需要交换的字符序列的右侧的终止位置
        int lpart = leftSize; // 记录左侧的需要交换的字符序列的长度
        int rpart = chars.length - leftSize; // 记录右侧的需要交换的字符序列的长度
        int same = Math.min(lpart, rpart); // 记录需要交换的长度
        int diff = lpart - rpart; // 记录左右两侧的字符序列的长度的差值
        exchange(chars, left, right, same);
        while (diff != 0){
            if (diff > 0){ // 左侧更长
                left += same;
                lpart = diff;
            } else { // 左右等长或者右侧更长
                right -= same;
                rpart = -diff;
            }
            same = Math.min(lpart, rpart);
            diff = lpart - rpart;
            exchange(chars, left, right, same);
        }
    }

    public static void exchange(char[] str, int left, int right, int size){
        int i = right - size + 1;
        char temp;
        while (size != 0){
            temp = str[left];
            str[left] = str[i];
            str[i] = temp;
            size -= 1;
            left += 1;
            i += 1;
        }
    }
    
    public static void main(String[] args) {
        char[] chars = new char[]{'a', 'b', 'c', 'd', 'e', 'f', 'g'};
        rotate1(chars, 4);
        System.out.println(chars);

    }
}
```

这是一种改进之后的算法，主要是需要不断比较交换部分的长度，长度较短的一侧交换过去之后将一直固定，不再参与之后的调整，然后循环对剩下的部分进行交换，一直到需要交换的的两个字符段长度相同。

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

### 背包问题

背包容量为w，一共有n袋零食，第i袋的零食的体积为$v[i]$，其中$v[i] \gt 0$。在零食的综艺及不超过背包容量的情况下，一共有多少种零食放法。（总体积为0也算一种方法）

#### 解法一：深度优先

```java
class Solution{
    public static int snack(int[] arr, int w){
        return process(arr, 0, w);
    }
    public static int process(int[] arr, int index, int rest){
        if (rest < 0){
            return -1;
        }
        if (index == arr.length){
            return 1;
        }
        // rest >= 0
        // 有零食index
        // index号零食，要 还是 不要
        int next1 = process(arr, index + 1, rest); // 不要index号零食
        int next2 = process(arr, index + 1, rest - arr[index]); // 要index号零食
        return next1 + (next2 == -1? 0: next2);
    }
}
```

#### 解法二：动态规划

```java
class Solution{
    public static int snack1(int[] arr, int w) {
        int N = arr.length;
        int[][] dp = new int[N][w + 1];
        for (int i = 0; i < N; i++) {
            dp[i][0] = 1;
        }

        if (arr[0] <= w) {
            dp[0][arr[0]] = 1;
        }

        for (int i = 1; i < N; i++) {
            for (int j = 1; j <= w; j++) {
                dp[i][j] = dp[i - 1][j];
                if (j - arr[i] >= 0) {
                    dp[i][j] += dp[i - 1][j - arr[i]];
                }
            }
        }

        int ans = 0;
        for (int i = 0; i <= w; i++) {
            ans += dp[N - 1][i];
        }
        return ans;
    }
}
```

#### 动态规划技巧之空间压缩

### 最长公共子序列

**注意，公共子序列和公共子串是不一样的问题，公共子序列不要求连续，公共字串必须要求连续。**



### 最长公共子串

```java
class Solution{
    // 动态规划的方法
    public static String longest1(String s1, String s2) {
        char[] str1 = s1.toCharArray();
        char[] str2 = s2.toCharArray();
        int[][] dp = new int[str1.length][str2.length];
        
        int endPosition = 0;
        int maxLength = 0;
        
        // 初始化第0列
        for (int i = 0; i < str1.length; i++) {
            dp[i][0] = str1[i] == str2[0] ? 1 : 0;
        }
        
        // 初始化第0行
        for (int i = 0; i < str2.length; i++) {
            dp[0][i] = str1[0] == str2[i] ? 1 : 0;
        }
        
        // 依次循环
        for (int i = 1; i < str1.length; i++) {
            for (int j = 1; j < str2.length; j++) {
                if (str1[i] == str2[j]){
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = 0;
                }
                // 记录下最长的子串的长度，并更新结束的位置
                if (dp[i][j] > maxLength){
                    maxLength = dp[i][j];
                    endPosition = i;
                }
            }
        }
        return s1.substring(endPosition - maxLength + 1, endPosition + 1);
    }

    public static String longest2(String s1, String s2) {
        char[] str1 = s1.toCharArray();
        char[] str2 = s2.toCharArray();
        int maxLength = 0; // 记录可以达到的最长的公共字串的长度
        int col = str2.length - 1; // 出发点的列号
        int row = 0; // 出发点的行号
        int endPosition = -1; // 记录达到最长的公共子串的时候的末尾的位置

        while (row < str1.length) {
            int i = row;
            int j = col;
            int len = 0;
            // 循环向右下方移动，每次移动一格
            while (i < str1.length && j < str2.length) {
                if (str1[i] == str2[j]) {
                    len += 1;
                } else {
                    len = 0;
                }
                if (len > maxLength) {
                    maxLength = len;
                    endPosition = i;
                }
                // 向右下方移动
                i += 1;
                j += 1;
            }
            // 处理出发点的位置
            if (col > 0) {
                col -= 1;
            } else {
                row += 1;
            }
        }
        return s1.substring(endPosition - maxLength + 1, endPosition + 1);
    }

    public static void main(String[] args) {
        String s1 = "ABC1234567DEFG";
        String s2 = "HIJKL1234567MNOP";
        System.out.println(longest1(s1, s2));
        System.out.println(longest2(s1, s2));

    }
}
```

#### 解法一：动态规划

如上面的代码的longest1函数，我们建立一个动态规划的数组，数组的更新规则是，如果当前遍历的两个字符是相同的，那么就在左上角的格子上的数字加一，如果不是相同的，就直接置为0，这是因为公共子串不可能以不相同的字符结尾。

这样一行一行地遍历下来，我们只要记录下出现过的最大值及其位置，等到循环截至之后去取对应的子串即可。

#### 解法二：对动态规划的空间进行优化

本质上，解法二依然是利用动态规划的思想去解决问题，但是在解决问题的基础上极大地优化了该问题的空间复杂度。

我们可看到，实际上和其他的动态规划题目类似，我们只需要用两个固定长度的数组即可完成整张表格的构建，数组一保存上一行的所有数据，数组二则根据上一行的数据和截止到目前为止的遍历的位置进行更新即可，同样记录下最大值和取得最大值的位置。

但是，我们可以更加优化空间复杂度，我们从整张表格的右上角开始，斜向右下依次遍历。开始遍历的位置开始时是在第0行，从右开始依次向左作为起点，如果达到了第0列，则开始对第0列开始从上往下依次选择遍历的起点。这样我们就可以如同上面的代码longest2函数那样，用有限的几个变量解决问题。

算法的过程如下图

![](..\images\lcst-longest2.PNG)

上方的数字是循环的次数，也可以理解为每一个循环的起始位置。



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

### 最长递增子序列

#### 解法一：纯粹动态规划

我们可以建立一个一维dp数组，$dp[i]$的含义是恰好以下标为i的数值结尾的递增子序列的最长的长度，依次对数据进行处理没然后返回dp数组中的最大的数值即可。

```java
public class Solution {

    public static int lengthOfLIS(int[] nums) {
        if (nums.length < 2) {
            return nums.length;
        }
        // 建立一个和原数组等长的dp数组
        int[] dp = new int[nums.length];
        // 记录下能达到的最大的递增子序列的长度，也就是我们最后需要的答案
        int ans = 1;
        // 初始化
        dp[0] = 1;
        
        // 依次对所有的数字进行遍历
        for (int i = 1; i < nums.length; i++) {
            int maxLength = 0;
            // 对目前数值之前的所有的数据进行遍历，找到小于nums[i]并且有最长的子序列的长度
            for (int j = i - 1; j >= 0; j--) {
                if (nums[j] < nums[i]) {
                    maxLength = Math.max(maxLength, dp[j]);
                }
            }
            dp[i] = maxLength + 1;
            if (dp[i] > ans) {
                ans = dp[i];
            }
        }
        return ans;
    }
}
```

#### 解法二：优化时间复杂度

上面的解法的一个比较大的问题，在于当我们处于一个位置i的时候，总是需要一直向前循环找到合适的数据，这一层循环会将时间复杂度变成$O(N^2)$。实际上，是由办法将时间复杂度控制在$O(NlogN)$的。

做法具体如下：

我们同样需要一个记录下以当前的数值nums[i]结尾的数组dp，同时我们也需要一个用以保存长度和该长度下可以拥有的最小的结尾数据的数据结构。

我们采用一个ends数组表示这个数据结构，$ends[i]$表示的是找到的所有长度为$i+1$的递增子序列中的最小的结尾的数值。听起来有些抽象，

当$ends[2] = 4$时，i此时的数值时2，那么表示当前的所有的长度是i+1=3的递增子序列中，这些子序列中最小的结尾的数最小的就是4。

可以看出，ends数组一定是升序存储的，最长的长度就是原数组的长度。

当我们在原数组中遍历到一个新的节点cur的位置是，我们首先在ends数组中搜索最小的大于或等于当前的数值$nums[cur]$的最小的位置，假设没有找到则扩增ends数组，假设找到了，记作此时找到的下标为p，那么我们就更新此时的$ends[p]$为$nums[cur]$。这是因为我们在 原始驻足中找到了一个新的长度为$p+1$的结尾的更小的数字，所以我们需要更新。

然后再来更新dp数组，我们需要找我们当前找到了的ends数组中对应的位置，我们就需要更新dp数组了。此时只要统计找到的在ends数组中的位置$p$之前有多少个数字即可（包含第p个数字）。这是因为我们能在ends数组中统计的是长度逐渐递增的结尾的数据，那么我们就一定可以确定的，当前的位置p一定包含于前面的数字，既然我已经更新了对应的ends数组，那么一定可以确定我们p之前的数字的个数就一定是dp数组的更新的值。

又因为ends数组是有序的，那么我们就直接可以用二分的方法去搜索对应的位置，依靠这一步，我们成功将蒜贩的时间复杂度下调到了$O(NlogN)$。



```java
public class Solution {
    public static int lengthOfLIS2(int[] nums) {
        if (nums.length < 2) {
            return nums.length;
        }
        // 建立两个和原始数组等长的数组，一个用作dp，一个用作ends
        int[] dp = new int[nums.length];
        int[] ends = new int[nums.length];
        
        // 初始化
        ends[0] = nums[0];
        dp[0] = 1;
        
        // 这一个变量记录的是ends数组最后一个有效数值的位置，endsLength + 1表示的是ends数组的有效部分的长度
        int endsLength = 0;
        int l = 0;
        int r = 0;
        int m = 0;
        
        // 依次循环
        for (int i = 0; i < nums.length; i++) {
            
            // 利用二分查找来搜索对应的位置
            l = 0;
            r = endsLength;
            while (l <= r) {
                m = (l + r) / 2;
                if (nums[i] > ends[m]) {
                    l = m + 1;
                } else {
                    r = m - 1;
                }
            }
            // 因为有可能没找到小于等于的数值，那么此时需要扩容，l指向的就是下一个需要扩容的位置。
            endsLength = Math.max(endsLength, l);
            ends[l] = nums[i];
            dp[i] = l + 1;
        }
        return endsLength + 1;
    }
}
```

#### 例题：信封包装问题

现在有一堆信封，每一个信封都有一个长度和宽度的信息，如果A信奉的长度和宽度都小于B信封，那么A信封就可以放入B信封内部，形成一个嵌套的信封。

计算给定的信封数据中，可以达到的最大的嵌套层数。

##### 解法：

这一题本质上也是一个递增子序列的问题，我们首先先将信封按照信封的长度进行由小到大排序，如果两个信封的长度相同，则按照信封的宽度有大到小进行排序。

此时信封的长度信息就可以忽略不计了，因为后一个信封在长度上一定可以包含前面的所有的信封，此时我们将所有的信封的宽度信息排列成一个数组，对这个数组求解最长的递增子序列即是最大的可以达到的嵌套层数。

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



### 编辑距离

![](..\images\edit-distance-problem.PNG)

#### 简化版本

各项操作的代价都是1

```java
public class Solution {
    public int minDistance(String word1, String word2) {
        char[] str1 = word1.toCharArray();
        char[] str2 = word2.toCharArray();

        int N = str1.length + 1;
        int M = str2.length + 1;
        int[][] dp = new int[N][M];
        for (int i = 0; i < N; i++) {
            dp[i][0] = i;
        }
        for (int j = 0; j < M; j++) {
            dp[0][j] = j;
        }
        for (int i = 1; i < N; i++) {
            for (int j = 1; j < M; j++) {
                if (str1[i - 1] == str2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }
                dp[i][j] = Math.min(dp[i][j], dp[i - 1][j] + 1);
                dp[i][j] = Math.min(dp[i][j], dp[i][j - 1] + 1);
            }
        }
        return dp[N - 1][M - 1];
    }
}

```

#### 完整的版本





















