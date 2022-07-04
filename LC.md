# LC

## Array

### #1两数之和

- 第一种解法（**暴力解法**）

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for(int i = 0;i<nums.length;i++){
            for(int j = i+1;j<nums.length;j++){
                if(nums[j]==target-nums[i]){
                    return new int[] {i,j};
                }
            }
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```

> 这里的拿出throws和throw的区别
>
> | throws                                           | throw                              |
> | ------------------------------------------------ | ---------------------------------- |
> | 用在方法声明后面，跟的是异常类名                 | 用在方法体内，跟的是异常对象名     |
> | 表示抛出异常，由该方法的调用者来处理             | 表示抛出异常，由方法体内的语句处理 |
> | 表示出现异常的一种可能性，并不一定会发生这些异常 | 执行throw一定抛出了某种异常        |
>
> ==因为这里有可能不存在2个数字的和等于target所以我们要在这种情况下抛出异常==
>
> 如果我们执行完了2个循环都还没有触发return那么就表面不存在2个数字的和等于target，所以我们在循环后直接抛出方法参数异常,表面所传的target参数并不适当
>
> - ```
>   public class IllegalArgumentException
>   extends RuntimeException
>   ```
>
>   ==抛出以指示方法已被传递非法或不适当的参数。== 

这种方法由于嵌套了一层循环，所以时间复杂度为0^2

- 第二种解法(利用hashmap来将时间复杂度O^2变为O，也就是没有嵌套循环)

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        //将nums数组以数值为key，索引为value加入到一个hashmap中s
        HashMap<Integer,Integer> hm = new HashMap();
        for(int i = 0;i<nums.length;i++){
            hm.put(nums[i],i);
        }

        //循环s找匹配
       	for(int i = 0;i<nums.length;i++){
        	int w = target - nums[i];
            if(hm.get(w) != null && hm.get(w) != i) return new int[] {hm.get(w),i};
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```

为了对运行时间复杂度进行优化，我们需要一种更有效的方法来检查数组中是否存在目标元素。如果存在，我们需要找出它的索引。保持数组中的每个元素与其索引相互对应的最好方法是什么？哈希表。

通过以空间换取速度的方式，我们可以将查找时间从 O(n) 降低到O(1)。哈希表正是为此目的而构建的，它支持以 近似 恒定的时间进行快速查找。我用“近似”来描述，是因为一旦出现冲突，查找用时可能会退化到 O(n)。但只要你仔细地挑选哈希函数，在哈希表中进行查找的用时应当被摊销为 O(1)。

一个简单的实现使用了两次迭代。在第一次迭代中，我们将每个元素的值和它的索引添加到表中。然后，在第二次迭代中，我们将检查每个元素所对应的目标元素（target - nums[i]）是否存在于表中。注意，该目标元素不能是 nums[i] 本身！

> 这里有几个细节：
>
> 1. if判断中&&与判断，会先判断放在&&前面的东西，比如上面的代码如果不先判断hm.get(w)!=null,的话，如果循环第一次就没发现存在这个数而去执行判断hm.get(w)!=i，就会触发空指针异常
> 2. 在这里目标袁术不能是nums[i]本身，意思就是不能出现2次，这里就直接判断就行了。

- 第三种解法（一遍哈希表）

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer,Integer> hm = new HashMap<>();
        for(int i = 0;i<nums.length;i++){
            int w = target-nums[i];
            if(hm.get(w)!=null){
                return new int[] {i,hm.get(w)};
            }
            hm.put(nums[i],i);
        }
        throw new IllegalArgumentException("No two sum solution");    
    }
}
```

事实证明，我们可以一次完成。在进行迭代并将元素插入到表中的同时，我们还会回过头来检查表中是否已经存在当前元素所对应的目标元素。如果它存在，那我们已经找到了对应解，并立即将其返回。

### #26 删除排序数组中的重复项

第一种解法（单个对比）

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int len = 1;
        for(int i = 0;i<nums.length;i++){
            if(i+1<nums.length&&nums[i]!=nums[i+1]){
                nums[len] = nums[i+1];
                len++; 
            }
        }
        return len;
    }
}
```

因为是排序好了的数组，所以只需要组个对比，然后将不一样的给放在前面就行了。

这种方法叫做双指针法：

> 数组完成排序后，我们可以放置两个指针 i和 j，其中 i 是慢指针，而 j 是快指针。只要 nums[i] = nums[j]，我们就增加 j 以跳过重复项。
>
> 当我们遇到 nums[j] 不等于 nums[i]时，跳过重复项的运行已经结束，因此我们必须把它（nums[j]）的值复制到 nums[i + 1]。然后递增 i，接着我们将再次重复相同的过程，直到 j 到达数组的末尾为止。



### #27 移除元素

没什么说的，ez

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int k = 0;
        for(int i = 0;i<nums.length;i++){
            if(nums[i]!=val){
                nums[k] = nums[i];
                k++;
            }
        }
        return k;
    }
}
```



### #35 搜索插入位置

没什么说的，ez

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        for(int i = 0;i<nums.length;i++){
            if(nums[i]>=target){
                return i;
            }
        }
        return nums.length;
   
    }
}
```



### #53 最大子序和*

> 给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
>
> 示例:
>
> 输入: [-2,1,-3,4,-1,2,1,-5,4]
> 输出: 6
> 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

方法一：贪心算法*（算法核心：若当前指针所指元素之前的和小于0，则丢弃当前元素之前的数列）

> 当前最大连续子序列和为 cur_sum，结果为 max_sum
> 如果 sum > 0，则说明 cur_sum对结果有增益效果，则 cur_sum保留并加上当前遍历数字
> 如果 sum <= 0，则说明 cur_sum对结果无增益效果，需要舍弃，则 cur_sum直接更新为当前遍历数字,(==只要总值小于0那么就舍弃，因为那一部分值无论存在于哪一个子序列中，都是会让总和变小的。所以需要将其重新更新为当前的数字==)
> 每次比较 cur_sum和 max_sum的大小，将最大值置为max_sum，遍历结束返回结果(==这里是为了判断是否加了当前索引会变小，如果变小了则最大的还是之前的值，但也不影响继续往后面加，也就是并没有改变cur_sum的值，因为有可能后面可以将这个索引的值的无增益效果变成有增益==)
>
> ```java
> int cur_sum = nums[0];
> int max_sum = cur_sum;
> for(int num:nums){
>     if(sum>0) cur_sum+=num;
>     else cur_sum = num;
>     max_sum = Math.max(max_sum,cur_Sum);
> }
> ```
>
> 下面是相同的思想，只是说更简化了

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int cur_sum = nums[0];
        int max_sum = cur_sum;
        for(int i = 1;i<nums.length;i++){
            cur_sum = Math.max(nums[i],nums[i]+cur_sum);
            max_sum = Math.max(cur_sum,max_sum);
        }
        return max_sum;
    }
}
```

> 贪心算法：顾名思义，**贪心算法总是作出在当前看来最好的选择**。也就是说贪心算法并不从整体最优考虑，它所作出的选择只是在某种意义上的局部最优选择。当然，希望贪心算法得到的最终结果也是整体最优的。虽然贪心算法不能对所有问题都得到整体最优解，但对许多问题它能产生整体最优解。

这里我们遍历整个数组，每次遍历都会加上现在索引所指向的值

也就是`cur_sum += nums[i]`，但这道题问的是连续最大的子序列，所以并不是需要全部加完，我们加到当前值比这个cur_sum还大的时候就将其代替(因为当前值都比之前加的大了那么我们就可以重这个索引开始加了，==也就是作出当前看来最好的选择==)

也就是`cur_sum = Math.max(num[i],cur_sum+nums[i])`，做完这一步之后还需要用一个变量来保存之前的最大值，然后做一个之前的最大值与加了现在索引的值之后的比较，

也就是`max_sum = Math.max(max_sum,cur_sum)`之前的是为了比这个索引和加了这个索引之后的大小，还得比加个这个索引以后和没加这个索引的数的大小（因为要是这个索引做的是减法的话，其实原来的值应该更大些）

---

方法二：动态规划（算法核心：**若前一个元素大于0，则加到当前元素上**）

```java
		int max = nums[0];
        for(int i = 1;i<nums.length;i++){
            if(nums[i-1]>0) nums[i]+=nums[i-1];
        }
        for(int sum:nums){
            if(max<sum) max = sum;
        }
        return max; 
```

和贪心算法的思想是一样的，只是换了个壳。

也是一样的如果前面一个数大于0那么是对后面的结果是有增益，所以就加到后面去。不过这样就会打乱原本的数组，并且这样是不知道最大和子序列的开始索引和结束索引在哪里了。但这个题需要返回的只是最大值并没有让返回开始和结束的索引。

### #66 加一

> 给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。
>
> 最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。
>
> 你可以假设除了整数 0 之外，这个整数不会以零开头。
>
> 示例 1:
>
> 输入: [1,2,3]
> 输出: [1,2,4]
> 解释: 输入数组表示数字 123。
> 示例 2:
>
> 输入: [4,3,2,1]
> 输出: [4,3,2,2]
> 解释: 输入数组表示数字 4321。

```java
class Solution {
    public int[] plusOne(int[] digits) {
        int m = 0;
        
        for(int i = digits.length-1;i>=0;i--){
            if(digits[i]<9){
                digits[i] +=1;
                return digits;
            }else if(digits[i]==9){
                digits[i] = 0;
                m++;
                if(m == digits.length){
                    break;
                }
            }
        }
        int[] new_digits = new int[m+1];//这里也可以选择覆盖原来的数组而不需重新新定义一个数组
        new_digits[0] = 1;
        //for(int i = 1;i<new_digits.length;i++){
         //   new_digits[i] = 0;
        //}需要注意的是这里不需要重新循环给数组赋值，因为数组初始化的时候的初始值就是0；
        return new_digits;
    }
}
```

ezez,需要注意一点的就是需要注意全都是9都是数组，这样的话返回的就得是一个比原来的数组的长度要长一个元素的数组。这样就需要新的一个数组空间。



### #88 合并两个有序数组

> 给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。
>
>  
>
> 说明:
>
> 初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。
> 你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。
>
>
> 示例:
>
> 输入:
> nums1 = [1,2,3,0,0,0], m = 3
> nums2 = [2,5,6],       n = 3
>
> 输出: [1,2,2,3,5,6]
>

方法一：创建一个新的数组将nums1和nums2的元素加入到其中，并使用Arrays.sort()对其进行排序，然后再讲数组的元素加入到nums1中

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        System.arraycopy(nums2, 0, nums1, m, n);
        Arrays.sort(nums1);
    }
}
```



