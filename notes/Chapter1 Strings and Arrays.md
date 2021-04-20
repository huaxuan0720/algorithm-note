## 字符串

### KMP算法

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

## 数组

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

### 快慢指针

![image-20210414231506183](..\images\fast-slow-pointers.PNG)

![image-20210415214638285](..\images\slow-fast-pointers-answer1.png)

![image-20210415221752428](..\images\slow-fast-pointers-answer2.png)

![image-20210415222049235](..\images\slow-fast-pointers-answer3.png)

### 次数大于数组长度的一半的元素

时间复杂度**O(N)**，空间复杂度**O(1)**。

#### 解法：

我们对这个问题定如下的一个规则：**一次删掉数组中两个不同的数**。

那么我们对数组应用上面的规则之后，只会出现两个结果，

1、没有数字剩下来，那么可以确定的是，一定不存在一个元素的出现次数大于数组长度的一半。

2、只会留下来一种数字，那么我们就对该数字在原数组中进行统计，如果出现的次数确实大于数组长度的一半，那么就可以确定该数据存在。反之如果出现的次数不大于数组长度的一半，那么就不存在对应的数据。例如，如果数组是1， 2， 3， 4， 5，那么一次删除两个不同的数据之后，就会剩下5，很明显，这种情况不存在出现次数大于数组长度一半的数据，因此需要对数据进行额外的判断。

```java
public class Solution {
    public static int halfMajor(int[] arr) {
        int candidate = 0;
        int hp = 0; // 血量，如果血量为0，则表示之前的所有数据全部都被一对一对地删除了

        // 一次删掉两个不同的数据
        for (int value : arr) {
            if (hp == 0) { // 如果血量是0，则重新立起一个可能的候选人
                candidate = value;
                hp = 1;
            } else if (value == candidate) { // 如果候选不是0，并且和当前的候选人的数据相同，则增加血量
                hp += 1;
            } else { // 反之则减少血量，如果血量是0，候选人则被杀死，表示之前的所有数据全部都被一对一对地删除了
                hp -= 1;
            }
        }

        if (hp == 0) {
            return -1;
        }

        // 如果有数据剩下，则单独进行判断
        hp = 0;
        for (int value : arr) {
            if (value == candidate) {
                hp += 1;
            }
        }
        if (hp > arr.length / 2) {
            return candidate;
        } else {
            return -1;
        }
    }
}
```

#### 更多的思考

如果我们需要寻找出现次数大于**N/K**的元素（K是一个给定的参数），那么和上面的类似，我们最多会拥有K-1个数据符合要求，和上面的情况类似，我们需要维护一个长度为K-1的数据表格，记录元素和对应的血量，对全部的数据进行遍历，如果此时表格中没有足够的数据，且当前的数据不在表格中，那么我们就将当前的数据放入表格，并且将它的血量设置为1，如果当前的数据在表格中，就将表格中对应的血量加1，如果当前的数据不在表格中且表格已经满了，那么我们就将表格中所有的数据的血量减1，如果血量变成了0，就将该数据踢出表格，表示该数据已经被杀死。此时就相当于我们每次都删除掉K个不同的数据，所以表格满了且当前的数据不存在在表格中时，我们就把表格的数据全部拉上1个垫背。

这样最后的表格中最多会剩下K-1个数据，我们就依次对所有的数据和上面的解法一样，进行额外的遍历，如果当前的数据是出现的次数确实是大于N/K的，那么就把这个数据放在答案中，然后将所有的答案返回即可。

```java
public class Solution {
    public static List<Integer> kMajor(int[] arr, int k) {
        List<Integer> ans = new ArrayList<>();
        if (k < 2) {
            return ans;
        }

        // 候选表，数据的个数最多是k-1
        HashMap<Integer, Integer> candidates = new HashMap<>();
        for (int i = 0; i != arr.length; i++) {
            if (candidates.containsKey(arr[i])) {
                candidates.put(arr[i], candidates.get(arr[i]) + 1);
            } else {
                if (candidates.size() == k - 1) {
                    allCandidatesMinusOne(candidates);
                } else {
                    candidates.put(arr[i], 1);
                }
            }
        }

        HashMap<Integer, Integer> reals = getReals(arr, candidates);
        for (Map.Entry<Integer, Integer> set : candidates.entrySet()) {
            Integer key = set.getKey();
            if (reals.get(key) > arr.length / k) {
                ans.add(key);
            }
        }
        return ans;
    }

    // 对所有的数据进行减1的操作，拉上这些数据垫背
    private static void allCandidatesMinusOne(HashMap<Integer, Integer> hashMap) {
        List<Integer> removeList = new LinkedList<>();
        for (Map.Entry<Integer, Integer> set : hashMap.entrySet()) {
            Integer key = set.getKey();
            Integer value = set.getValue();
            if (value == 1) {
                removeList.add(key);
            }
            hashMap.put(key, value - 1);
        }
        for (Integer removeKey : removeList) {
            hashMap.remove(removeKey);
        }
    }


    // 重新进行统计
    private static HashMap<Integer, Integer> getReals(int[] arr, HashMap<Integer, Integer> candidates) {
        HashMap<Integer, Integer> reals = new HashMap<>();
        for (int cur : arr) {
            if (candidates.containsKey(cur)) {
                if (reals.containsKey(cur)) {
                    reals.put(cur, reals.get(cur) + 1);
                } else {
                    reals.put(cur, 1);
                }
            }
        }
        return reals;
    }
}
```



