## 算法基础

### 时间复杂度

### 空间复杂度

### 基本排序算法

#### 插入排序（稳定）

#### 冒泡排序（稳定）

#### 选择排序

#### 快速排序

#### 堆排序

#### 希尔排序

#### 归并排序（稳定）

#### 基数排序（稳定）

### 二分思想

#### 有序数组中搜索数据

```java
public class Solution {
    public static boolean exist(int[] sortedArr, int num) {
        if (sortedArr == null || sortedArr.length == 0) {
            return false;
        }

        int left = 0;
        int right = sortedArr.length - 1;
        int mid;
        while (left < right) {
            mid = left + (right - left) / 2;
            if (sortedArr[mid] == num) {
                return true;
            } else if (sortedArr[mid] > num) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return sortedArr[left] == num;
    }
}
```

#### 在一个有序数组中，找到大于或等于给定数据的最左侧的位置

```java
public class Solution {
    public static int nearestIndex(int[] arr, int value) {
        int left = 0;
        int right = arr.length - 1;
        int index = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] >= value) {
                index = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return index;
    }
}
```

#### 局部最小值

```java
public class Solution {
    public static int getLessIndex(int[] arr) {
        if (arr == null || arr.length == 0) {
            return -1;
        }
        if (arr.length == 1 || arr[0] < arr[1]) {
            return 0;
        }
        if (arr[arr.length - 1] < arr[arr.length - 2]) {
            return arr.length - 1;
        }
        int left = 1;
        int right = arr.length - 2;
        int mid = 0;
        while (left < right) {
            mid = left + (right - left) / 2;
            if (arr[mid] > arr[mid - 1]) {
                right = mid - 1;
            } else if (arr[mid] > arr[mid + 1]) {
                left = mid + 1;
            } else {
                return mid;
            }
        }
        return left;
    }
}
```

#### 猴子吃香蕉或者船运输货物问题