方法二：利用双指针的方式*从前向后

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
    int [] nums1_copy = new int[m];
    System.arraycopy(nums1, 0, nums1_copy, 0, m);

    int p1 = 0;//nums1_copy的指针
    int p2 = 0;//nums2的指针
    int p = 0;//nums1的指针
    while ((p1 < m) && (p2 < n))
      nums1[p++] = (nums1_copy[p1] < nums2[p2]) ? nums1_copy[p1++] : nums2[p2++];//太帅了吧这个代码
      if(p1==m)//当nums1_copy中的元素已经加载完了，就只剩nums2的元素的时候，就需要将剩下的元素给复制到nums1中，下同。
          System.arraycopy(nums2,p2,nums1,p1+p2,m+n-p1-p2);
      if(p2==n)
          System.arraycopy(nums1_copy,p1,nums1,p1+p2,m+n-p1-p2);
    }
}
```



![img](G:/Typora%20image/LC/992f95361c37ad06deadb6f14a9970d0184fd47330365400dd1d6f7be239e0ff-image.png)

一般而言，对于有序数组可以通过 双指针法 达到O(n + m)O(n+m)的时间复杂度。

最直接的算法实现是将指针p1 置为 nums1的开头， p2为 nums2的开头，在每一步将最小值放入输出数组中。

由于 nums1 是用于输出的数组，需要将nums1中的前m个元素放在其他地方，也就需要 O(m)O(m) 的空间复杂度。

另外需要注意的是当对比完了，还需要将nums1_copy或者nums2中的其他数据复制到nums1中去，



方法三 利用双指针 从后向前

方法二已经取得了最优的时间复杂度O(n + m)O(n+m)，但需要使用额外空间。这是由于在从头改变nums1的值时，需要把nums1中的元素存放在其他位置。

如果我们从结尾开始改写 nums1 的值又会如何呢？这里没有信息，因此不需要额外空间。

这里的指针 p 用于追踪添加元素的位置。

![image-20200916195822486](G:/Typora%20image/LC/image-20200916195822486.png)

![image-20200916195829768](G:/Typora%20image/LC/image-20200916195829768.png)

```java
class Solution {
  public void merge(int[] nums1, int m, int[] nums2, int n) {
    // two get pointers for nums1 and nums2
    int p1 = m - 1;
    int p2 = n - 1;
    // set pointer for nums1
    int p = m + n - 1;

    // while there are still elements to compare
    while ((p1 >= 0) && (p2 >= 0))
      // compare two elements from nums1 and nums2 
      // and add the largest one in nums1 
      nums1[p--] = (nums1[p1] < nums2[p2]) ? nums2[p2--] : nums1[p1--];

    // add missing elements from nums2
    System.arraycopy(nums2, 0, nums1, 0, p2 + 1);
  }
}
```

### #120三角形最小路径和（中等）*

> 给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。
>
> 相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。
>
>  
>
> 例如，给定三角形：
>
> [
>      [2],
>     [3,4],
>    [6,5,7],
>   [4,1,8,3]
> ]
> 自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/triangle
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

分析：
若定义 f(i, j)f(i,j) 为 (i, j)(i,j) 点到底边的最小路径和，则易知递归求解式为:

f(i, j) = min(f(i + 1, j), f(i + 1, j + 1)) + triangle[i][j]f(i,j)=min(f(i+1,j),f(i+1,j+1))+triangle[i][j]

由此，我们将任一点到底边的最小路径和，转化成了与该点相邻两点到底边的最小路径和中的较小值，再加上该点本身的值。这样本题的 **递归解法**（解法一） 就完成了。

#### 方法一：递归

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        return dfs(triangle,0,0);  
    }
    private int dfs(List<List<Integer>> triangle, int i, int j) {
        if (i == triangle.size()) {
            return 0;
        }
        return Math.min(dfs(triangle, i + 1, j), dfs(triangle, i + 1, j + 1)) + triangle.get(i).get(j);
    }
}
```

超时，递归

#### 方法二：递归+记忆化

在解法一的基础上，定义了二维数组进行记忆化

```java
class Solution {
    Integer[][] memo;
    public int minimumTotal(List<List<Integer>> triangle) {
        memo = new Integer[triangle.size()][triangle.size()];
        return  dfs(triangle, 0, 0);
    }

    private int dfs(List<List<Integer>> triangle, int i, int j) {
        if (i == triangle.size()) {
            return 0;
        }
        if (memo[i][j] != null) {
            return memo[i][j];  //这是每次都查看已计算这个值，如果已经被计算就直接提取就行了，不需要再重新计算
        }
        return memo[i][j] = Math.min(dfs(triangle, i + 1, j), dfs(triangle, i + 1, j + 1)) + triangle.get(i).get(j);  //这里将每次计算的结果都存储到memo数组中之后每次调用dfs函数都会先查看memo[i][j]是否有内容就可以不需要一次计算完
    }
}
```

MS(记忆搜索算法)：

相当于通过再定义一个数组，来保存每次计算的数值，这是一个自顶向下的递归。之后当调用dfs函数时，会先将判断存储数组中这个数字是否已经被计算，如果有值就返回这个值，就可以不用计算这个块了。**通过消耗内存来减少计算时间**

#### 方法三：动态规划

![image-20201019200520460](G:/Typora%20image/LC/image-20201019200520460.png)

```JAVA
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[][] f = new int[n][n];
        f[0][0] = triangle.get(0).get(0);
        for (int i = 1; i < n; ++i) {
            f[i][0] = f[i - 1][0] + triangle.get(i).get(0);//*
            for (int j = 1; j < i; ++j) {
                f[i][j] = Math.min(f[i - 1][j - 1], f[i - 1][j]) + triangle.get(i).get(j);
            }
            f[i][i] = f[i - 1][i - 1] + triangle.get(i).get(i);  //* 这里可以通过判断条件来代替，可能会更加清晰点，但这样写，就可以不用去判断了，应该会减少代码运行的时间，可以参考一下。
        }
        int minTotal = f[n - 1][0];
        for (int i = 1; i < n; ++i) {
            minTotal = Math.min(minTotal, f[n - 1][i]);
        }
        return minTotal;
    }
}
```

**这里再此说明一下动态规划算法，就是将一个大问题分解为多个小问题，计算后来代替之前的数组中的数据，这样我们只需要对比最后的数据就可以了**

#### 方法四：动态规划+空间优化

![image-20201019201328807](G:/Typora%20image/LC/image-20201019201328807.png)

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[][] f = new int[2][n];
        f[0][0] = triangle.get(0).get(0);
        for (int i = 1; i < n; ++i) {
            int curr = i % 2;
            int prev = 1 - curr;
            f[curr][0] = f[prev][0] + triangle.get(i).get(0);
            for (int j = 1; j < i; ++j) {
                f[curr][j] = Math.min(f[prev][j - 1], f[prev][j]) + triangle.get(i).get(j);
            }
            f[curr][i] = f[prev][i - 1] + triangle.get(i).get(i);
        }
        int minTotal = f[(n - 1) % 2][0];//这里是因为最后计算的一行是i=n-1的时候，所以对应到的就是f的(n-1)%2的那一个行，牛逼
        for (int i = 1; i < n; ++i) {
            minTotal = Math.min(minTotal, f[(n - 1) % 2][i]);
        }
        return minTotal;
    }
}
```

![image-20201019202337979](G:/Typora%20image/LC/image-20201019202337979.png)

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[] f = new int[n];
        f[0] = triangle.get(0).get(0);
        for (int i = 1; i < n; ++i) {
            f[i] = f[i - 1] + triangle.get(i).get(i);
            for (int j = i - 1; j > 0; --j) {
                f[j] = Math.min(f[j - 1], f[j]) + triangle.get(i).get(j);
            }
            f[0] += triangle.get(i).get(0);
        }
        int minTotal = f[0];
        for (int i = 1; i < n; ++i) {
            minTotal = Math.min(minTotal, f[i]);
        }
        return minTotal;
    }
}
```



### #121 买卖股票的最佳时机 