## 链表

### 回文链表

#### 解法一：使用栈

#### 解法二：使用数组长度一半的栈

```java
public class Solution {
    public static boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) {
            return true;
        }
        
        // 寻找链表的中间结点
        ListNode right = head.next;
        ListNode cur = head;
        while (cur.next != null && cur.next.next != null) {
            right = right.next;
            cur = cur.next.next;
        }
        
        // 使用stack倒置后半部分的节点
        Stack<ListNode> stack = new Stack<>();
        while (right != null) {
            stack.push(right);
            right = right.next;
        }
        while (!stack.isEmpty()) {
            if (head.val != stack.pop().val) {
                return false;
            }
            head = head.next;
        }
        return true;
    }
}
```



#### 解法三：掉转链表后半部分的顺序

```java
public class Solution {
    public static boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) {
            return true;
        }
        ListNode n1 = head;
        ListNode n2 = head;
        while (n2.next != null && n2.next.next != null) { // find the mid node
            n1 = n1.next;
            n2 = n2.next.next;
        }

        n2 = n1.next;
        n1.next = null;
        ListNode n3 = null;
        while (n2 != null) { // revert the right part
            n3 = n2.next;
            n2.next = n1;
            n1 = n2;
            n2 = n3;
        }

        n3 = n1;
        n2 = head;
        boolean res = true;
        while (n1 != null && n2 != null) { // check palindrome
            if (n1.val != n2.val) {
                res = false;
                break;
            }
            n1 = n1.next;
            n2 = n2.next;
        }
        // recover the list
        n1 = n3.next;
        n3.next = null;
        while (n1 != null) {
            n1.next = n3;
            n2 = n1;
            n1 = n2;
        }
        return res;
    }
}
```

### 链表的快速排序的partition

#### 解法：

### 带有随机指针的链表的深度复制

#### 解法一：使用map保存新的链表节点和老节点的关系

```java
public class Solution {
    static class RandomNode {
        int value;
        RandomNode next;
        RandomNode random;

        RandomNode(int value) {
            this.value = value;
            next = null;
            random = null;
        }
    }

    public static RandomNode copyListWithRandom(RandomNode head) {
        HashMap<RandomNode, RandomNode> map = new HashMap<>();
        RandomNode cur = head;
        while (cur != null) {
            map.put(cur, new RandomNode(cur.value));
            cur = cur.next;
        }
        cur = head;
        while (cur != null) {
            map.get(cur).next = map.get(cur.next);
            map.get(cur).random = map.get(cur.random);
            cur = cur.next;
        }
        return map.get(head);
    }
}
```



#### 解法二：将新节点插入到老节点的后面

![image-20210418162503719](..\images\random-linkedlist-deep-copy)

```java
public class Solution {
    static class RandomNode {
        int value;
        RandomNode next;
        RandomNode random;

        RandomNode(int value) {
            this.value = value;
            next = null;
            random = null;
        }
    }

    public static RandomNode copyListWithRandom(RandomNode head) {
        if (head == null) {
            return null;
        }
        RandomNode cur = head;
        RandomNode next = null;

        // copy node and link to every node
        while (cur != null) {
            next = cur.next;
            cur.next = new RandomNode(cur.value);
            cur.next.next = next;
            cur = next;
        }
        cur = head;
        RandomNode curCopy = null;
        while (cur != null) {
            next = cur.next.next;
            curCopy = cur.next;
            curCopy.random = cur.random != null ? cur.random.next : null;
            cur = next;
        }
        RandomNode res = head.next;
        cur = head;

        // split these two linked list
        while (cur != null) {
            next = cur.next.next;
            curCopy = cur.next;
            cur.next = next;
            curCopy.next = next != null ? next.next : null;
            cur = next;
        }
        return res;
    }
}
```

### 环状链表的相关问题

![img](..\images\clip_image002.jpg)

![img](..\images\clip_image004.jpg)

![img](..\images\clip_image006.jpg)

![img](..\images\clip_image008.jpg)

![img](..\images\clip_image010.jpg)

![img](..\images\clip_image012.jpg)

![img](..\images\clip_image014.jpg)

### 约瑟夫环















