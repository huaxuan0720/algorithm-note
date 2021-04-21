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

