> 给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
>
> 如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。
>
> 注意：你不能在买入股票前卖出股票。
>
>  
>
> 示例 1:
>
> 输入: [7,1,5,3,6,4]
> 输出: 5
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
>      注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
> 示例 2:
>
> 输入: [7,6,4,3,1]
> 输出: 0
> 解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices.length==0){
            return 0;
        }
        int min_index = 0;
        int min = prices[0];
        int df_max = 0;
        for(int i =1;i<prices.length;i++){
            if(min>prices[i]){
                min_index = i;
                min = prices[i];
            }
            df_max = prices[i]-min>df_max?prices[i]-min:df_max;
        }
        return df_max;
    }
}
```

由于，我们只需要找出数组最小值，然后计算在最小值之后的数与他差距最大的数值就可以了。所以我们只需要遍历一轮数组就可以，在不断寻找数组最小值的同时计算最大差值。ez

### #122 买卖股票的最佳时机 II

> 给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
>  
>
> 示例 1:
>
> 输入: [7,1,5,3,6,4]
> 输出: 7
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
>      随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
> 示例 2:
>
> 输入: [1,2,3,4,5]
> 输出: 4
> 解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
>      注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
>      因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
> 示例 3:
>
> 输入: [7,6,4,3,1]
> 输出: 0
> 解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
>
>
> 提示：
>
> 1 <= prices.length <= 3 * 10 ^ 4
> 0 <= prices[i] <= 10 ^ 4

my code

```java
class Solution {
    public int maxProfit(int[] prices) {
        int min_index =0;
        int min = prices[0];
        int df_max = 0;
        int buy_lambda = 1;
        int cixuup_lambda =0;
        for(int i=1;i<prices.length;i++){
            if(buy_lambda==1 && min>prices[i]){
                min_index = i;
                min = prices[i];
                buy_lambda = 0;
            }
            if(i+1!=prices.length&&prices[i]>prices[i+1]){
                df_max = df_max+prices[i]-prices[min_index];
                min = prices[i+1];
                min_index = i+1;
                buy_lambda =1;
            }else{
                cixuup_lambda = 1;
            }
            if(cixuup_lambda==1&&i==prices.length-1){
                df_max = df_max+ prices[i]-prices[min_index];
            }
        }
        return df_max;
    }
}
```

想的太复杂了，其实只要现在这个比前一个大的话就加进去就行了。

```java
class Solution {
    public int maxProfit(int[] prices) {
        int maxprofit = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1])
                maxprofit += prices[i] - prices[i - 1];
        }
        return maxprofit;
    }
}
```

ezezez

### #两数之和 Ⅱ -输入有序数组

> 给定一个已按照升序排列 的有序数组，找到两个数使得它们相加之和等于目标数。
>
> 函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。
>
> 说明:
>
> 返回的下标值（index1 和 index2）不是从零开始的。
> 你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。
> 示例:
>
> 输入: numbers = [2, 7, 11, 15], target = 9
> 输出: [1,2]
> 解释: 2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

#### 第一种方法:利用上一次的方法来做

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int[] sum_index = new int[2];
        HashMap<Integer,Integer> hm = new HashMap();
        for(int i=0;i<numbers.length;i++){
            hm.put(numbers[i],i+1);
        }
        
        for(int i=0;i<numbers.length;i++){
            int w = target-numbers[i];
            if(hm.get(w)!=null && hm.get(w)!=i+1){
                if(hm.get(w)>i) return new int[]{i+1,hm.get(w)};
                else return new int[]{hm.get(w),i+1};
            }
        }
        return new int[2];
    }
}
```

时间复杂度：O（2n）

**注意：** 这次的条件比上次条件不同的是这个序列是升序序列，所以似乎可以不用hashmap来做。可以只用一次循环来做。

#### 双指针方法*

**初始时两个指针分别指向第一个元素位置和最后一个元素的位置。每次计算两个指针指向的两个元素之和，并和目标值比较。如果两个元素之和等于目标值，则发现了唯一解。如果两个元素之和小于目标值，则将左侧指针右移一位。如果两个元素之和大于目标值，则将右侧指针左移一位。移动指针之后，重复上述操作，直到找到答案。**

使用双指针的实质是缩小查找范围。那么会不会把可能的解过滤掉？答案是不会。假设 $\text{numbers}[i]+\text{numbers}[j]=\text{target}$ 是唯一解，其中$ 0 \leq i<j \leq \text{numbers.length}-1$。初始时两个指针分别指向下标 0 和下标 $\text{numbers.length}-1$，左指针指向的下标小于或等于 i，右指针指向的下标大于或等于 j。除非初始时左指针和右指针已经位于下标 i和 j，否则一定是左指针先到达下标 i 的位置或者右指针先到达下标 j 的位置。

如果左指针先到达下标 i 的位置，此时右指针还在下标 j 的右侧，$\text{sum}>\text{target}$，因此一定是右指针左移，左指针不可能移到 i 的右侧。

如果右指针先到达下标 j 的位置，此时左指针还在下标 i 的左侧，$\text{sum}<\text{target}$，因此一定是左指针右移，右指针不可能移到 j 的左侧。

由此可见，在整个移动过程中，左指针不可能移到 i 的右侧，右指针不可能移到 j 的左侧，因此不会把可能的解过滤掉。由于题目确保有唯一的答案，因此使用双指针一定可以找到答案。

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int low = 0, high = numbers.length - 1;
        while (low < high) {
            int sum = numbers[low] + numbers[high];
            if (sum == target) {
                return new int[]{low + 1, high + 1};
            } else if (sum < target) {
                ++low;
            } else {
                --high;
            }
        }
        return new int[]{-1, -1};
    }
}
```

！！！

### #168. Excel表列名称

> 给定一个正整数，返回它在 Excel 表中相对应的列名称。
>
> 例如，
>
>     1 -> A
>     2 -> B
>     3 -> C
>     ...
>     26 -> Z
>     27 -> AA
>     28 -> AB 
>     ...
> 示例 1:
>
> 输入: 1
> 输出: "A"
> 示例 2:
>
> 输入: 28
> 输出: "AB"
> 示例 3:
>
> 输入: 701
> 
>

```java
class Solution {
    public String convertToTitle(int n) {
        StringBuilder sb = new StringBuilder();
        while (n > 0) {
            int c = n % 26;
            if(c == 0){
                c = 26;
                n -= 1;
            }
            sb.insert(0, (char) ('A' + c - 1));
            n /= 26;
        }
        return sb.toString();
    }
}
```

可以只写从char+int转换到 char，这算是强制转换，说明强制转换可以适用于不同的类型的数值。ez

### # 多数元素

> 给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。
>
> 你可以假设数组是非空的，并且给定的数组总是存在多数元素。
>
>  
>
> 示例 1:
>
> 输入: [3,2,3]
> 输出: 3
> 示例 2:
>
> 输入: [2,2,1,1,1,2,2]
> 输出: 2

```java
class Solution {
    public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        if(nums.length>2) return nums[nums.length/2];
        else return nums[0];
    }
}
```

将数组排序后，返回 n/2的数值就行了，排除nums.length=1和2的特殊情况就行了。ezez

### # 171. Excel表列序号

> 给定一个Excel表格中的列名称，返回其相应的列序号。
>
> 例如，
>
>     A -> 1
>     B -> 2
>     C -> 3
>     ...
>     Z -> 26
>     AA -> 27
>     AB -> 28 
>     ...
> 示例 1:
>
> 输入: "A"
> 输出: 1
> 示例 2:
>
> 输入: "AB"
> 输出: 28
> 示例 3:
>
> 输入: "ZY"
> 输出: 701

```java
class Solution {
    public int titleToNumber(String s) {
        StringBuilder sb = new StringBuilder(s);
        int stringLength = sb.length();
        int sum = 0;
        int j = 0;
        for(int i = stringLength-1;i>=0;i--){
            sum += ((int)sb.charAt(i)-64) * (int)Math.pow(26,j++);
        }
        return sum;
        
    }
}
```

ezez

### #172.阶乘后的零*

> 给定一个整数 n，返回 n! 结果尾数中零的数量。
>
> 示例 1:
>
> 输入: 3
> 输出: 0
> 解释: 3! = 6, 尾数中没有零。
> 示例 2:
>
> 输入: 5
> 输出: 1
> 解释: 5! = 120, 尾数中有 1 个零.
> 说明: 你算法的时间复杂度应为 O(log n) 。

自己一开始的想法是通过先计算出阶乘，再去计算末尾有多少个零。但这样会造成超时。

```java
import java.math.BigInteger;
class Solution {
    public int trailingZeroes(int n) {
        
        // Calculate n!
        BigInteger nFactorial = BigInteger.ONE;
        for (int i = 2; i <= n; i++) {
            nFactorial = nFactorial.multiply(BigInteger.valueOf(i));
        }
                        
        // Count how many 0's are on the end.
        int zeroCount = 0;
        
        while (nFactorial.mod(BigInteger.TEN).equals(BigInteger.ZERO)) {
            nFactorial = nFactorial.divide(BigInteger.TEN);
            zeroCount++;
        }
        
        return zeroCount;
    }
}
```

于是看别人解答如下：

之前小红书面试的时候碰到的一道题，没想到又是 leetcode 的原题。这种没有通用解法的题，完全依靠于对题目的分析理解了，自己当时也是在面试官的提示下慢慢出来的，要是想不到题目的点，还是比较难做的。

首先肯定不能依赖于把阶乘算出来再去判断有多少个零了，因为阶乘很容易就溢出了，所以先一步一步理一下思路吧。

首先末尾有多少个 0 ，只需要给当前数乘以一个 10 就可以加一个 0。

再具体对于 5!，也就是 5 * 4 * 3 * 2 * 1 = 120，我们发现结果会有一个 0，原因就是 2 和 5 相乘构成了一个 10。而对于 10 的话，其实也只有 2 * 5 可以构成，所以我们只需要找有多少对 2/5。

我们把每个乘数再稍微分解下，看一个例子。

11! = 11 * 10 * 9 * 8 * 7 * 6 * 5 * 4 * 3 * 2 * 1 = 11 * (2 * 5) * 9 * (4 * 2) * 7 * (3 * 2) * (1 * 5) * (2 * 2) * 3 * (1 * 2) * 1

对于含有 2 的因子的话是 1 * 2, 2 * 2, 3 * 2, 4 * 2 ...

对于含有 5 的因子的话是 1 * 5, 2 * 5...

含有 2 的因子每两个出现一次，含有 5 的因子每 5 个出现一次，所有 2 出现的个数远远多于 5，换言之找到一个 5，一定能找到一个 2 与之配对。所以我们只需要找有多少个 5。

直接的，我们只需要判断每个累乘的数有多少个 5 的因子即可。

```java
public int trailingZeroes(int n) {
    int count = 0;
    for (int i = 1; i <= n; i++) {
        int N = i;
        while (N > 0) {
            if (N % 5 == 0) {
                count++;
                N /= 5;
            } else {
                break;
            }
        }
    }
    return count;

}
```

但发生了超时，我们继续分析。

对于一个数的阶乘，就如之前分析的，5 的因子一定是每隔 5 个数出现一次，也就是下边的样子。

n! = 1 * 2 * 3 * 4 * (1 * 5) * ... * (2 * 5) * ... * (3 * 5) *... * n

因为每隔 5 个数出现一个 5，所以计算出现了多少个 5，我们只需要用 n/5 就可以算出来。

但还没有结束，继续分析。

... * (1 * 5) * ... * (1 * 5 * 5) * ... * (2 * 5 * 5) * ... * (3 * 5 * 5) * ... * n

每隔 25 个数字，出现的是两个 5，所以除了每隔 5 个数算作一个 5，每隔 25 个数，还需要多算一个 5。

也就是我们需要再加上 n / 25 个 5。

同理我们还会发现每隔 5 * 5 * 5 = 125 个数字，会出现 3 个 5，所以我们还需要再加上 n / 125 。

综上，规律就是每隔 5 个数，出现一个 5，每隔 25 个数，出现 2 个 5，每隔 125 个数，出现 3 个 5... 以此类推。

最终 5 的个数就是 n / 5 + n / 25 + n / 125 ...

写程序的话，如果直接按照上边的式子计算，分母可能会造成溢出。所以算 n / 25 的时候，我们先把 n 更新，n = n / 5，然后再计算 n / 5 即可。后边的同理。

```java
public int trailingZeroes(int n) {
    int count = 0;
    while (n > 0) {
        count += n / 5;
        n = n / 5;
    }
    return count;
}
```

### #217.存在重复元素

> 给定一个整数数组，判断是否存在重复元素。
>
> 如果任意一值在数组中出现至少两次，函数返回 true 。如果数组中每个元素都不相同，则返回 false 。
>
>  
>
> 示例 1:
>
> 输入: [1,2,3,1]
> 输出: true
> 示例 2:
>
> 输入: [1,2,3,4]
> 输出: false
> 示例 3:
>
> 输入: [1,1,1,3,3,4,3,2,4,2]
> 输出: true

自己的解法是通过将数组放在set集合中，这样利用set集合特别元素不能重复的特性，对比set数组的长度和原数组的长度就可以对比出是否有重复元素。

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> s = new HashSet<>();
        for(int e:nums) s.add(e);
        return s.size()==nums.length?false:true;
    }
}
```

