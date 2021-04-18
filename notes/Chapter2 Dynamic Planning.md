## 详解动态规划

动态规划是编程思维中比较难以理解的部分

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

![](C:/Users/Bryan/Desktop/Algorithm Notes/images/lcst-longest2.PNG)

上方的数字是循环的次数，也可以理解为每一个循环的起始位置。

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

### 编辑距离

![](C:/Users/Bryan/Desktop/Algorithm Notes/images/edit-distance-problem.PNG)



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

```java
class Solution{
     /**
     * 完整的编辑距离的求解
     *
     * @param s1：
     * @param s2：
     * @param dc：delete  cost, 删除一个字符的代价
     * @param ic：insert  cost，插入一个字符的代价
     * @param rc：replace cost，替换一个字符的代价
     * @return
     */
    public static int fullMinDistance(String s1, String s2, int dc, int ic, int rc) {
        char[] str1 = s1.toCharArray();
        char[] str2 = s2.toCharArray();
        int N = str1.length + 1;
        int M = str2.length + 1;
        int[][] dp = new int[N][M];
        for (int i = 0; i < N; i++) {
            dp[i][0] = i * dc;
        }
        for (int i = 0; i < M; i++) {
            dp[0][i] = ic * i;
        }
        for (int i = 1; i < N; i++) {
            for (int j = 1; j < M; j++) {
                if (str1[i] == str2[j]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = dp[i - 1][j - 1] + rc;
                }
                dp[i][j] = Math.min(dp[i][j], dp[i - 1][j] + dc);
                dp[i][j] = Math.min(dp[i][j], dp[i][j - 1] + ic);
            }
        }
        return dp[N - 1][M - 1];
    }

}
```

#### 相关题目

给定两个字符串s1和s2，问s2最少删除多少字符可以成为s1的字串。

如s1="abcde"，s2=“axbc"，因为删除x字符之后s2就是s1的字串了，所以返回1。

##### 解法1：如果s2的长度很短

求解出s2的所有的子序列，然后对每一个子序列判断是否是s1的字串，挑选出最长的一个序列，用原始的s2的长度减去这个最长的子序列的长度就是最后的答案。

好一点的方法是按照s2的子序列的长度从大到小的顺序求解子序列，然后去匹配，如果找到了一个合适的就可以立刻返回。

```java
public class Solution {
    public static int minCost(String s1, String s2) {
        ArrayList<String> s2Subs = new ArrayList<>();
        process(s2.toCharArray(), 0, "", s2Subs);
        s2Subs.sort(new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o2.length() - o1.length();
            }
        });
        for (String str : s2Subs) {
            if (s1.indexOf(str) != -1) {
                return s2.length() - str.length();
            }
        }
        return s2.length();
    }

    private static void process(char[] str, int index, String s, ArrayList<String> list) {
        if (index == str.length) {
            list.add(s);
            return;
        }
        process(str, index + 1, s, list);
        process(str, index + 1, s + str[index], list);
    }
}
```

##### 解法二：如果s2很长，s1的长度比较小

此时，我们可以先求解出s1的所有的字串，然后考察这些字串和s2的编辑距离，（假设此时只有插入行为，而没有删除和替换行为）

```java
public class Solution {
    public static int minCost(String s1, String s2) {
        int ans = Integer.MAX_VALUE;
        char[] str2 = s2.toCharArray();
        for (int start = 0; start < s1.length(); start++) {
            for (int end = start + 1; end <= s1.length(); end++) {
                ans = Math.min(ans, distance(str2, s1.substring(start, end).toCharArray()));
            }
        }
        return ans;
    }

    // 从目标的字符串到s1的各个字串之间的距离，此时只可以采用删除的方法。
    // 删除一个字符的代价定为1.
    // 将无法构成的数据定为Integer.MAX_VALUE，表示无效
    private static int distance(char[] str2, char[] s1sub) {
        int row = str2.length;
        int col = s1sub.length;
        int[][] dp = new int[row][col];


        dp[0][0] = str2[0] == s1sub[0] ? 0 : Integer.MAX_VALUE;
        for (int j = 1; j < col; j++) {
            dp[0][j] = Integer.MAX_VALUE;
        }
        for (int j = 1; j < row; j++) {
            dp[j][0] = (dp[j - 1][0] != Integer.MAX_VALUE || str2[j] == s1sub[0]) ? j : Integer.MAX_VALUE;
        }

        for (int i = 1; i < row; i++) {
            for (int j = 1; j < col; j++) {
                dp[i][j] = Integer.MAX_VALUE;
                if (dp[i - 1][j] != Integer.MAX_VALUE) {
                    dp[i][j] = dp[i - 1][j] + 1;
                }
                if (dp[i - 1][j - 1] != Integer.MAX_VALUE && str2[i] == s1sub[j]) {
                    dp[i][j] = Math.min(dp[i][j], dp[i - 1][j - 1]);
                }
            }
        }
        return dp[row - 1][col - 1];
    }
}
```

##### 优化：

如果我们观察仔细一点，可以发现，我们每次对一个字串都建立一张新的dp表格，实际上可以对这一步进行优化。

例如从0开始的所有字串，依次为基础构建的dp表格其实是逐渐增加的，这一步其实可以优化成如下，从0开始的剩下的字串，求解和目标串的编辑距离，然后对最后一行进行统计，计算出其中的最小值，然后对从1开始的剩下的字串，求解和目标串的编辑距离，统计最后一行的最小值，然后再依次循环下去。