[吃香蕉问题](https://leetcode-cn.com/problems/koko-eating-bananas/?utm_source=LCUS&utm_medium=ip_redirect&utm_campaign=transfer2china)

### 异或运算(XOR)

#### 异或运算的性质

- 相当于不带进位的相加
- $0 \space  XOR \space N=N$
- $N \space XOR \space N = 0$
- 满足交换律

#### 不适用额外的变量交换两个数

```java
public class Solution {
    public static void main(String[] args) {
        int a = 1; // 记作a = 甲
        int b = 921; // 记作b = 乙
        a = a ^ b; // 此时，a = 甲^乙， b = 乙
        b = a ^ b; // 此时，a = 甲^乙， b = 甲^乙^乙 = 甲
        a = a ^ b; // 此时，a = 甲^乙^甲 = 乙，b = 甲
        System.out.println(a);
        System.out.println(b);
    }
}
```

```java
public class Solution {
    public static void swap(int[] arr, int i, int j) {
        if (i == j) { // 必须判断此时i和j是否相等，如果相等还进行异或运算的话，会将数据全部刷成0
            return;
        }
        arr[i] = arr[i] ^ arr[j];
        arr[j] = arr[i] ^ arr[j];
        arr[i] = arr[i] ^ arr[j];
    }
}
```

#### 数组中唯一重复奇数次的数

在一个数组中，有一种数出现了奇数次，其他的数字全部都出现了偶数次，找到这个数字。

这一题是典型的利用异或运算的性质的题目，由于异或运算满足交换律，因此异或和数据的顺序无关。我们将所有的数据进行异或（初始值是0），那么所有出现偶数次的数异或之后全部变成0，那么就一定会剩下那么出现了奇数次的数，而这个一定是我们需要的答案。

```java
class Solution {
    public static int function(int[] arr) {
        int ans = 0;
        for (int num : arr) {
            ans = ans ^ num;
        }
        return ans;
    }
}
```

#### 数组中唯二重复出现奇数次的数

由于数组中有且仅有两个数字（不妨记作a和b）重复出现了奇数次，那么我们如果对所有的数据进行异或的话，是没有办法求出对应的a和b，我们只能求解出$a \space XOR \space b$。但是这对我们已经有了一定的启发。

我们知道，对所有的数据进行异或之后，得到的数据肯定不会是0，因为a和b并不相同，所以得到的结果一定会在某一位上是1，不妨记作i位置(0<i<32)，a和b在这一位上一定不相同。那么我们就可以利用这个1将数据分成两类，一类是在i位置上是0的所有的数据的集合，另外一类是在i位置上是1的所有的数据的集合，那么a和b也一定不会在同一个集合中，所以我们分别对两个集合的全部数据进行异或运算即可求解出对应的a和b。

这里有一个小技巧，因为之前我们已经求解出了$a \space XOR \space b$，所以我们只需要对其中的一个数据集合进行全部数据的异或即可，不妨设求解出了a，那么，我们再和$a \space XOR \space b$进行异或之后，就可以求解出正确的b。

##### 求解出一个数的二进制表示中最右侧的1

我们考察其中的一个二进制表示，可以发现对任何一个数字，$N \& (\tilde N + 1)$，其中 $\tilde N$表示的是对N的所有二进制表示取反。

例如

```java
// N            =  0100111100110110000
// ~N           =  1011000011001001111
// ~N + 1       =  1011000011001010000

// N & (~N + 1) =  0100111100110110000 &
//                 1011000011001010000
//              =  0000000000000010000
```

##### 代码

利用前面说的求解出二进制最右侧的1，然后用这个位置的1进行分类，然后求解即可。

```java
class Solution {
    public static int[] function(int[] arr) {
        int eor = 0;
        for (int num : arr) {
            eor = eor ^ num;
        }

        int rightOne = eor & ((~eor) + 1); // 寻找最右侧的1
        int onlyOne = 0;
        for (int num : arr) {
            if ((num & rightOne) == 0) { // 也可以写成 != 0，本质上能区分即可
                onlyOne = onlyOne ^ num;
            }
        }
        return new int[]{onlyOne, onlyOne ^ eor};
    }
}
```

##### 思考

利用上面的求解最右侧1的性质，我们也可很快的求解出一个数的二进制中包含多少个1。

```java
class Solution {
    public static int bit1Count(int N) {
        int count = 0;
        while (N != 0) {
            int rightOne = N & ((~N) + 1);
            count += 1;
            N = N ^ rightOne;
        }
        return count;
    }
}
```

### 递归

#### 字符串的全部子序列

简单的递归问题，代码略

#### 字符串的全排列（含重）

```java
class Solution {
    static PrintStream out = System.out;

    public static ArrayList<String> permutation(String str) {
        ArrayList<String> res = new ArrayList<>();
        if (str == null || str.length() == 0) {
            return res;
        }
        char[] chs = str.toCharArray();
        process(chs, 0, res);
        return res;
    }

    private static void process(char[] str, int i, ArrayList<String> ans) {
        if (i == str.length) {
            ans.add(String.valueOf(str));
            return;
        }

        for (int j = i; j < str.length; j++) {
            swap(str, i, j);
            process(str, i + 1, ans);
            swap(str, i, j);
        }
    }

    private static void swap(char[] str, int i, int j) {
        char t = str[i];
        str[i] = str[j];
        str[j] = t;
    }
}
```



#### 字符串的全排列（不含重复）

```java
class Solution {
    static PrintStream out = System.out;

    public static ArrayList<String> permutation(String str) {
        ArrayList<String> res = new ArrayList<>();
        if (str == null || str.length() == 0) {
            return res;
        }
        char[] chs = str.toCharArray();
        process(chs, 0, res);
        return res;
    }

    private static void process(char[] str, int i, ArrayList<String> ans) {
        if (i == str.length) {
            ans.add(String.valueOf(str));
            return;
        }

        boolean[] visited = new boolean[26];
        for (int j = 0; j < 26; j++) {
            visited[j] = false;
        }
        for (int j = i; j < str.length; j++) {
            if (!visited[str[j] - 'a']) { // 分支限界，如果当前的字符已经交换过，就直接跳过
                visited[str[j] - 'a'] = true;
                swap(str, i, j);
                process(str, i + 1, ans);
                swap(str, i, j);
            }
        }
    }

    private static void swap(char[] str, int i, int j) {
        char t = str[i];
        str[i] = str[j];
        str[j] = t;
    }
}
```



#### 数字串转化成字符串

规定1和A对应，2和B对应，3和C对应，那么一个数字字符串比如"111"，可以转化成"AAA"，"KA"，"AK"。

给定一个只有数字字符组成的字符串str，返回有多少种转化的结果。

```java
class Solution {
    static PrintStream out = System.out;

    public static int ways(String s) {
        if (s == null || s.isEmpty()) {
            return 0;
        }
        return process(s.toCharArray(), 0);
    }

    private static int process(char[] str, int i) {
        if (i == str.length) {
            return 1;
        }

        // 因为数字不会以0开头，所以直接返回0
        if (str[i] == '0') {
            return 0;
        }

        // 10 ~ 19
        if (str[i] == '1') {
            int res = process(str, i + 1);
            if (i + 1 < str.length) {
                res += process(str, i + 2);
            }
            return res;
        }

        // 20 ~ 26, 注意，字符数字只会对应到26
        if (str[i] == '2') {
            int res = process(str, i + 1);
            if (i + 1 < str.length && '0' <= str[i] && str[i] <= '6') {
                res += process(str, i + 2);
            }
            return res;
        }

        // 因为最多对应到26，所以只会取第一个
        return process(str, i + 1);
    }
}
```

#### N皇后问题

**n 皇后问题研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。**

![在这里插入图片描述](..\images\N-queens.png)

##### 常规的递归解法

```java
class Solution {
    static PrintStream out = System.out;

    public static int queens(int n) {
        if (n < 2) {
            return 0;
        }
        int[] record = new int[n];
        return process(0, record, n);
    }

    // 处理第i行的皇后位置
    // 返回摆完所有的皇后之后所有的解法，可能为0
    private static int process(int i, int[] record, int n) {
        if (i == n) { // 如果达到了最后一个位置，返回1
            return 1;
        }
        // 如果没有达到最后一个位置
        int res = 0;
        for (int j = 0; j < n; j++) { // 对当前行的每一个列的位置进行尝试
            if (isValid(record, i, j)) { // 如果i行的j列可以放一个皇后
                record[i] = j; // 更新当前行的皇后的位置
                res += process(i + 1, record, n); // 对i + 1行进行求解
            }
        }
        return res;
    }

    // 对当前行row的col位置，判断和之前的（row行之前 0 ~ row - 1）所有的行的皇后是否有冲突
    private static boolean isValid(int[] record, int row, int col) {
        for (int k = 0; k < row; k++) {
            if (col == record[k] || Math.abs(record[k] - col) == Math.abs(row - k)) {
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args) {
        out.println(queens(15));
    }
}
```

##### 利用位运算进行求解

考察上面的问题，我们发现我们会有三个限制（除了行的限制之外），一个是列限制，一个是左斜对角线上的限制，一个是右斜对角线上的限制。那么我们就用3个整数去表示这三个限制，由于Int类型的数据只有32位，因此这个方法一般不能求解超过32的N皇后问题。

```java
class Solution {
    static PrintStream out = System.out;

    public static int queens(int n) {
        if (n < 2 || n > 32) {
            return 0;
        }
        // limit的最右侧是连续的n个1，在求解的过程中，limit始终保持不变
        int limit = n == 32 ? -1 : (1 << n) - 1;
        return process(limit, 0, 0, 0);
    }

    /**
     * limit参数确定了问题的皇后的数目
     * @param limit : 问题结束时的列的状态
     * @param colLimit : 列的限制，如果其中的某一个位置的bit是1，那么表示该位置不能放置皇后
     * @param leftDiaLimit : 左斜对角线上的限制，规则和列限制相同
     * @param rightDiaLimit : 右斜对角线上的限制，规则和列限制相同
     * @return : N 皇后的所有的解法
     */
    private static int process(int limit, int colLimit, int leftDiaLimit, int rightDiaLimit) {
        if (colLimit == limit) {
            return 1;
        }

        // 将所有的限制进行或操作，表示的所有的被占用的位置（被占用的位置是1，未被占用的位置是0），
        // 取反之后，所有的被占用的位置都变成了0，未被占用的位置变成了1
        // 和limit进行与操作表示的是将开头的32-n的bit位清零，消除前置无效数据的影响
        // 经过下面的代码之后，所有的可能的放置的位点都是1，不能放置的都是0
        int pos = limit & (~(colLimit | leftDiaLimit | rightDiaLimit));

        int mostRightOne = 0; // 记录最右侧的1的情况，如果最右侧的第三个位置是1，那么这个数字最后变成00^00100
        int res = 0;
        while (pos != 0) {
            mostRightOne = pos & (~pos + 1); // 计算最右侧的1的位置
            pos = pos ^ mostRightOne; // 异或运算表示消除了最右侧的那个1，让这个位置的数据变成0，然后就可以接着找最右侧的1
            res += process(limit,
                    colLimit | mostRightOne, // 列限制和最右侧的那个1进行或操作，将那个位置的列限制变成1，表示该位置已经被占用了。
                    (leftDiaLimit | mostRightOne) << 1, // 左斜对角线和最右侧的那个1或操作之后，**左移一位**。因为我们或完之后，其实还是在具体的那一个列，因此需要左移
                    (rightDiaLimit | mostRightOne) >>> 1); // 同上，只不过需要右移一位，而且是不带符号的右移一位
        }
        return res;
    }

    public static void main(String[] args) {
        out.println(queens(15));
    }
}
```