### #219.存在重复元Ⅱ

>给定一个整数数组和一个整数 k，判断数组中是否存在两个不同的索引 i 和 j，使得 nums [i] = nums [j]，并且 i 和 j 的差的 绝对值 至多为 k。
>
> 
>
>示例 1:
>
>输入: nums = [1,2,3,1], k = 3
>输出: true
>示例 2:
>
>输入: nums = [1,0,1,1], k = 1
>输出: true
>示例 3:
>
>输入: nums = [1,2,3,1,2,3], k = 2
>输出: false

自己的解法：通过HashSet来判断是否有重复元素，通过HashMap来计算重复元素索引之间的差值。

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        Set<Integer> s = new HashSet<>();
        HashMap<Integer,Integer> hm = new HashMap<>();
        for(int i = 0;i<nums.length;i++){
            if(s.add(nums[i])){
                hm.put(nums[i],i);
            }else{
                if(Math.abs(hm.get(nums[i])-i)<=k) return true;
                hm.remove(nums[i]);
                hm.put(nums[i],i);
            }
        }
        return false;
    }
}
```

ezez

散列表法：

> 思路
>
> 用散列表来维护这个kk大小的滑动窗口。
>
> 算法
>
> 在之前的方法中，我们知道了对数时间复杂度的 搜索 操作是不够的。在这个方法里面，我们需要一个支持在常量时间内完成 搜索，删除，插入 操作的数据结构，那就是散列表。这个算法的实现跟方法二几乎是一样的。
>
> 遍历数组，对于每个元素做以下操作：
> 在散列表中搜索当前元素，如果找到了就返回 true。
> 在散列表中插入当前元素。
> 如果当前散列表的大小超过了 kk， 删除散列表中最旧的元素。
> 返回 false。

```java
public boolean containsNearbyDuplicate(int[] nums, int k) {
    Set<Integer> set = new HashSet<>();
    for (int i = 0; i < nums.length; ++i) {
        if (set.contains(nums[i])) return true;
        set.add(nums[i]);
        if (set.size() > k) {
            set.remove(nums[i - k]);
        }
    }
    return false;
}
```

### #228.汇总区间

> 给定一个无重复元素的有序整数数组 nums 。
>
> 返回 恰好覆盖数组中所有数字 的 最小有序 区间范围列表。也就是说，nums 的每个元素都恰好被某个区间范围所覆盖，并且不存在属于某个范围但不属于 nums 的数字 x 。
>
> 列表中的每个区间范围 [a,b] 应该按如下格式输出：
>
> "a->b" ，如果 a != b
> "a" ，如果 a == b
>
>
> 示例 1：
>
> 输入：nums = [0,1,2,4,5,7]
> 输出：["0->2","4->5","7"]
> 解释：区间范围是：
> [0,2] --> "0->2"
> [4,5] --> "4->5"
> [7,7] --> "7"
> 示例 2：
>
> 输入：nums = [0,2,3,4,6,8,9]
> 输出：["0","2->4","6","8->9"]
> 解释：区间范围是：
> [0,0] --> "0"
> [2,4] --> "2->4"
> [6,6] --> "6"
> [8,9] --> "8->9"
> 示例 3：
>
> 输入：nums = []
> 输出：[]
> 示例 4：
>
> 输入：nums = [-1]
> 输出：["-1"]
> 示例 5：
>
> 输入：nums = [0]
> 输出：["0"]

自己的方法：

```java
class Solution {
    public List<String> summaryRanges(int[] nums) {
        ArrayList<String> al =  new ArrayList<>();
        if(nums.length == 0) return al;
        int a = nums[0];
        for(int i = 0;i<nums.length;i++){
            if(i!=nums.length-1&&nums[i]+1!=nums[i+1]){
                if(a!=nums[i]) al.add(String.valueOf(a)+"->"+String.valueOf(nums[i]));
                else al.add(String.valueOf(a));
                a = nums[i+1];
                // continue;
            }
            if(i==nums.length-1){
                if(a==nums[i]) al.add(String.valueOf(a));
                else al.add(String.valueOf(a)+"->"+String.valueOf(nums[i]));
            }
        }
        return al;
    }
}
```

ezez

### #268.丢失的数字

> 给定一个包含 [0, n] 中 n 个数的数组 nums ，找出 [0, n] 这个范围内没有出现在数组中的那个数。
>
>  
>
> 进阶：
>
> 你能否实现线性时间复杂度、仅使用额外常数空间的算法解决此问题?
>
>
> 示例 1：
>
> 输入：nums = [3,0,1]
> 输出：2
> 解释：n = 3，因为有 3 个数字，所以所有的数字都在范围 [0,3] 内。2 是丢失的数字，因为它没有出现在 nums 中。
> 示例 2：
>
> 输入：nums = [0,1]
> 输出：2
> 解释：n = 2，因为有 2 个数字，所以所有的数字都在范围 [0,2] 内。2 是丢失的数字，因为它没有出现在 nums 中。
> 示例 3：
>
> 输入：nums = [9,6,4,2,3,5,7,0,1]
> 输出：8
> 解释：n = 9，因为有 9 个数字，所以所有的数字都在范围 [0,9] 内。8 是丢失的数字，因为它没有出现在 nums 中。
> 示例 4：
>
> 输入：nums = [0]
> 输出：1
> 解释：n = 1，因为有 1 个数字，所以所有的数字都在范围 [0,1] 内。1 是丢失的数字，因为它没有出现在 nums 中。
>
>
> 提示：
>
> n == nums.length
> 1 <= n <= 104
> 0 <= nums[i] <= n
> nums 中的所有数字都 独一无二

 ez

```java
class Solution {
    public int missingNumber(int[] nums) {
        int len = nums.length;
        int sum = 0;
        int nums_sum = 0;
        if(len%2==0){
            sum = (len+1)*(len/2);
        }else{
            sum = (len+1)*(len/2)+(len+1)/2;
        }

        for(int e:nums) nums_sum += e;

        return sum - nums_sum; 
    }
}
```

将n的和减去nums的和就是丢失那个数字。

### 283.移动零

> 给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。
>
> 示例:
>
> 输入: [0,1,0,3,12]
> 输出: [1,3,12,0,0]
> 说明:
>
> 必须在原数组上操作，不能拷贝额外的数组。
> 尽量减少操作次数。

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int nums_n = 0;
        int j = 0;
        for(int i =0;i<nums.length;i++){
            if(nums[i]!=0){
                nums[j++] = nums[i];
                nums_n++;
            }
        }
        for(int i = nums_n;i<nums.length;i++){
            nums[i] = 0;
        }
    }
}
```

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int j = 0;
        for(int i =0;i<nums.length;i++){
            if(nums[i]!=0){
                int temp = nums[j];
                nums[j++] = nums[i];
                nums[i] = temp;
            }
        }
    }
}
```

ezez

### 414. 第三大的数

> 给定一个非空数组，返回此数组中第三大的数。如果不存在，则返回数组中最大的数。要求算法时间复杂度必须是O(n)。
>
> 示例 1:
>
> 输入: [3, 2, 1]
>
> 输出: 1
>
> 解释: 第三大的数是 1.
> 示例 2:
>
> 输入: [1, 2]
>
> 输出: 2
>
> 解释: 第三大的数不存在, 所以返回最大的数 2 .
> 示例 3:
>
> 输入: [2, 2, 3, 1]
>
> 输出: 1
>
> 解释: 注意，要求返回第三大的数，是指第三大且唯一出现的数。
> 存在两个值为2的数，它们都排第二。

```java
class Solution {
    public int thirdMax(int[] nums) {
        if(nums.length==1) return nums[0];
        int n1 = nums[0];
        int n2 = -2147483648;
        int n3 = -2147483648;
        int lambda = 1;
        for(int i = 1;i<nums.length;i++){
            if(nums[i]>=n1){
                if(nums[i] == n1) continue;
                n3 = n2;
                n2 = n1;
                n1 = nums[i];
                lambda++;
                continue;
            }
            if(nums[i]>=n2){
                if(nums[i] == -2147483648){
                    lambda++;
                    continue;
                } 
                if (nums[i] == n2) continue;
                n3 = n2;
                n2 = nums[i];
                lambda++;
                continue;
            }
            if(nums[i]>=n3){
                n3 = nums[i];
                lambda++;
            }
        }
        if(lambda<3) return n1;
        else return n3;
        
    }
}
```

ezez，虽然简单，但是却忽略了代码的稳定性，一开始忘记考虑了很多代码上的小BUG，所以导致了代码的不稳定性，所以一定要考虑好代码的方方面面。

### 448.找到所有数组中消失的数字*

> 给定一个范围在  1 ≤ a[i] ≤ n ( n = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。
>
> 找到所有在 [1, n] 范围之间没有出现在数组中的数字。
>
> 您能在不使用额外空间且时间复杂度为O(n)的情况下完成这个任务吗? 你可以假定返回的数组不算在额外空间内。
>
> 示例:
>
> 输入:
> [4,3,2,7,8,2,3,1]
>
> 输出:
> [5,6]

自己的方法，但超时了

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> l = new ArrayList<>();
        for(int i = 1;i<=nums.length;i++){
            l.add(i);
        }
        for(int e : nums){
            l.remove(Integer.valueOf(e));
        }
        return l;
    }
}
```

官方的方法

#### 使用哈希表

![image-20201104202335292](G:/Typora%20image/LC/image-20201104202335292.png)



![image-20201104202606511](G:/Typora%20image/LC/image-20201104202606511.png)

![image-20201104202612390](G:/Typora%20image/LC/image-20201104202612390.png)

![image-20201104202618550](G:/Typora%20image/LC/image-20201104202618550.png)

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        
        // Hash table for keeping track of the numbers in the array
        // Note that we can also use a set here since we are not 
        // really concerned with the frequency of numbers.
        HashMap<Integer, Boolean> hashTable = new HashMap<Integer, Boolean>();
        
        // Add each of the numbers to the hash table
        for (int i = 0; i < nums.length; i++) {
            hashTable.put(nums[i], true);
        }
        
        // Response array that would contain the missing numbers
        List<Integer> result = new LinkedList<Integer>();
        
        // Iterate over the numbers from 1 to N and add all those
        // that don't appear in the hash table. 
        for (int i = 1; i <= nums.length; i++) {
            if (!hashTable.containsKey(i)) {
                result.add(i);
            }
        }
        
        return result;
    }
}
```

#### 方法二：原地修改

![image-20201104202809597](G:/Typora%20image/LC/image-20201104202809597.png)

![image-20201104203330346](G:/Typora%20image/LC/image-20201104203330346.png)

![image-20201104203338135](G:/Typora%20image/LC/image-20201104203338135.png)

![image-20201104203344622](G:/Typora%20image/LC/image-20201104203344622.png)

![image-20201104203351026](G:/Typora%20image/LC/image-20201104203351026.png)

![image-20201104203356173](G:/Typora%20image/LC/image-20201104203356173.png)

![image-20201104203402558](G:/Typora%20image/LC/image-20201104203402558.png)

···

![image-20201104203420471](G:/Typora%20image/LC/image-20201104203420471.png)

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        
        // Iterate over each of the elements in the original array
        for (int i = 0; i < nums.length; i++) {
            
            // Treat the value as the new index
            int newIndex = Math.abs(nums[i]) - 1;
            
            // Check the magnitude of value at this new index
            // If the magnitude is positive, make it negative 
            // thus indicating that the number nums[i] has 
            // appeared or has been visited.
            if (nums[newIndex] > 0) {
                nums[newIndex] *= -1;
            }
        }
        
        // Response array that would contain the missing numbers
        List<Integer> result = new LinkedList<Integer>();
        
        // Iterate over the numbers from 1 to N and add all those
        // that have positive magnitude in the array
        for (int i = 1; i <= nums.length; i++) {
            
            if (nums[i - 1] > 0) {
                result.add(i);
            }
        }
        
        return result;
    }
}
```

要学会用原地算法来做，来标记。

### #485.最大连续1的个数



> 给定一个二进制数组， 计算其中最大连续1的个数。
>
> 示例 1:
>
> 输入: [1,1,0,1,1,1]
> 输出: 3
> 解释: 开头的两位和最后的三位都是连续1，所以最大连续1的个数是 3.
> 注意：
>
> 输入的数组只包含 0 和1。
> 输入数组的长度是正整数，且不超过 10,000。

刚说完要学会原地算法，这道题就可以用原地算法来做。

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int sumOne = 0;
        int len = nums.length;
        if(len == 0) return 0;
        if(len==1){
            if(nums[0]==1) return 1;
            else return 0;
        }
        for(int i = 1;i<len;i++){
            if(nums[i]==1){
                nums[i] +=nums[i-1];
            }else{
                if(sumOne<nums[i-1]){
                    sumOne = nums[i-1];
                }
            }
        }
        return nums[len-1]>sumOne?nums[len-1]:sumOne;
    }
}
```

ezez

### #509.斐波那契数

> 斐波那契数，通常用 F(n) 表示，形成的序列称为斐波那契数列。该数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：
>
> F(0) = 0,   F(1) = 1
> F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
> 给定 N，计算 F(N)。
>
>  
>
> 示例 1：
>
> 输入：2
> 输出：1
> 解释：F(2) = F(1) + F(0) = 1 + 0 = 1.
> 示例 2：
>
> 输入：3
> 输出：2
> 解释：F(3) = F(2) + F(1) = 1 + 1 = 2.
> 示例 3：
>
> 输入：4
> 输出：3
> 解释：F(4) = F(3) + F(2) = 2 + 1 = 3.

第一种方法:迭代（耗时太长）

```java
class Solution {
    public int fib(int N) {
        if(N==1) return 1;
        if(N==0) return 0;
        return fib(N-1)+fib(N-2);
    }
}
```

第二种方法：用一个数组来存储，这样就可以减少时间。

```java
class Solution {
    public int fib(int N) {
        // if(N==1) return 1;
        // if(N==0) return 0;
        // return fib(N-1)+fib(N-2);

        if(N<=1) return N;
        int[] nums = new int[]{0,1};
        boolean flag = true;
        for(int i = 2;i<N+1;i++){
            if(flag){
                nums[0] +=nums[1];
                flag = false;
            }else{
                nums[1] +=nums[0];
                flag = true;
            }
        }
        return flag?nums[1]:nums[0];

    }
}
```

### #561.数组拆分Ⅰ

> 给定长度为 2n 的整数数组 nums ，你的任务是将这些数分成 n 对, 例如 (a1, b1), (a2, b2), ..., (an, bn) ，使得从 1 到 n 的 min(ai, bi) 总和最大。
>
> 返回该 最大总和 。
>
>  
>
> 示例 1：
>
> 输入：nums = [1,4,3,2]
> 输出：4
> 解释：所有可能的分法（忽略元素顺序）为：
> 1. (1, 4), (2, 3) -> min(1, 4) + min(2, 3) = 1 + 2 = 3
> 2. (1, 3), (2, 4) -> min(1, 3) + min(2, 4) = 1 + 2 = 3
> 3. (1, 2), (3, 4) -> min(1, 2) + min(3, 4) = 1 + 3 = 4
> 所以最大总和为 4
> 示例 2：
>
> 输入：nums = [6,2,6,5,1,2]
> 输出：9
> 解释：最优的分法为 (2, 1), (2, 5), (6, 6). min(2, 1) + min(2, 5) + min(6, 6) = 1 + 2 + 6 = 9

我的方法：先进行排序之后，从最大的数字到最小的数字不断判断2个数字的最小并加入到sum值。

```java
class Solution {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums);
        int sum = 0;
        for(int i = nums.length-1;i>=0;i--){
            sum += nums[i]<=nums[--i]?nums[i+1]:nums[i];
        }
        return sum;
    }
}
```

ezez

### #566.重塑矩阵

> 在MATLAB中，有一个非常有用的函数 reshape，它可以将一个矩阵重塑为另一个大小不同的新矩阵，但保留其原始数据。
>
> 给出一个由二维数组表示的矩阵，以及两个正整数r和c，分别表示想要的重构的矩阵的行数和列数。
>
> 重构后的矩阵需要将原始矩阵的所有元素以相同的行遍历顺序填充。
>
> 如果具有给定参数的reshape操作是可行且合理的，则输出新的重塑矩阵；否则，输出原始矩阵。
>
> 示例 1:
>
> 输入: 
> nums = 
> [[1,2],
>  [3,4]]
> r = 1, c = 4
> 输出: 
> [[1,2,3,4]]
> 解释:
> 行遍历nums的结果是 [1,2,3,4]。新的矩阵是 1 * 4 矩阵, 用之前的元素值一行一行填充新矩阵。
> 示例 2:
>
> 输入: 
> nums = 
> [[1,2],
>  [3,4]]
> r = 2, c = 4
> 输出: 
> [[1,2],
>  [3,4]]
> 解释:
> 没有办法将 2 * 2 矩阵转化为 2 * 4 矩阵。 所以输出原矩阵。
> 注意：
>
> 给定矩阵的宽和高范围在 [1, 100]。
> 给定的 r 和 c 都是正数。



我的方法

```java
class Solution {
    public int[][] matrixReshape(int[][] nums, int r, int c) {
        int rBefore = nums.length;
        int cBefore = nums[0].length;
        if(rBefore*cBefore!=r*c) return nums;
        int[][] numsAfter = new int[r][c];
        int a = 0;
        int b = 0;
        for(int i = 0;i<rBefore;i++){
            for(int j = 0;j<cBefore;j++){
                if(b>=c){
                    b=0;
                    a++;
                }
                numsAfter[a][b++] = nums[i][j];           
            }
        }
        return numsAfter;

    }
}
```

ezezez



### #605.种花问题

> 假设你有一个很长的花坛，一部分地块种植了花，另一部分却没有。可是，花卉不能种植在相邻的地块上，它们会争夺水源，两者都会死去。
>
> 给定一个花坛（表示为一个数组包含0和1，其中0表示没种植花，1表示种植了花），和一个数 n 。能否在不打破种植规则的情况下种入 n 朵花？能则返回True，不能则返回False。
>
> 示例 1:
>
> 输入: flowerbed = [1,0,0,0,1], n = 1
> 输出: True
> 示例 2:
>
> 输入: flowerbed = [1,0,0,0,1], n = 2
> 输出: False
> 注意:
>
> 数组内已种好的花不会违反种植规则。
> 输入的数组长度范围为 [1, 20000]。
> n 是非负整数，且不会超过输入数组的大小。



```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        if(flowerbed.length==1&&flowerbed[0]==0) return --n>0?false:true;
        for(int i = 0;i<flowerbed.length;i++){
            if(flowerbed[i]==0 &&i==0&&flowerbed[i+1]==0){
                n--;
                flowerbed[i]=1;
                continue;
            } 
            if(flowerbed[i]==0 && i==flowerbed.length-1&&flowerbed[i-1]==0){
                n--;
                flowerbed[i]=1;
                continue;

            }
            if(i>0&&i<flowerbed.length-1&&flowerbed[i]==0 && flowerbed[i-1]==0&&flowerbed[i+1]==0){
                n--;
                flowerbed[i]=1;
                continue;
            }
        }
        return n>0? false:true;
    }
}
```

ezez

### #628. 三个数的最大乘积

> 给定一个整型数组，在数组中找出由三个数组成的最大乘积，并输出这个乘积。
>
> 示例 1:
>
> 输入: [1,2,3]
> 输出: 6
> 示例 2:
>
> 输入: [1,2,3,4]
> 输出: 24
> 注意:
>
> 给定的整型数组长度范围是[3,104]，数组中所有的元素范围是[-1000, 1000]。
> 输入的数组中任意三个数的乘积不会超出32位有符号整数的范围。

 ```JAVA
class Solution {
    public int maximumProduct(int[] nums) {
        Arrays.sort(nums);
        int len = nums.length;
        // return nums[len-1]*nums[len-2]*nums[len-3];
        if(nums[len-1]>=0){
            return nums[len-2]*nums[len-3]>=nums[0]*nums[1]?nums[len-1]*nums[len-2]*nums[len-3]:nums[len-1]*nums[0]*nums[1];
        }
        return nums[len-2]*nums[len-3]<=nums[0]*nums[1]?nums[len-1]*nums[len-2]*nums[len-3]:nums[len-1]*nums[0]*nums[1];
    }
}
 ```

ezezez



### #643.子数组最大平均数Ⅰ

> 给定 n 个整数，找出平均数最大且长度为 k 的连续子数组，并输出该最大平均数。
>
> 示例 1:
>
> 输入: [1,12,-5,-6,50,3], k = 4
> 输出: 12.75
> 解释: 最大平均数 (12-5-6+50)/4 = 51/4 = 12.75
>
>
> 注意:
>
> 1 <= k <= n <= 30,000。
> 所给数据范围 [-10,000，10,000]。

自己的方法：复杂但没有重新创造空间。

```java
class Solution {
    public double findMaxAverage(int[] nums, int k) {
        int begin = 0;
        int end = k-1;
        for(int i = 1;i<=nums.length-k;i++){
            int sumBegin = 0;
            int sumEnd = 0;
            for(int j = begin;j<i;j++) sumBegin+=nums[j];
            for(int n = end+1;n<i+k;n++) sumEnd+=nums[n];
            if(sumBegin<sumEnd){
                begin = i;
                end = begin+k-1;
            } 
        }
        Double sum = 0.00;
        for(int i = begin;i<=end;i++) sum+=(double)nums[i];
        return sum/k;
    }
}
```

#### 方法一：累计求和

![image-20201110161823060](G:/Typora%20image/LC/image-20201110161823060.png)

```java
public class Solution {
	public double findMaxAverage(int[] nums, int k) {
		int[] sum = new int[nums.length];
		sum[0] = nums[0];
		for (int i = 1; i < nums.length; i++)
		sum[i] = sum[i - 1] + nums[i];
		double res = sum[k - 1] * 1.0 / k;
		for (int i = k; i < nums.length; i++) {
			res = Math.max(res, (sum[i] - sum[i - k]) * 1.0 / k);
		}
		return res;
	}
}
```



### #661.图片平滑器

> 包含整数的二维矩阵 M 表示一个图片的灰度。你需要设计一个平滑器来让每一个单元的灰度成为平均灰度 (向下舍入) ，平均灰度的计算是周围的8个单元和它本身的值求平均，如果周围的单元格不足八个，则尽可能多的利用它们。
>
> 示例 1:
>
> 输入:
> [[1,1,1],
>  [1,0,1],
>  [1,1,1]]
> 输出:
> [[0, 0, 0],
>  [0, 0, 0],
>  [0, 0, 0]]
> 解释:
> 对于点 (0,0), (0,2), (2,0), (2,2): 平均(3/4) = 平均(0.75) = 0
> 对于点 (0,1), (1,0), (1,2), (2,1): 平均(5/6) = 平均(0.83333333) = 0
> 对于点 (1,1): 平均(8/9) = 平均(0.88888889) = 0
> 注意:
>
> 给定矩阵中的整数范围为 [0, 255]。
> 矩阵的长和宽的范围均为 [1, 150]。

```java
class Solution {
    public int[][] imageSmoother(int[][] M) {
        int R = M.length, C = M[0].length;
        int[][] ans = new int[R][C];

        for (int r = 0; r < R; ++r)
            for (int c = 0; c < C; ++c) {
                int count = 0;
                for (int nr = r-1; nr <= r+1; ++nr)
                    for (int nc = c-1; nc <= c+1; ++nc) {
                        if (0 <= nr && nr < R && 0 <= nc && nc < C) {
                            ans[r][c] += M[nr][nc];
                            count++;
                        }
                    }
                ans[r][c] /= count;
            }
        return ans;
    }
}
```

通过使用一个相同大小的数组来储存周围的8个数加上自己的数的合。然后再直接除

### #665.非递减数列 *

> 给你一个长度为 n 的整数数组，请你判断在 最多 改变 1 个元素的情况下，该数组能否变成一个非递减数列。
>
> 我们是这样定义一个非递减数列的： 对于数组中所有的 i (0 <= i <= n-2)，总满足 nums[i] <= nums[i + 1]。
>
>  
>
> 示例 1:
>
> 输入: nums = [4,2,3]
> 输出: true
> 解释: 你可以通过把第一个4变成1来使得它成为一个非递减数列。
> 示例 2:
>
> 输入: nums = [4,2,1]
> 输出: false
> 解释: 你不能在只改变一个元素的情况下将其变为非递减数列。

#### 好难我日

#### 方法一：向下拐点的寻找与消除

这道题可以看做是找向下拐点的过程，如果向下拐点存在，那么你就需要作出改变数字的操作了

![搜狗截图20200804093851.png](G:/Typora%20image/LC/6059e2af4b8a39e3d586033bc59077904e85f2adb6a348d96310a86627381276-%E6%90%9C%E7%8B%97%E6%88%AA%E5%9B%BE20200804093851.png)

根据题意，非递减序列可以看做一个非单调的递增序列，那么我们改变向下拐点的方法也就有两种：
下移

![搜狗截图20200804093852.png](G:/Typora%20image/LC/0b58aac8ff2c0d7c880bb8eb41630b6b204b5a8ca8ad9a991b9223d9bd37f8de-%E6%90%9C%E7%8B%97%E6%88%AA%E5%9B%BE20200804093852.png)

上移

![搜狗截图20200804094626.png](G:/Typora%20image/LC/eb6dcc672e6331e8ebd7fb4cd780eb49a5542a90d396a3f11ddb6cd3d19224fb-%E6%90%9C%E7%8B%97%E6%88%AA%E5%9B%BE20200804094626.png)

如何确定是哪一种，就需要向下拐点的前一个元素来帮忙了。
显然，如果在拐点前一位元素＜拐点后一位元素时，选择向上移的方法就有可能存在拐点没有被消除的现象，而如果选择向下移，就可以完全避免这种情况：

![搜狗截图20200804095100.png](G:/Typora%20image/LC/fd496e90e610d5a8b7b74be4703ca136759248ae752e719838b5adb94e9175d6-%E6%90%9C%E7%8B%97%E6%88%AA%E5%9B%BE20200804095100.png)

如果前一位元素≥后一位元素，就只能采用上移才能保证向下拐点消除：

![搜狗截图20200804095848.png](G:/Typora%20image/LC/835de2e892aae37780c66e44af479803ee3585318f4740949629c8c799bcf1a9-%E6%90%9C%E7%8B%97%E6%88%AA%E5%9B%BE20200804095848.png)


操作一次计数器增加1，当出现第二个向下拐点时，就说明改变一次是不行滴，返回false。

```java
class Solution {
    public boolean checkPossibility(int[] nums) {
        int count = 0;
        for(int i = 1;i<nums.length;i++){
            if(nums[i-1]>nums[i]){
                count++;
                if(count>1) return false;
                if(i-2>=0 && nums[i-2]>nums[i]){
                    nums[i]=nums[i-1];     //nums[i]上移           
                }
                else nums[i-1]=nums[i];   //nums[i-1]下移
            }
        }
        return true;
    }
}
```

如果是最开始的元素大于第二个元素，那么也是下移。

**这种判断拐点的题，应该采用画图的方式来做！！！！**





# 顺序

作者：穷码农
链接：https://www.zhihu.com/question/36738189/answer/908664455
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



## 1. Pattern: Sliding window，**滑动窗口类型**

滑动窗口类型的题目经常是用来执行数组或是链表上某个区间（窗口）上的操作。比如找最长的全为1的子数组长度。滑动窗口一般从第一个元素开始，一直往右边一个一个元素挪动。当然了，根据题目要求，我们可能有固定窗口大小的情况，也有窗口的大小变化的情况。

![img](G:/Typora%20image/LC/v2-ec5aa95052f81e22fed272c87c423653_hd.jpg)![img](G:/Typora%20image/LC/v2-ec5aa95052f81e22fed272c87c423653_720w.jpg)

该图中，我们的窗子不断往右一格一个移动



下面**是一些我们用来判断我们可能需要上滑动窗口策略的方法**：

- 这个问题的输入是一些线性结构：比如链表呀，数组啊，字符串啊之类的
- 让你去求最长/最短子字符串或是某些特定的长度要求

**经典题目：**

Maximum Sum Subarray of Size K (easy)

Smallest Subarray with a given sum (easy)

Longest Substring with K Distinct Characters (medium)

Fruits into Baskets (medium)

No-repeat Substring (hard)

Longest Substring with Same Letters after Replacement (hard)

Longest Subarray with Ones after Replacement (hard)

------

## 2. Pattern: two points, **双指针类型**

双指针是这样的模式：两个指针朝着左右方向移动（双指针分为同向双指针和异向双指针），直到他们有一个或是两个都满足某种条件。双指针通常用在排好序的数组或是链表中寻找对子。比如，你需要去比较数组中每个元素和其他元素的关系时，你就需要用到双指针了。

我们需要双指针的原因是：如果你只用一个指针的话，你得来回跑才能在数组中找到你需要的答案。这一个指针来来回回的过程就很耗时和浪费空间了 — 这是考虑算法的复杂度分析的时候的重要概念。虽然brute force一个指针的解法可能会奏效，但时间复杂度一般会是O(n²)。在很多情况下，双指针能帮助我们找到空间或是时间复杂度更低的解。

![img](G:/Typora%20image/LC/v2-961e3bfeff0b5f3323956b4198c2811b_hd.jpg)![img](G:/Typora%20image/LC/v2-961e3bfeff0b5f3323956b4198c2811b_720w.jpg)上图是说，我们在排好序的数组里面找是否有一对数加起来刚好等于目标和



识别使用双指针的招数：

- 一般来说，数组或是链表是排好序的，你得在里头找一些组合满足某种限制条件
- 这种组合可能是一对数，三个数，或是一个子数组

**经典题目：**

Pair with Target Sum (easy)

Remove Duplicates (easy)

Squaring a Sorted Array (easy)

Triplet Sum to Zero (medium)

Triplet Sum Close to Target (medium)

Triplets with Smaller Sum (medium)

Subarrays with Product Less than a Target (medium)

Dutch National Flag Problem (medium)

------

## 3. Pattern: Fast & Slow pointers, **快慢指针类型**

**这种模式，有一个非常出门的名字，叫龟兔赛跑。**咱们肯定都知道龟兔赛跑啦。但还是再解释一下快慢指针：这种算法的两个指针的在数组上（或是链表上，序列上）的移动速度不一样。还别说，**这种方法在解决有环的链表和数组时特别有用**。

通过控制指针不同的移动速度（比如在环形链表上），这种算法证明了他们肯定会相遇的。快的一个指针肯定会追上慢的一个（可以想象成跑道上面跑得快的人套圈跑得慢的人）。

![img](G:/Typora%20image/LC/v2-2a365e4768a0ed84683257d2364a6e71_hd.jpg)![img](G:/Typora%20image/LC/v2-2a365e4768a0ed84683257d2364a6e71_720w.jpg)上面这个图演示了快慢两个指针最终在5相遇了



咋知道需要用快慢指针模式勒？

- 问题需要处理环上的问题，比如环形链表和环形数组
- 当你需要知道链表的长度或是某个特别位置的信息的时候

那啥时候用快慢指针而不是上面的双指针呢？

- 有些情形下，咱们不应该用双指针，比如我们在单链表上不能往回移动的时候。一个典型的需要用到快慢指针的模式的是当你需要去判断一个链表是否是回文的时候。

**经典题目：**

LinkedList Cycle (easy)

Start of LinkedList Cycle (medium)

Happy Number (medium)

Middle of the LinkedList (easy)

------

## 4. Pattern: Merge Intervals，**区间合并类型**

区间合并模式是一个用来处理有区间重叠的很高效的技术。在设计到区间的很多问题中，通常咱们需要要么判断是否有重叠，要么合并区间，如果他们重叠的话。这个模式是这么起作用的：

给两个区间，一个是a，另外一个是b。别小看就两个区间，他们之间的关系能跑出来6种情况。详细的就看图啦。

![img](G:/Typora%20image/LC/v2-603053309be9d035b3c8ccee773e46e7_hd.jpg)![img](G:/Typora%20image/LC/v2-603053309be9d035b3c8ccee773e46e7_720w.jpg)



理解和识别这六种情况，灰常重要。因为这能帮你解决一大堆问题。这些问题从插入区间到优化区间合并都有。

怎么识别啥时候用合并区间模式呀？

- 当你需要产生一堆相互之间没有交集的区间的时候
- 当你听到重叠区间的时候

**经典题目：**

Merge Intervals (medium)

Insert Interval (medium)

Intervals Intersection (medium)

Conflicting Appointments (medium)

------

## 5. Pattern: Cyclic Sort，**循环排序**

这种模式讲述的是一直很好玩的方法：可以用来处理数组中的数值限定在一定的区间的问题。这种模式一个个遍历数组中的元素，如果当前这个数它不在其应该在的位置的话，咱们就把它和它应该在的那个位置上的数交换一下。你可以尝试将该数放到其正确的位置上，但这复杂度就会是O(n^2)。这样的话，可能就不是最优解了。因此循环排序的优势就体现出来了。

![img](G:/Typora%20image/LC/v2-e5a2fe3faa0b55ad5c6d8f182039cd35_hd.jpg)![img](G:/Typora%20image/LC/v2-e5a2fe3faa0b55ad5c6d8f182039cd35_720w.jpg)

咋鉴别这种模式？

- 这些问题一般设计到排序好的数组，而且数值一般满足于一定的区间
- 如果问题让你需要在排好序/翻转过的数组中，寻找丢失的/重复的/最小的元素



**经典题目：**

Cyclic Sort (easy)

Find the Missing Number (easy)

Find all Missing Numbers (easy)

Find the Duplicate Number (easy)

Find all Duplicate Numbers (easy)

## 6. Pattern: In-place Reversal of a LinkedList，**链表翻转**

------

在众多问题中，题目可能需要你去翻转链表中某一段的节点。通常，要求都是你得原地翻转，就是重复使用这些已经建好的节点，而不使用额外的空间。这个时候，原地翻转模式就要发挥威力了。

这种模式每次就翻转一个节点。一般需要用到多个变量，一个变量指向头结点（下图中的current），另外一个（previous）则指向咱们刚刚处理完的那个节点。在这种固定步长的方式下，你需要先将当前节点（current）指向前一个节点（previous），再移动到下一个。同时，你需要将previous总是更新到你刚刚新鲜处理完的节点，以保证正确性。

![img](G:/Typora%20image/LC/v2-79af44147f0e31ef768b8867a43acac5_hd.jpg)![img](G:/Typora%20image/LC/v2-79af44147f0e31ef768b8867a43acac5_720w.jpg)

咱们怎么去甄别这种模式呢？

- 如果你被问到需要去翻转链表，要求不能使用额外空间的时候

**经典题目：**

Reverse a LinkedList (easy)

Reverse a Sub-list (medium)

Reverse every K-element Sub-list (medium)

------

## 7. Pattern: Tree Breadth First Search，**树上的BFS**

这种模式基于宽搜（Breadth First Search (BFS)），适用于需要遍历一颗树。借助于队列数据结构，从而能保证树的节点按照他们的层数打印出来。打印完当前层所有元素，才能执行到下一层。所有这种需要遍历树且需要一层一层遍历的问题，都能用这种模式高效解决。

这种树上的BFS模式是通过把根节点加到队列中，然后不断遍历直到队列为空。每一次循环中，我们都会把队头结点拿出来（remove），然后对其进行必要的操作。在删除每个节点的同时，其孩子节点，都会被加到队列中。

识别树上的BFS模式：

- 如果你被问到去遍历树，需要按层操作的方式（也称作层序遍历）

**经典题目：**

Binary Tree Level Order Traversal (easy)

Reverse Level Order Traversal (easy)

Zigzag Traversal (medium)

Level Averages in a Binary Tree (easy)

Minimum Depth of a Binary Tree (easy)

Level Order Successor (easy)

Connect Level Order Siblings (medium)

------

## 8. Pattern: Tree Depth First Search，**树上的DFS**

树形DFS基于深搜（Depth First Search (DFS)）技术来实现树的遍历。

咱们可以用递归（或是显示栈，如果你想用迭代方式的话）来记录遍历过程中访问过的父节点。

该模式的运行方式是从根节点开始，如果该节点不是叶子节点，我们需要干三件事：

1. 需要区别我们是先处理根节点（pre-order，前序），处理孩子节点之间处理根节点（in-order，中序），还是处理完所有孩子再处理根节点（post-order，后序）。
2. 递归处理当前节点的左右孩子。

识别树形DFS：

- 你需要按前中后序的DFS方式遍历树
- 如果该问题的解一般离叶子节点比较近。

**经典题目：**

Binary Tree Path Sum (easy)

All Paths for a Sum (medium)

Sum of Path Numbers (medium)

Path With Given Sequence (medium)

Count Paths for a Sum (medium)

------

## 9. Pattern: Two Heaps，**双堆类型**

很多问题中，我们被告知，我们拿到一大把可以分成两队的数字。为了解决这个问题，我们感兴趣的是，怎么把数字分成两半？使得：小的数字都放在一起，大的放在另外一半。双堆模式就能高效解决此类问题。

正如名字所示，该模式用到了两个堆，是不是很难猜？一个最小堆用来找最小元素；一个最大堆，拿到最大元素。这种模式将一半的元素放在最大堆中，这样你可以从这一堆中秒找到最大元素。同理，把剩下一半丢到最小堆中，O(1)时间找到他们中的最小元素。通过这样的方式，这一大堆元素的**中位数**就可以从两个堆的堆顶拿到数字，从而计算出来。

判断双堆模式的秘诀：

- 这种模式在优先队列，计划安排问题（Scheduling）中有奇效
- 如果问题让你找一组数中的最大/最小/中位数
- 有时候，这种模式在涉及到二叉树数据结构时也特别有用

**经典题目：**

Find the Median of a Number Stream (medium)

Sliding Window Median (hard)

Maximize Capital (hard)

------

## 10. Pattern: Subsets，**子集类型，一般都是使用多重DFS**

超级多的编程面试问题都会涉及到排列和组合问题。子集问题模式讲的是用BFS来处理这些问题。

这个模式是这样的：

给一组数字 [1, 5, 3]

1. 我们从空集开始：[[]]
2. 把第一个数（1），加到之前已经存在的集合中：[[], [1]];
3. 把第二个数（5），加到之前的集合中得到：[[], [1], [5], [1,5]];
4. 再加第三个数（3），则有：[[], [1], [5], [1,5], [3], [1,3], [5,3], [1,5,3]].

该模式的详细步骤如下：

![img](G:/Typora%20image/LC/v2-0409666e91e94287c167ef81670d19a5_hd.jpg)![img](G:/Typora%20image/LC/v2-0409666e91e94287c167ef81670d19a5_720w.jpg)

如果判断这种子集模式：

- 问题需要咱们去找数字的组合或是排列

**经典题目：**

Subsets (easy)

Subsets With Duplicates (easy)

Permutations (medium)

String Permutations by changing case (medium)

Balanced Parentheses (hard)

Unique Generalized Abbreviations (hard)

------

## 11. Pattern: Modified Binary Search，**改造过的二分**

当你需要解决的问题的输入是排好序的数组，链表，或是排好序的矩阵，要求咱们寻找某些特定元素。这个时候的不二选择就是二分搜索。这种模式是一种超级牛的用二分来解决问题的方式。

对于一组满足上升排列的数集来说，这种模式的步骤是这样的：

1. 首先，算出左右端点的中点。最简单的方式是这样的：middle = (start + end) / 2。但这种计算方式有不小的概率会出现整数越界。因此一般都推荐另外这种写法：middle = start + (end — start) / 2
2. 如果要找的目标改好和中点所在的数值相等，我们返回中点的下标就行
3. 如果目标不等的话：我们就有两种移动方式了
4. 如果目标比中点在的值小（key < arr[middle]）：将下一步搜索空间放到左边（end = middle - 1）
5. 如果比中点的值大，则继续在右边搜索，丢弃左边：left = middle + 1

图示该过程的话，如下图所示：

![img](G:/Typora%20image/LC/v2-29f25eef886240f3ed3767039fb8f1db_hd.jpg)![img](G:/Typora%20image/LC/v2-29f25eef886240f3ed3767039fb8f1db_720w.jpg)

**经典题目：**

Order-agnostic Binary Search (easy)

Ceiling of a Number (medium)

Next Letter (medium)

Number Range (medium)

Search in a Sorted Infinite Array (medium)

Minimum Difference Element (medium)

Bitonic Array Maximum (easy)

------

## 12. Pattern: Top ‘K’ Elements，**前K个系列**

任何让我们求解最大/最小/最频繁的K个元素的题，都遵循这种模式。

用来记录这种前K类型的最佳数据结构就是堆了（译者注：在Java中，改了个名，叫优先队列（PriorityQueue））。这种模式借助堆来解决很多这种前K个数值的问题。

这个模式是这样的：

1. 根据题目要求，将K个元素插入到最小堆或是最大堆。
2. 遍历剩下的还没访问的元素，如果当前出来到的这个元素比堆顶元素大，那咱们把堆顶元素先删除，再加当前元素进去。

![img](G:/Typora%20image/LC/v2-d42febfdf2d1ce2d2211e78ff8ea88db_hd.jpg)![img](G:/Typora%20image/LC/v2-d42febfdf2d1ce2d2211e78ff8ea88db_720w.jpg)

注意这种模式下，咱们不需要去排序数组，因为堆具有这种良好的局部有序性，这对咱们需要解决问题就够了。

识别最大K个元素模式：

- 如果你需要求最大/最小/最频繁的前K个元素
- 如果你需要通过排序去找一个特定的数

**经典题目：**

Top ‘K’ Numbers (easy)

Kth Smallest Number (easy)

‘K’ Closest Points to the Origin (easy)

Connect Ropes (easy)

Top ‘K’ Frequent Numbers (medium)

Frequency Sort (medium)

Kth Largest Number in a Stream (medium)

‘K’ Closest Numbers (medium)

Maximum Distinct Elements (medium)

Sum of Elements (medium)

Rearrange String (hard)

------

## 13. Pattern: K-way merge，**多路归并**

K路归并能帮咱们解决那些涉及到多组排好序的数组的问题。

每当你的输入是K个排好序的数组，你就可以用堆来高效顺序遍历其中所有数组的所有元素。你可以将每个数组中最小的一个元素加入到最小堆中，从而得到全局最小值。当我们拿到这个全局最小值之后，再从该元素所在的数组里取出其后面紧挨着的元素，加入堆。如此往复直到处理完所有的元素。

![img](G:/Typora%20image/LC/v2-3e133c0710ef919e120fc74275d5255b_hd.jpg)![img](G:/Typora%20image/LC/v2-3e133c0710ef919e120fc74275d5255b_720w.jpg)

该模式是这样的运行的：

1. 把每个数组中的第一个元素都加入最小堆中
2. 取出堆顶元素（全局最小），将该元素放入排好序的结果集合里面
3. 将刚取出的元素所在的数组里面的下一个元素加入堆
4. 重复步骤2，3，直到处理完所有数字

识别K路归并：

- 该问题的输入是排好序的数组，链表或是矩阵
- 如果问题让咱们合并多个排好序的集合，或是需要找这些集合中最小的元素

**经典题目：**

Merge K Sorted Lists (medium)

Kth Smallest Number in M Sorted Lists (Medium)

Kth Smallest Number in a Sorted Matrix (Hard)

Smallest Number Range (Hard)

------

## 14. Pattern: 0/1 Knapsack (Dynamic Programming)，**0/1背包类型**



经典题目：

0/1 Knapsack (medium)

Equal Subset Sum Partition (medium)

Subset Sum (medium)

Minimum Subset Sum Difference (hard)

------

## 15. Pattern: Topological Sort (Graph)，**拓扑排序类型**

拓扑排序模式用来寻找一种线性的顺序，这些元素之间具有依懒性。比如，如果事件B依赖于事件A，那A在拓扑排序顺序中排在B的前面。

这种模式定义了一种简单方式来理解拓扑排序这种技术。

这种模式是这样奏效的：

1. 初始化
   a) 借助于HashMap将图保存成邻接表形式。
   b) 找到所有的起点，用HashMap来帮助记录每个节点的入度
2. 创建图，找到每个节点的入度
   a) 利用输入，把图建好，然后遍历一下图，将入度信息记录在HashMap中
3. 找所有的起点
   a) 所有入度为0的节点，都是有效的起点，而且我们讲他们都加入到一个队列中
4. 排序
   a) 对每个起点，执行以下步骤
   —i) 把它加到结果的顺序中
   — ii)将其在图中的孩子节点取到
   — iii)将其孩子的入度减少1
   — iv)如果孩子的入度变为0，则改孩子节点成为起点，将其加入队列中
   b) 重复（a）过程，直到起点队列为空。





![img](G:/Typora%20image/LC/v2-8a8abca46a67d38c1be674f9c777c8ef_hd.jpg)![img](G:/Typora%20image/LC/v2-8a8abca46a67d38c1be674f9c777c8ef_720w.jpg)

拓扑排序模式识别：

- 待解决的问题需要处理无环图
- 你需要以一种有序的秩序更新输入元素
- 需要处理的输入遵循某种特定的顺序

**经典题目：**

Topological Sort (medium)

Tasks Scheduling (medium)

Tasks Scheduling Order (medium)

All Tasks Scheduling Orders (hard)

Alien Dictionary (hard)



大家好好练练这些题目，面试中遇到中高等难度的题目，应该就能解得不错了。

------

**第二门则是单独将动态规划（DP）的题目进行了细分。**

提到算法，绕不开的重点和难点就肯定会包括动态规划 -- DP，本文就把经典的DP问题按照分类列一下，大家可以按照**Recursion**，**Top-Down**，**Bottom-Up**三种方式都练一练。俗话说，熟能生巧，多练才是提高算法的不二法宝。

课程详细的内容，可以参考这里：

[Grokking Dynamic Programming Patterns for Coding Interviewswww.educative.io![图标](https://pic2.zhimg.com/v2-4bce6b312c6177a5f10303eddbc74b99_180x120.jpg)](https://link.zhihu.com/?target=https%3A//www.educative.io/courses/grokking-dynamic-programming-patterns-for-coding-interviews%3Faff%3DK7qB)

该门课程中, 作者将DP的问题分成以下几类:

## 1. 0/1 Knapsack, 0/1背包，6个题

0/1 Knapsack，0/1背包问题

Equal Subset Sum Partition，相等子集划分问题

Subset Sum，子集和问题

Minimum Subset Sum Difference，子集和的最小差问题

Count of Subset Sum，相等子集和的个数问题

Target Sum，寻找目标和的问题

## 2. Unbounded Knapsack，无限背包，5个题

Unbounded Knapsack，无限背包

Rod Cutting，切钢条问题

Coin Change，换硬币问题

Minimum Coin Change，凑齐每个数需要的最少硬币问题

Maximum Ribbon Cut，丝带的最大值切法

## 3. Fibonacci Numbers，斐波那契数列，6个题

Fibonacci numbers，斐波那契数列问题

Staircase，爬楼梯问题

Number factors，分解因子问题

Minimum jumps to reach the end，蛙跳最小步数问题

Minimum jumps with fee，蛙跳带有代价的问题

House thief，偷房子问题

## 4. Palindromic Subsequence，回文子系列，5个题

Longest Palindromic Subsequence，最长回文子序列

Longest Palindromic Substring，最长回文子字符串

Count of Palindromic Substrings，最长子字符串的个数问题

Minimum Deletions in a String to make it a Palindrome，怎么删掉最少字符构成回文

Palindromic Partitioning，怎么分配字符，形成回文

## 5. Longest Common Substring，最长子字符串系列，13个题

Longest Common Substring，最长相同子串

Longest Common Subsequence，最长相同子序列

Minimum Deletions & Insertions to Transform a String into another，字符串变换

Longest Increasing Subsequence，最长上升子序列

Maximum Sum Increasing Subsequence，最长上升子序列和

Shortest Common Super-sequence，最短超级子序列

Minimum Deletions to Make a Sequence Sorted，最少删除变换出子序列

Longest Repeating Subsequence，最长重复子序列

Subsequence Pattern Matching，子序列匹配

Longest Bitonic Subsequence，最长字节子序列

Longest Alternating Subsequence，最长交差变换子序列

Edit Distance，编辑距离

Strings Interleaving，交织字符串



大家可以先把以上35个题目练熟，这样DP到达中等水平肯定是okay了的。再加以训练和提高。突破算法的硬骨头不在话下。一定要按照三种方式对照起来练。