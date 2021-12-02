---
title: LeetCode-JAVA
date: 2021-09-20 10:48:01
tags: 
categories: "刷题记录"
---
<<<<<<< HEAD
按照[《Leetcode101-A Leetcode Gringding Guide》](https://github.com/changgyhub/leetcode_101)顺序记录。除此之外，开始正视代码书写规范。
=======
按照[《Leetcode101-A Leetcode Gringding Guide》](https://github.com/changgyhub/leetcode_101)顺序记录
>>>>>>> 52288f2321ba9974432bb20ba0d8a8ebb5f312a6
<!--more--> 
# 贪心算法  
## 445 分发饼干 easy
依次满足胃口最小的孩子。可以想到先排序，然后再去分配。注意当满足了孩子后,cookie再加一，因为一个饼干只能用一次。
```java
//知识点
/*
length()：String类的一个方法
字符串.length()
length() 方法用于返回字符串的长度。
长度等于字符串中 16 位 Unicode 代码单元的数量。

length：类的属性
数组.length
*/
```
```java 
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int cookie = 0, child = 0;
        while(child < g.length && cookie < s.length){
            if(g[child] <= s[cookie]){
                child++;
            }
            cookie++;//饼干只能用一次，满足条件再加1。
        }
        return child;
    }
}
```
## 135 分发糖果 hard
首先建立一个数组，然后初始化为1，也就是每个人都先分到一颗糖。然后从左往右边遍历，如果右边的孩子得分高于左边，则右边的孩子糖果数=左边的孩子糖果数+1，注意这里不是直接加1.因为分数可能是依次增加，还要求分数高的糖果多。然后从右往左遍历，如果左边的孩子分数大于右边孩子分数并且左边孩子的糖果数不如右边孩子糖果数，则左边孩子糖果数=右边孩子糖果数+1，这种情况对应于左边分数大于右边。
```java 
class Solution {//根据书上的思路写的，写法比较冗余。
    public int candy(int[] ratings) {
        int candys[] = new int[ratings.length];
        int count = 0; 
        for(int i = 0; i < ratings.length; i++){
            candys[i] = 1;
        }
        for(int i = 0; i < ratings.length-1; i++){
            if(ratings[i+1] > ratings[i]){
                candys[i+1] = candys[i] +1;
            }
        }
        for(int i = ratings.length-1; i > 0 ; i--){
            if(ratings[i-1] > ratings[i] && candys[i-1] <=  candys[i]){
                candys[i-1] = candys[i] +1 ;
            }
        }
        for(int i = 0; i < ratings.length; i++){
            count += candys[i]; 
        }
    return count;
    }
}
```
```java
class Solution {//这个有点不一样，分别创建两个数组，对应满足左右两个规则。然后分别取两个数组的最大值，为什么这样做可以？有个评论写得很清晰,如下。
/*
为什么取最大值是正确的思考：
很多人说这个问题显而易见，不值得讨论，但我相信还是有人像我一样不理解，在这里说一下我的想法
我疑惑的问题不是取最大值为啥是最优解，而是取最大值后为啥不影响某一规则的成立。
我们取序列中的任意两点，A B
如果 A > B ,则按照左规则处理后，B不会比A多；按照右规则处理后，A一定比B多，那么A一定会被更新（变大），但L、R规则仍然成立：B不会比A多，A一定比B多；
同理可讨论 A<B;
当 A == B，A、B的值无论如何更新，都不影响 L、R规则
综上，取最大值后不影响某一规则的成立。
*/

    public int candy(int[] ratings) {
        int[] left = new int[ratings.length];
        int[] right = new int[ratings.length];
        Arrays.fill(left, 1);
        Arrays.fill(right, 1);//把right数组全部填充为1，如果长度是长，则right=[1,1,1]
        for(int i = 1; i < ratings.length; i++)
            if(ratings[i] > ratings[i - 1]) left[i] = left[i - 1] + 1;
        int count = left[ratings.length - 1];
        for(int i = ratings.length - 2; i >= 0; i--) {
            if(ratings[i] > ratings[i + 1]) right[i] = right[i + 1] + 1;
            count += Math.max(left[i], right[i]);
        }
        return count;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/candy/solution/candy-cong-zuo-zhi-you-cong-you-zhi-zuo-qu-zui-da-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
## 435 无重叠区间 medium(可动态规划)
这里学到了Arrays.sort()新写法，开头一直纠结怎么把书上C++的排序用java表达(java用得少)。这里是要移除区间的最小个数，贪心的策略是：在选择要保留区间时，选择的区间结尾越小，余留给其他区间的空间就越大，就能保留更多的区间。首先对尾巴进行递增排序，也就是每个区间的第二个数字排序。然后对prve赋值为第一个区间的尾巴。开始进入for循环，如果第二个区间的头是小于prev，也就是在第一个的区间内，需要进行移除，如果大于了prev，则保留区间，然后prev赋值给第二个区间的尾巴，以此类推。
```java 
//首先是知识点笔记，部分来源于网络 Arrays.sort()
System.out.println(Arrays.deepToString(intervals));//这样可以输出二维数组的样子
System.out.println(Arrays.toString(intervals));//输出的貌似是地址，反正是这样的[[I@49993335, [I@20322d26, [I@192b07fd, [I@64bfbc86]
/*
方法一、使用Comparable接口：
让待排序对象所在的类实现Comparable接口，并重写Comparable接口中的compareTo() 。方法缺点是只能按照一种规则排序

方法二、使用Comparator接口 （推荐使用）
如果一个类要实现java.util.Comparator接口：它一定要实现
int compare(T o1, T o2) 函数，而另一个可以不实现（boolean equals(Object obj)） 。
使用编写排序方式类实现Comparator接口，并重写新Comparator接口中的compare()方法。优点：想用什么方式排就用什么方式排。

原理：观察Arrays.sort()源码如下：
public static <T> void sort(T[] a, Comparator<? super T> c) {
        if (c == null) {
            sort(a);
        } else {
            if (LegacyMergeSort.userRequested)
                legacyMergeSort(a, c);
            else
                TimSort.sort(a, 0, a.length, c, null, 0, 0);
        }
    }
所以，在传入要排序数组时，还可以传入一个Compartor接口（比较器），然后这个接口中要重写一个compare()方法，这个重写的compare()方法就是我们自己规定的比较规则。

实现Comparator接口，必须实现下面这个函数：
@Override
public int compare(CommentVo o1, CommentVo o2) {
           return o1.getTime().compareTo(o2.getTime());
}
这里o1表示位于前面的对象，o2表示后面的对象
返回-1（或负数），表示不需要交换01和02的位置，o1排在o2前面，asc
返回1（或正数），表示需要交换01和02的位置，o1排在o2后面，desc
*/
//所以本题的sort，比较尾巴就是interval1[1]，为了递增，左边尾巴减右边尾巴，负数就不需要交换，正数就交换。
```
```java
class Solution {//这个是贪心策略写法
    public int eraseOverlapIntervals(int[][] intervals) {
        if(intervals.length == 0){
            return 0;
        }
        
        Arrays.sort(intervals,new Comparator<int []>(){
            public int compare(int[] interval1, int[] interval2){
                return interval1[1]-interval2[1];
            }
        });//学废了吗？
        int total = 0, prev = intervals[0][1];
        for(int i = 1; i < intervals.length; i++){
            if(intervals[i][0] < prev){
                total++;
            }
            else{
                prev = intervals[i][1];
            }
        }
       
    return total;
    }
}

```
## 605 种花问题 easy
虽说是easy题，但是发现自己想的bug很多，看到了一个很清晰的解题方法：跳格法。注意题目是不能打破原来的种植规则！。情况1：当index遇到1的时候，也就是至少隔一格才能种花，所以i要跳两格。情况2：当index遇到0时候，如果下一格为0，则可以种花（此时n-1），并且顺便跳两格，这里还有个情况就是如果已经是最后一格了，那就也能种花，一开始会想，万一最后一格的前面一格是1呢？注意这个情况不会发生，因为你是跳格法，你跳的index就代表是可能种花的，只需要考虑后面就行。如果下一个格子为1(比如0100)，则这格不能种花，则i要跳3格才可以种花。
```java
class Solution {//这个写得比较清晰，但是冗余。
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
         for(int i = 0; i < flowerbed.length && n >0;){
             if(flowerbed[i] == 1){
                 i += 2;
             }
             else if((flowerbed[i] ==0 && i == flowerbed.length-1) || (flowerbed[i] ==0 && flowerbed[i+1] == 0)){
                 n--;
                 i += 2;     
             }
             else if(flowerbed[i] ==0 && flowerbed[i+1] == 1){
                 i +=3;
             }
         }
        return n <= 0;
    }
}
```
```java
class Solution {//这个省略了点。
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
         for(int i = 0; i < flowerbed.length && n >0;){
             if(flowerbed[i] == 1){
                 i += 2;
             }
             else if(i == flowerbed.length-1 ||  flowerbed[i+1] == 0){//因为你的flowerbed[i]不是1就是0，上一步已经判断好了是不是1，所以如果不是1的话自然跳到这里。
                 n--;
                 i += 2;     
             }
             else {
                 i += 3;
             }
         }
        return n <= 0;
    }
}
```
## 452 用最少数量的箭引爆气球 medium(和435相似)
这个题我的思路是，首先还是对尾巴进行升序排列，然后赋值prev给第一个数的尾巴，开始进行for循环比较第二个数，如果prev大于第二个数的头，也就是这个箭还是可以穿过去，不需要考虑尾巴，因为题目说了头一定比尾巴小。然后如果prev比头小，就说明穿不过去了，这时候就箭的数目加1.以此类推，这个注意count初始值为1，因为本身至少都需要一支箭，可以试试只有一个区间，在循环内counnt是不增加的。这里和435比较：435是不重叠区间，而这里刚好是重叠区间。
```java
class Solution {//看了一遍435后自己写的，注意这里的升序有点和435的不一样，因为会有一个用例存在溢出问题，如果按照435的写。[[-2147483646,-2147483645],[2147483646,2147483647]]，sort后是[[2147483646,2147483647],[-2147483646,-2147483645]]，因为他们相减后会溢出，所以用到小于来比较。
    public int findMinArrowShots(int[][] points) {
        if(points.length == 0){
            return 0;
        }
        Arrays.sort(points,new Comparator<int []>(){
            public int compare(int[] points1, int[] points2){
                if(points1[1] < points2[1]){
                    return -1;
                }
                else return 1;
            }
        });
       
        int count = 1,prev = points[0][1];
        for(int i = 1; i < points.length; i++){
           if(points[i][0] > prev){
                count++;
                prev = points[i][1];
            }           
        }
        return count;
    }
}

/*这个for循环就比较详细，但是没必要，增加运行的时间。
    for(int i = 1; i < points.length; i++){
            if(points[i][0] < prev){
                continue;
            }
            else if(points[i][0] > prev){
                count++;
                System.out.println(count);
                prev = points[i][1];
            }           
        }
*/
```
## 763 划分字母区间 medium
自己想的思路比较复杂，太冗余，而且可能考虑的东西不够全面。官方思路:首先用一个长度为26的数组a把每个字母的最后一个位置进行标记。设置start和end，开始循环字符串，访问每个字母，通过之前a来获取他的最后一个位置endc，令end=max（end，endc）。如果循环到i等于end，就说明之前的字母都包括在这个区间内，那就让长度写入partitio，并令start=end+1。
```java
//知识点总结，
List<Integer> partition = new ArrayList<Integer>();
/*
List是一个接口
<>表示了List里面放的对象是什么类型的，上面List里面放的必须是Integer类型的

ArrayList类是一个特殊的数组–动态数组。通过添加和删除元素，就可以动态改变数组的长度。
优点：
1、支持自动改变大小 2、可以灵活的插入元素 3、可以灵活的删除元素
局限：
比一般的数组的速度慢一些；
可以调用 List接口里面的内置函数,add,get等方法
*/
last[s.charAt(i) - 'a'] = i;
/*
String s = "www.runoob.com";
char result = s.charAt(6);
输出为n
所以本题这样的做法，可以把每个字母的最后一个位置记录下来
*/
```
```java
class Solution {
    public List<Integer> partitionLabels(String s) {
          int start = 0, end = 0;
          int[] last =new int[26];
          List<Integer> partition = new ArrayList<Integer>();
          for(int i = 0; i < s.length(); i++){
              last[s.charAt(i) - 'a'] = i;
          }
          for(int i = 0; i < s.length(); i++){
              end = Math.max(end, last[s.charAt(i) - 'a']);
              if(i == end){
                  partition.add(end - start + 1);
                  start = end + 1;
              }
          }
          return partition;
    }
}
```
## 122 买卖股票的最佳时机2 easy(可动态规划)
还是没有独立想出来，想得太复杂，一直纠结怎么用区间来解答。官方解答太多数学公式，总体来说就是只要选择贡献大于0的区间，然后一直累加利润，但是这个做法是不知道第几次买卖的，只能求利润，对于负数，则和0比较就行。秒呀！
```java
class Solution {
    public int maxProfit(int[] prices) {
        int price = 0;
        for(int i = 1; i < prices.length; i++){
            price += Math.max(0, prices[i] - prices[i-1]);
        }
    return price;
    }
}
```
## 406 根据身高重建队列 medium
[网友思路](https://leetcode-cn.com/problems/queue-reconstruction-by-height/solution/xian-pai-xu-zai-cha-dui-dong-hua-yan-shi-suan-fa-g/)：首先遇到这种数对问题，先排序。根据第一个元素正向排序，根据第二个元素反向排序，或者根据第一个元素反向排序，根据第二个元素正向排序，往往能够简化解题过程。在本题目中，先对数对进行排序，按照数对的元素 1 降序排序，按照数对的元素 2 升序排序。原因是，按照元素 1 进行降序排序，对于每个元素，在其之前的元素的个数，就是大于等于他的元素的数量，而按照第二个元素正向排序，我们希望 k 大的尽量在后面，减少插入操作的次数。小陈补充：如果第一个位置降序，第二个位置也降序排，再按照这样写法去插入的话，有部分用例是不能通过的，比如a[[7,0],[6,1],[5,2]]下一个待插入的数是[5,0]，按照算法应该插入到第一个位置，变成a[[5,0],[7,0],[6,1],[5,2]]这时候我们就发现[5,2]已经错误了，因为前面有三个数大于或者等于了。也就是你插入后，你得保证后面没有等于你的数插入，所以，第二个位置的排序，要升序！！！保证同胞小弟位置(第二个位置)先安排好。我们可以这样缕清楚，a里面已经插入的数都是比后面待插入的数大或者相等，如果后面待插入的数第二个位置比a的长度小，那么就是说他来选位置插，反之，他就插到最后面。
```java
//知识点
//这个题可以学到compare对两个位置进行排序的写法，具体看下面的答案这里不多说
 List<int[]> list = new LinkedList<>();
/*
这个的话是双向链表，比创建数组a更加方便，创建数组的话，当你的情况是a的长度大小大于people[i][1]时，你是要插入到a的people[i][1]的位置，这时候你需要进行移动a[people[i][1]]以及后面的每个数据1位，然后才能插进去，这样麻烦。
最后转成list.toArray(new int[list.size()][]);返回即可。

可以对比763的
List<Integer> partition = new ArrayList<Integer>();

*/
```
```java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people, new Comparator<int []>(){
            public int compare(int[] people1, int[] people2){
                if(people1[0] != people2[0]){
                    return people2[0] - people1[0];
                }
                else{
                    return people1[1] - people2[1];
                }
            }
        });
        List<int[]> list = new LinkedList<>();
        for(int i = 0; i < people.length; i++){
            if(list.size() > people[i][1]){
                list.add(people[i][1],people[i]);
            }
            else{
                list.add(list.size(),people[i]);
            }
        }
        return list.toArray(new int[list.size()][]);
    }
}
```
## 665 非递减数列 easy
第一次看题看错了，看成只移动一个数。这个题是改变一个数！虽然是easy，但是并不一定easy。[网友解答很清晰](https://leetcode-cn.com/problems/non-decreasing-array/solution/yi-ding-yao-rang-ni-nong-dong-wei-shi-ya-u9te/)：本题是要维持一个非递减的数列，所以遇到递减的情况时（nums[i] > nums[i + 1]），要么将前面的元素缩小，要么将后面的元素放大。但是本题唯一的易错点就在这，如果将nums[i]缩小，可能会导致其无法融入前面已经遍历过的非递减子数列；如果将nums[i + 1]放大，可能会导致其后续的继续出现递减；所以要采取贪心的策略，在遍历时，每次需要看连续的三个元素，也就是瞻前顾后，遵循以下两个原则：需要尽可能不放大nums[i + 1]，这样会让后续非递减更困难；如果缩小nums[i]，但不破坏前面的子序列的非递减性；算法步骤:遍历数组，如果遇到递减：还能修改：修改方案1：将nums[i]缩小至nums[i + 1]；修改方案2：将nums[i + 1]放大至nums[i]；不能修改了：直接返回false；
```java
class Solution {//只能修改一次，让数组递增。
    public boolean checkPossibility(int[] nums) {
      if(nums.length == 1){
          return true;
      }
      boolean flag = nums[0] <= nums[1] ? true : false;//一开始第一个数小于第二个数，则拥有一次修改的机会。
      for(int i = 1; i < nums.length-1 ; i++){
          if(nums[i] > nums[i+1]){//开始出现递减情况。i大于后面一个数了
              if(flag){//如果拥有修改机会
                  if(nums[i+1] >= nums[i-1]){//如果i的后面一个数比i的前面一个数大的话，就说明他们是递增，让i缩小的话，也没有破坏非递减性，并且不影响i+1后面的序列。
                      nums[i] = nums[i+1];//这时候就让i缩小
                  }
                  else{
                      nums[i+1] = nums[i];//这个情况就是i后面的一个数比i前面的一个数小，但是同时i后面的数还小于i，所以只能让i后面的数扩大为i。
                  }
                  flag = false;//用掉了唯一的一次修改机会了
               }
               else {
                 return false;//再出现递减情况，但是已经没有修改机会了，直接返回false。
               }
           }
       }
        return true;//若nums(0) > nums(1)的话，flag是false，且后面没有出现递减，所以已经是可以用修改一次来递增，也就是把第一个数变成第二个数就满足，所以直接返回true。
    }
}
```
# 双指针
## 167 两数之和2 easy
注意题目给的数组是非递减顺序排列（也就是总体递增，然后可能有两个相邻的数是相等），所以思路上很简单，两个变量去追踪这个数组，一头一尾巴，如果两数之和小于target，左边就需要移动一位，反之则右边需要移动一位。
```java
class Solution {//一开始根据书上思路写的，超时了~~~可能是暴力解法的原因，而且这个代码尚未验证是否正确。
    public int[] twoSum(int[] numbers, int target) {
        int j = numbers.length-1;
        int[] ans = new int[2];
        for(int i = 0; i < numbers.length-1 && i < j;){
           if(numbers[i] + numbers[j] == target)
           {
               ans[0] = i+1;
               ans[1] = j+1;
              
           }
           else if(numbers[i] + numbers[j] < target){
               i++;
           }
           else if(numbers[i] + numbers[j] > target){
               j--;
           }
        }
        return ans;
    }
}

```
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int i = 0;
        int j = numbers.length-1;
        //int[] ans = new int[2];
        while(i < j){
            int  sum = numbers[i] + numbers[j];
            if(sum == target){
                break;
            }
            else if(sum < target){
                i++;
            }
            else if(sum > target){
                j--;
            }
        }
        return new int[]{i + 1, j + 1};//这样就不用先去定义一个数组了。
    }
}
```
## 88 合并两个有序数组 easy
题目给的是两个非递减数组，思路是用m,n来指向两个数组的尾巴，还有pos来定位。首先要注意是在数组1的基础上去排，不需要额外开辟一个数组。pos定位在数组1的尾巴，开始对比两个数组的尾巴，哪个大就先复制过去。这里最后要注意，如果数组1复制完了，但是数组2还有，务必要记得继续复制。反之如果数组2复制完了，则不需要操作，因为数组1本身就是非递减，而且返回的数组就是他自己。
```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
         int pos = m-- + n-- -1;//这样写的话就少了一步，别忘了数组大小和数组位置是相差1.
         while(m >= 0 && n >= 0){
             nums1[pos--] = nums1[m] > nums2[n] ? nums1[m--] :nums2[n--];//注意是哪个大才会自减减哦。不是每次都自减减。
         }
         while(n >= 0){
             nums1[pos--] = nums2[n--];//务必不要忘记如果数组2还没复制完这个事！！！！！此时的数组1已经复制完啦！！！
         }
    }
}
```
## 142 环形链表2 medium
这个题涉及了一些数学计算，感谢[网友](https://leetcode-cn.com/problems/linked-list-cycle-ii/solution/linked-list-cycle-ii-kuai-man-zhi-zhen-shuang-zhi-/)讲解，这里复述一下：设置两个指针，一个为fast，一个为slow，fast每次走2步，slow每次走1步，设链表为a+b个节点，a为抵达环状的步数，b为环状的节点数。没有环状的链表很容易考虑，这里直接讲有环状的情况，也就是fast和slow会相遇：首先可以得到第一个关系式f=2s，这个是slow和fast的步数关系。第二个关系式是f=s+nb，因为fast比slow快，所以最终一定能追上，这时候呢，其实fast比slow多走了n个环。根据这两个关系，可以得到<mark>f=2nb</mark>,<mark>s=nb</mark>。接下来我们考虑，一个指针从头走到环状开头走过的步数<mark>k=a+nb</mark>，当n为0，也就是你走了a步到了环状的门口，然后n=1的话，你相当于绕了一圈环，然后又到了门口。现在有了三个表达式，从head结点走到入环点需要走:a + nb， 而slow已经走了nb（之前推了相遇的时候他们两个的关系），那么slow再走a步就是入环点了,如何知道slow刚好走了a步？fast从新从head开始和slow指针一起走，再相遇时刚好就是a步。
```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head, slow = head;
        while(true){
            if(fast == null || fast.next == null){//注意别忘了是两个条件
                return null;
            }
            fast = fast.next.next;
            slow = slow.next;
            if(fast == slow){
                break;
            }
        }
        fast = head;
        while(slow != fast){
            fast = fast.next;
            slow = slow.next;
        }
        return fast;
        
    }
}
<<<<<<< HEAD
```
## 76 最小覆盖子串 hard
依旧是[网友](https://leetcode-cn.com/problems/minimum-window-substring/solution/tong-su-qie-xiang-xi-de-miao-shu-hua-dong-chuang-k/993501)的思路，这个题在于需要考虑不少东西，这里简单描述一下：首先是建立一个128的ASCII列表，第一步先记录t中每个字符的数量。定义好l和r初始位置，还有用count去记录还需要的字符数量，这样就不用每次去查看need中哪个字符还大于0。开始while循环，用r去遍历整个S串，经过每个r的位置去提取字符c，首先判断c在need中的情况，如果是大于0，说明这个字符是符合t串的。然后减去一个count，代表已经找到了一个符合的字符，接下来是need中减去字符c的数量，注意，这里也包括不在t中的字符，不在t的中字符减去自然是为负数，代表这个字符是多余的。然后判断count为0的情况，count为0，代表已经找到符合的子串了，但是题目要求的size是最小的，所以可以缩减范围，当l小于r，并且里面有多余的字符，我们首先在need中加回去，然后移动l，然后开始重置size的大小，注意这时候的start变成新的l。接来下是移动l，看看还有没有更小的窗口，注意这里用start去保存这个开始的位置，而不是直接用l，这是有含义的，因为你的r是要遍历整个S串，这样你才知道哪个窗口是最小的，所以只有当size更小时候，我们才去更新更新start值，再加上size大小，就可以找到最小的串位置。这时候你无需当心l和r移动的位置了。务必务必注意，移动l的时候，请记得更新need和count以及l！！！
```java
class Solution {
    public String minWindow(String s, String t) {
    int[] need = new int[128];
    for (int i = 0 ; i < t.length() ; i++){
        need[t.charAt(i)]++;
    }
    int l = 0, r = 0, start = 0, size = Integer.MAX_VALUE, count = t.length();
    while (r < s.length()){
        char c = s.charAt(r);
        if (need[c] > 0){
            count--;
        }
        need[c]--;
        if(count == 0){
           while(l < r && need[s.charAt(l)] < 0){
               need[s.charAt(l)]++;
               l++;
           }
           if (r -l + 1 < size){
               size = r - l + 1;
               start = l;
           } 
           need[s.charAt(l)]++;
           count++;
           l++;
       }
       r++;
       
    }
    return size == Integer.MAX_VALUE ? "" : s.substring(start, start + size);
}}
```
## 633 平方数之和 medium
和167的很像，这里[网友](https://leetcode-cn.com/problems/sum-of-square-numbers/solution/shuang-zhi-zhen-de-ben-zhi-er-wei-ju-zhe-ebn3/)非常详细说明为什么i++和j--不会错过答案。
```java
class Solution {
    public boolean judgeSquareSum(int c) {
    long i = 0,j =(long) Math.sqrt(c);//如果直接写成c，会超时。理论上也是应该对的。
    while(i <= j){
       long sum = i*i + j*j;
       if( sum == c ){
           return true;
       }
       else if( sum < c ){
           i++;
       }
       else {
           j--;
       }
    }
    return false;
    }
}

```
## 680 验证回文字符串2 easy
这个题虽然是简单题，但是还是看了[网友](https://leetcode-cn.com/problems/valid-palindrome-ii/solution/cong-liang-ce-xiang-zhong-jian-zhao-dao-bu-deng-de/403606)思路：用双指针去对比，一个在头l，一个在尾巴r，当遇到不相等的情况，我们可以让l加1个位置，或者让r减去一个位置，因为题目说了可以最多删除一个字符，然后再用一个函数去对比子字符串。这里我一开始想到的是用一个计数器去判断删除的次数，后来发现其实不需要，比如abxbgga，要删除两次才行，你只要仔细看代码，发现只要一次之后不行就直接false了，所以不用考虑加一个计数器的问题，那么删除一个字符是体现在r-1或者l+1上
```java
class Solution {
    public boolean validPalindrome(String s) {
    int l = 0, r = s.length()-1;
    while(l < r){
        if(s.charAt(l) != s.charAt(r)){
            return judegesub(s, l+1, r) || judegesub(s, l, r-1);
        }
        l++;
        r--;
    }
    return true;
    }
    public boolean judegesub(String s, int l, int r){
        while(l < r){
            if(s.charAt(l) != s.charAt(r)){
                return false;
            }
        l++;
        r--;
        }
        return true;
    }
}

```
## 524 通过删除字母匹配到字典里最长单词 medium
这个题我一开始考虑的是，首先跟上一个题的区别是，这个题的意思是可以删除好几个元素，然后第二个不同的是，这个题有多个词，是不是要用暴力算法一个个去看？看了官方解答后，发现被上一题绕进去了。大概重复下解法：用双指针思路，i和j分别指向t(字典中的词)和s的第一个字母，注意这里是每个字典的词都会遍历，然后如果匹配，则i和j同时移动一位，如果不匹配，i不动，j+1。直到最后i要是等于这个词的长度的话，就代表全部匹配到。注意这里是长度，长度和单词最后一个字符位置是相差1的。题目中说要长度最长和序号最低的。所以自然有一个长度对比以及序号对比，序号对比是用compareTo函数，这个是对比ASCII对比，也就是序号在前的话是小于0。
```java
//知识点
class Solution {
    public String findLongestWord(String s, List<String> dictionary) {
        for (String t : dictionary) {
          System.out.print(t);
          System.out.print(" ");
        }  
    }
}
//输入是["ale","apple","monkey","plea"]这里会依次输出ale apple monkey plea 

关于compareTo:
返回值是整型，它是先比较对应字符的大小(ASCII码顺序)如果第一个字符和参数的第一个字符不等，结束比较，返回他们之间的长度差值，如果第一个字符和参数的第一个字符相等，则以第二个字符和参数的第二个字符做比较，以此类推,直至比较的字符或被比较的字符有一方结束。

如果参数字符串等于此字符串，则返回值 0；
如果此字符串小于字符串参数，则返回一个小于 0 的值；
如果此字符串大于字符串参数，则返回一个大于 0 的值。
```
```java
class Solution {
    public String findLongestWord(String s, List<String> dictionary) {
        String res = "";
        for (String t : dictionary) {
            int i = 0, j = 0;
            while (i < t.length() && j < s.length()) {
                if (t.charAt(i) == s.charAt(j)) {
                    ++i;
                }
                ++j;//注意这里，如果匹配的话，j是在这里++，而不是在上面的if语句，因为这里还有一个就是如果不匹配的话，j也要++，而这时候i不变，所以这个写法可以同时满足两个条件
            }
            if (i == t.length()) {
                if (t.length() > res.length() || (t.length() == res.length() && t.compareTo(res) < 0)) {
                    res = t;
                }
            }
        }
        return res;
    }
}
```
# 二分查找  
## 69 Sqrt(x) easy
这个题是看了官方解法，其实思路就是二分法，每次寻找中间值，如果中间值的平方小于输入值，则把左边的边界设置为mid+1，反之如果大于输入值，则把右边界设置为mid-1，这里注意一个问题就是mid * mid前面要加long，不然超过范围。
```java
class Solution {
    public int mySqrt(int x) {
        int l = 0, r = x, ans = -1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if ((long) mid * mid <= x){
                ans = mid;
                l = mid + 1;
            }else {
                r = mid - 1;
            }
        }
    return ans;
    }
}
```
## 34 在排序数组中查找元素的第一个和最后一个位置 medium
这里有一个网友的[二分法模板](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/tu-jie-er-fen-zui-qing-xi-yi-dong-de-jia-ddvc/)，关于这个题，分了两步，首先是寻找第一个target，循环内部条件是num[mid]大于等于target，然后用模板1，寻找最后出现的target，用模板2。至于为什么分开模板1和2，有个网友解释很清楚：因为取左边第一个target时，当nums[mid]==target时，中间位置的右边元素一定不是target出现的第一个位置，所以下次搜索区间是[left,mid],right=mid;取最后一个target时，当nums[mid]==target时，中间位置的左边元素一定不是target出现的最后一个位置，所以下次搜索区间是[mid，right],left=mid。
```java
//模板1
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = (l + r)/2;
        if (check(mid)) r = mid;
        else l = mid + 1;
    }
    return l;
}

//模板2
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = ( l + r + 1 ) /2;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}

/*
总结归纳：当l=mid时，mid=(l+r+1)/2，当l=mid+1，mid=(l+r)/2，至于为什么请看网友解释，这个题要多次理解。
这里复制了网友的解释：什么时候用模板1，什么时候用模板2？
假设初始时我们的二分区间为[l,r]，每次二分缩小区间时，如果左边界l要更新为 l = mid，此时我们就要使用模板2，让 mid = (l + r + 1)/ 2，否则while会陷入死循环。如果左边界l更新为l = mid + 1,此时我们就使用模板1，让mid = (l + r)/2。因此，模板1和模板2本质上是根据代码来区分的，而不是应用场景。如果写完之后发现是l = mid，那么在计算mid时需要加上1，否则如果写完之后发现是l = mid + 1，那么在计算mid时不能加1。

*/
```
```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        if (nums.length == 0) return new int[]{-1, -1};//上来就要记住这个。
        int l = 0, r = nums.length - 1;
        while (l < r){
            int mid = (l + r) / 2;
            if (nums[mid] >= target) r = mid ;//比目标值大，则寻找左区间，右边变为mid，左边不动。
            else l = mid + 1;//比目标值小，寻找右区间，左边变成mid+1，右边不动。
        }
        if (nums[r] != target) return new int[]{-1,-1};//这个特别容易忘记的。
        int L = r;
        l = 0; r = nums.length - 1;
        while (l < r){
            int mid = (l + r + 1) / 2;
            if (nums[mid] <= target) l = mid ;//比目标值小，寻找右区间，左边变成mid，右边不变。
            else r = mid - 1;//比目标值大，寻找左区间，右边变成mid-1，左边不变。
        }
        return new int[]{L,r};

    }
}
```
## 81 搜索旋转排序数组 II medium
找数组的目标数，这个题就是说本来的数组是增序的，但是现在相当于在中间断开，然后再连起来，就变成一个旋转数组。所以，旋转数组的特性的有一部分是增序的。先依旧找到中点位置，后面解释看代码。这个[coder](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/solution/zai-javazhong-ji-bai-liao-100de-yong-hu-by-reedfan/)把一些情况得很清楚。
```java
/*
解题思路：
本题是需要使用二分查找，怎么分是关键，举个例子：

第一类
1011110111 和 1110111101 这种。此种情况下 nums[start] == nums[mid]，分不清到底是前面有序还是后面有序，此时 start++ 即可。相当于去掉一个重复的干扰项。
第二类
22 33 44 55 66 77 11 这种，也就是 nums[start] < nums[mid]。此例子中就是 2 < 5；
这种情况下，前半部分有序。因此如果 nums[start] <=target<nums[mid]，则在前半部分找，否则去后半部分找。
第三类
66 77 11 22 33 44 55 这种，也就是 nums[start] > nums[mid]。此例子中就是 6 > 2；
这种情况下，后半部分有序。因此如果 nums[mid] <target<=nums[end]。则在后半部分找，否则去前半部分找

*/
```
```java
class Solution {
    public boolean search(int[] nums, int target) {
        int start = 0, end = nums.length - 1;
        while (start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target){
                return true;
            }
            if (nums[start] == nums[mid]){//因为数组会存在重复数字，如果中点和左端相等，我们不能确定在左区间全部相等，还是右边区间全部相等，这种情况，可以把start右移动一个，当然这种操作我们不能说是完全的二分法，这个题只是部分地方用到二分法。
                start++;//无法判断哪个区间是增序的
            } else if (nums[mid] <= nums[end]) {//右区间是增序的
                if (target > nums[mid] && target <= nums[end]) {//然后在右区间内判断target，用二分法。target在mid和end中间，这里需要注意，target是要小于等于end。我觉得要这么去想，你想如果都等于mid，那肯定是直接返回true，所以有mid比较这边是开区间
                    start = mid + 1;//就让start移动到mid右边
                }else {
                    end = mid -1;//反之，让end移动到mid左边
                }
            } else {//这个情况则是左区间增序
                if (target < nums[mid] && target >= nums[start]) {//这里需要注意，target是要大于等于start
                    end = mid - 1;
                } else {
                    start = mid + 1;
                }
            }
        }
    return false;
    }
}
```
## 154 寻找旋转排序数组中的最小值 II hard
这个题也是旋转数组，和上个题的区别是，1.上个题是找target，这个题是找最小值。2.这个题旋转多次。[这个作者解释得不错](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/solution/154-find-minimum-in-rotated-sorted-array-ii-by-jyd/)，把作者思路拷贝到了下面了，注意一下这个题，旋转后每个数字的序号保持原来不变，也就是原来是0位置，旋转后序号还是0。
```java
/*
    -思路-
    旋转数组肯定将nums分为两个有序的数组，分为别nums1和nums2。并且假设nums1中的元素均大于等于nums2中的元素
    那么我们要求的元素就是nums2的首元素
    --步骤--
    如果nums[mid] > nums[right],说明此时的mid严格的在nums1当中。（这时候最小值在[mid,right]上？所以把区间缩到这来，我理解的），那么nums2的首元素设为i的话，就应当是：mid < i <= right。
    取left = mid + 1;附其他解释：当 nums[mid] > nums[right]时，mid一定在第 1 个排序数组中，i 一定满足 mid < i <= right，因此执行 left = mid + 1；


    如果nums[mid] < nums[right],说明此时的mid严格的在nums2当中。（这时候最小值在[left,mid]上？所以把区间缩到这来，我理解的）也就是：mid <= i < right
    取right = mid(注意这里没有mid-1)；附其他作者解释：当 nums[mid] < nums[right] 时，mid 一定在第 2 个排序数组中，i一定满足 left < i <= mid，因此执行 right = mid；

    如果nums[mid] == nums[right],细分为三种情况。
        情况一：[1,1,1,1,1,1,1,1]
        情况二：[4,5,6,7,1,1,1,1,1,1]
        情况二：[4,5,6,7,0,1,1,1,1,1]
    取right--便可，这个很关键！！！！

    另外一个作者解释了为什么这样：
    我们采用 right = right - 1 解决此问题，证明：
    此操作不会使数组越界：因为迭代条件保证了 right > left >= 0；
    此操作不会使最小值丢失：假设 nums[right]是最小值，有两种情况：
    若 nums[right]是唯一最小值：那就不可能满足判断条件 nums[mid] == nums[right]，因为 mid < right（left != right 且 mid = (left + right) // 2 向下取整）；
    若 nums[right]不是唯一最小值，由于 mid < right 而 nums[mid] == nums[right]，即还有最小值存在于 [left, right - 1][left,right−1] 区间，因此不会丢失最小值。

作者：jyd
链接：https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/solution/154-find-minimum-in-rotated-sorted-array-ii-by-jyd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
*/
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = (left + right) / 2;
            if (nums[mid] == nums[right]) {
                right--;
            } else if (nums[mid] < nums[right]) {
                right = mid;
            } else if (nums[mid] > nums[right]) {
                left = mid + 1;
            }
        }
    return nums[right]; //貌似nums[left]也是可以的。其实看了下，最后left=mid+1也就是等于right，所以这样也就是两种写法都是一样。
    }
}
```
## 540. 有序数组中的单一元素 meidum  
这个题是找到唯一的单身狗，注意题目是升序的，不过貌似与升序没关系。这里先整理下官方的清晰解答：因为这个模块是讲二分法，所以不讲解暴力算法。
```java
/*
首先要知道，这个数组一定是奇数个，因为只有一个单身狗！
用 halvesAreEven = (right - mid) % 2 == 0来判断哪一侧元素为奇数，因为单身狗肯定在这一侧。
情况1：mid 和 mid+1是同元素。然后mid元素把两侧都分为偶数个，当我们把mid+1拿掉后，右侧就变成奇数个了，也就是右侧肯定存在单身狗，设置left = mid + 2
情况2；mid 和 mid+1是同元素。然后mid元素把两侧都分为奇数个，当我们把mid+1拿掉后，右侧是偶数个，所以左侧存在单身狗，设置right = mid - 1 
情况3：mid 和 mid-1是同元素。然后mid元素把两侧都分为偶数个，当我们把mid-1拿掉后，左侧就是奇数个了，所以左侧存在单身狗，设置right = mid - 2
情况4：mid 和 mid-1是同元素。然后mid元素把两侧都分为奇数个，当我们把mid-1拿掉后，右侧就存在单身狗，所以left = mid + 1
首先判断mid和左边还是右边的元素相等，然后通过halvesAreEven判断左右两侧是奇还是偶。
这里只举例情况1和2，因为这是放在一起的。现在已经知道了mid和mid+1相等，然后再想，如果mid把两侧分为了偶数个(单纯讲个数，与数组位置无关)，也就是mid的位置是一个偶数(数组从0计算)，然后整个数组肯定是奇数个，也就是最后一个数的位置也是偶数，所以当halvesAreEven为true，就说明符合情况1，反之符合情况2。
注意写代码的时候别忘了mid就是单身狗的情况。

下面这个方法也是二分，但是仅对偶数索引进行二分搜索，比上面的方法简洁，而且不用单独考虑mid是单身狗的情况，因为else就是包含了mid是单身狗或者左侧存在单身狗。
然后就是简洁点，整理下上面的思路，我们确保mid是偶数的位置，如果是奇数就减去1，然后检查 mid 的元素是否与其后面的索引相同。如果相同，则我们知道 mid 不是单个元素。
且单个元素在 mid 之后。则我们将 left 设置为 mid + 2。
如果不是，则我们知道单个元素位于 mid，或者在 mid 之前。我们将 right 设置为 mid。
一旦 left == right，则当前搜索空间为 1 个元素，那么该元素为单个元素，我们将返回它。
*/
```
```java
class Solution {
/*
知识点：int mid = left + (right - left) / 2;
为什么前面还要加left?因为防止超出整型数据溢出。可以看看前面的题有没有这种情况！！
*/
    public int singleNonDuplicate(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (mid % 2 == 1 ) mid--;
            if (nums[mid] == nums[mid+1]) {
                left = mid + 2;
            } else {
                right = mid;
            }
        }
    return nums[right];//left也可以，第一次写的时候写成了mid，先不说结果对不对，这里语法就存在问题，因为mid在while里面，所以系统会检测不到mid，是一个局部变量。
    }
}
```
## 4 寻找两个正序数组的中位数 hard
这个题虽然看上去是可以合并起来去找，但是，由于有时间复杂度的要求，所以用二分法比较好，坦白说，确实hard。详细解释[点击这里看解法三](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/)。
```java
//用的代码是官方的，但是上面说的解释更清楚。
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int length1 = nums1.length, length2 = nums2.length;
        int totallength = length1 + length2;
        if (totallength % 2 == 1){//两个数组长度和为奇数情况
           int midIndex = totallength / 2;//注意totallength是个数，而不是数组中的序号(从0开始)。
           double median = getKthElement(nums1, nums2, midIndex + 1);
           return median;
        } else {//两个数组长度合为偶数情况
            int midIndex1 = totallength / 2 -1, midIndex2 =totallength / 2;
            double median = (getKthElement(nums1, nums2, midIndex1 + 1) + getKthElement(nums1, nums2, midIndex2 + 1)) / 2.0;
            return median;
        }

    }
    public int getKthElement(int[] nums1, int[] nums2, int k) {
        //下面是官方写的
        /* 主要思路：要找到第 k (k>1) 小的元素，那么就取 pivot1 = nums1[k/2-1] 和 pivot2 = nums2[k/2-1] 进行比较
         * 这里的 "/" 表示整除
         * nums1 中小于等于 pivot1 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个
         * nums2 中小于等于 pivot2 的元素有 nums2[0 .. k/2-2] 共计 k/2-1 个
         * 取 pivot = min(pivot1, pivot2)，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个
         * 这样 pivot 本身最大也只能是第 k-1 小的元素
         * 如果 pivot = pivot1，那么 nums1[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums1 数组
         * 如果 pivot = pivot2，那么 nums2[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums2 数组
         * 由于我们 "删除" 了一些元素（这些元素都比第 k 小的元素要小），因此需要修改 k 的值，减去删除的数的个数
         */

        int length1 = nums1.length, length2 = nums2.length;
        int index1 = 0, index2 = 0;
        int kthElement = 0;

        while (true) {//边界情况
            if (index1 == length1) {
                return nums2[index2 + k - 1];
            }
            if (index2 == length2) {
                return nums1[index1 + k - 1];
            }
            if (k == 1) {//当k剩下一个的时候，也就是比较剩下哪个数谁比较小。
                return Math.min(nums1[index1], nums2[index2]);
            }

            //正常情况 
            int half = k / 2;
            int newIndex1 = Math.min(index1 + half, length1) - 1;// k/2-1
            int newIndex2 = Math.min(index2 + half, length2) - 1;// k/2-1
            int pivot1 = nums1[newIndex1] ,pivot2 = nums2[newIndex2];
            if (pivot1 <= pivot2) {
                k = k -(newIndex1 - index1 + 1);
                index1 = newIndex1 + 1;
            } else {
                k = k -(newIndex2 - index2 + 1);//加1是因为要去掉pivot这个元素
                index2 = newIndex2 + 1;
            }
        }//这个是while语句的结束
    }
}
```
```java
//由于这个题实在是费脑，这里详细举例子，按照代码思路一步步来
nums1:1 3 4 9
nums2:1 2 3 4 5 6 7 8 9
开始计算： 
length1=4,length2=9
totallength=13
判断为奇数，midIndex=6 传入到子函数(nums1，nums2,6+1)//也就是第7个数是中位数

下面是子函数循环情况

下面说的 排 是指两个数组合并起来，从小到大排第几个的意思
length1=4,length2=9
正常情况
第一轮while
half=7/2=3
newIndex1=3-1=2,newIndex2=3-1=2//比较nums1[k/2-1],nums2[k/2-1]的元素，本题也就是nums1[2],nums2[2]

[pivot]元素
1 3 [4] 9
1 2 [3] 4 5 6 7 8 9    可以看出nums2[2]的更小，把nums2[2]及其前面元素全部去掉，然后更新index2和k

这里需要理解为什么更新k和index2，我们要找的数是排序第七个(不是从0计算)的数，然后分别比较两个数组的nums[k/2-1]
nums1 中小于等于 pivot1 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个，本题也就是2个
nums2 中小于等于 pivot2 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个，本题也就是2个
然后取两个数组中比较小的pivot，本题是num2[2],可以推导，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个，也就是全部元素合并后小于等于nums2[2]元素的不超过5个，如果按照等式左边是为4个，因为是整除，如果按照等式右边就直接是5个。
这样的话，即便取pivot本身最大也只能是第 k-1 小的元素，也就是6，但是按照上一行的分析，pivot元素是排第5或者第6，本题的话实际是排第5。
总之还不是第七个我们要找的元素。那么可以完全排除nums[2]和左边的元素，这时候就要更新k和index2
k更新：因为本身要找第7个元素，现在已经排除了3个元素了，所以k=7-3=4，也就是在剩下的数组中找排第四个的元素，具体写法是7-（2-0+1)=4
index2更新： index2=2+1=3，也就是从nums2[3]开始
index1依旧为0

第二轮while   
half=4/2=2
newIndex1=0+2-1=1,newIndex2=3+2-1=4
下面标记|，代表左边的元素也就消除。
1 [3] 4 9
1 2 3 | 4 [5] 6 7 8 9
经过比较后，nums1[1]及其左边消除
k更新：因为本身要找第4个元素，现在已经排除了2个元素了，所以k=4-2=2，也就是在剩下的数组中找排第二个的元素，具体写法是4-（1-0+1)=2
index1更新： index1=1+1=2，也就是从nums1[2]开始
index2依旧为3

第三轮while
half=2/2=1
newIndex1=2+1-1=2,newIndex2=3+1-1=3
1 3 | [4] 9
1 2 3 | [4] 5 6 7 8 9
这里pivot元素相等，我们就假设上面的4大于下面的4，由于两个数相等，所以我们无论去掉哪个数组中的都行，因为去掉 1 个总会保留 1 个的，所以没有影响。
经过比较后，nums2[3]及其左边消除。
k更新：因为本身要找第2个元素，现在已经排除了1个元素了，所以k=2-1=1，也就是在剩下的数组中找排第二个的元素，具体写法是2-（0-0+1)=1
index2更新： index2=3+1=4，也就是从nums2[4]开始
index1依旧为2

第四轮while
k已经等于1了，直接找剩下比较小的数就行。
1 3 | [4] 9
1 2 3 4 | [5] 6 7 8 9
答案就是4


当然还有注意边界的情况，本题没有涉及。
所谓边界的问题,也就是有可能其中一个数组过小，然后进行更新的时候会发现越界，这时候也就是这个小的数组数组全部已经小于第K个数据，然后我们之后关注大的数组找到k就行。
```
# 常用排序算法
![排序算法](/images/leetcode-java/allsort.jpg)
## 快速排序
```java
//本代码是可执行代码，后面的排序算法可以直接用在本模板调用。
//快速排序就是每次把第一个数选为枢轴元素，然后左右扫描，右边扫描比它小的交换，左边扫描比它大的交换，最后放到正确的位置，最终左边的元素都比它小，右边的元素都比它大，然后左右递归。
import java.util.Arrays;
public class sort {
    public static void main(String[] args) {
        int arr[] = {6,7,8,1,2,3,9,10,0,4,6,8,7,99,77,44};//初始化数组时用new与不用new是没区别的
        int temp[] = new int[arr.length];
        int size = arr.length;
        quick_sort(arr, 0, size);//实现快速排序
        //insertion_sort(arr,size);//实现插入排序
        //merge_sort(arr, 0, size, temp);//实现归并排序
        //bubble_sort(arr, 0, size);//实现冒泡排序
        //select_sort(arr,size);实现选择排序
        System.out.println(Arrays.toString(arr));//输出结果
    }
    public static void quick_sort(int[] arr,int left, int right) {//左闭右闭区间，也就是right一开始输入的是size
        if (left + 1 >= right) {
            return;
        }
        int first = left, last = right - 1, key = arr[first];
        while (first <last) {
            while (first < last && arr[last] >= key) {//右边扫描
                --last;
            }
            arr[first] = arr[last];
            while (first < last && arr[first] <= key) {//左边扫描
                ++first;
            }
            arr[last] = arr[first];
        }
        arr[first] = key;//把枢轴元素放到正确的位置
        quick_sort(arr, left, first);//左边递归
        quick_sort(arr, first + 1, right);//右边递归
    }

```
## 插入排序
```java
//从第二个数开始，只要比前面小就一直交换。这样每轮的前i个数都是从小到大排好序，就好像插队一样，一直插。
public static void insertion_sort(int[] arr, int size){
    for (int i = 0; i < size; i++) {
        for (int j = i; j > 0 && arr[j] < arr[j - 1]; j--) {//别忘了防止越界的问题，j>0。
            int temp = arr[j-1];
            arr[j-1] = arr[j];
            arr[j] = temp;
        }
    }
}
```
## 选择排序
```java
//每次把未排序中最小的数选出来，然后和前面未排序第一个数交换。
public static void select_sort(int[] arr,int right) {
    int small;
    for (int i = 0; i < right - 1; i++) {//这里写成right也是没有问题的，但是right的话理论上来说多做了一步无用功
        small = i;
        for (int j = i + 1; j < right; j++) {
            if (arr[j] < arr[small]) {
                int temp = arr[j];
                arr[j] = arr[small];
                arr[small] = temp;
            }
        }
    }
}
```
## 归并排序
```C++
/*
归并排序采用了分治的思想。将数字分成许多小块，每块排序，然后再把块逐步合并起来。
重点：最终合并的时候需要一个临时数组来存储合并数据。合并的时候左右两边是两个排好序的数组，现在要把它们组合起来。关键点就在于判断每一次放入临时数组的是左侧还是右侧的数据。
first和second代表左右侧
如果左侧比右侧小，而且此时两个数组都没越界，左侧的数输入到temp。
如果左侧越界，那么右侧读入。
如果右侧越界，那么左侧读入。
综合起来，读入左侧数据的条件即为右侧越界或者左侧没越界且左侧比右侧小。
最后再改变nums
*/
void merge_sort(vector<int> &nums, int left, int right, vector<int> &temp)
{//C++版本
    if (left + 1 >= right) {
       return;
    }
    int mid = left + (right - left) / 2;//寻找中间数的位置

    merge_sort(nums, left, mid, temp);//左侧递归
    merge_sort(nums, mid, right, temp);//右侧递归

   int first = left, second = mid, i = left;
   while (first < mid || second < right) {   //first是左侧边界开始，second是右侧边界开始
    if (second >= r || (nums[first] <= nums[second] && first < mid)) {//右侧越界或者是 左侧没有越界，并且左侧的first位置比右侧的second位置小
        temp[i++] = nums[first++];//那就左侧写入temp
    } else {
        temp[i++] = nums[second++];
    }
   }
   for (i = l; i < r; ++i) {
        nums[i] = temp[i];
   }
}
```
```java
//java
public static void merge_sort(int[] arr,int left, int right, int[] temp) {//左闭右开区间哦，所以right进来是size
    if (left + 1 >= right) {
        return;
    }
    int mid = left + (right - left) / 2;
    merge_sort(arr, left, mid, temp);//别忘了左闭右开区间
    merge_sort(arr, mid, right, temp);
    int first = left, second = mid, i = left;
    while (first < mid || second < right) {
        if (second >= right || (arr[first] <= arr[second] && first < mid)) {
            temp[i++] = arr[first++];
        } else {
            temp[i++] = arr[second++];
        }
    }
    for (i = left; i < right; i++) {
        arr[i] = temp[i];
    }
}
```
下面画图理解递归是怎么操作的。
![归并排序](/images/leetcode-java/mergesort.jpg)
## 冒泡排序
```java
public static void bubble_sort(int[] arr,int right) {//每一轮把最大的一个数沉下去，下一轮就可以不用比较前一轮最后一个数
    for (int i = 1; i < right; i++) {//这个是从1开始
        for (int j = 1; j < right - i + 1; j++) {//不用再比较前一轮的最后一个数，size-i+1
            if (arr[j] < arr[j - 1]) {//因为和前一个比较，所以一开始的i起始位置是1
                int temp = arr[j];
                arr[j] = arr[j - 1];
                arr[j - 1] = temp;
            }
        }
    }
}
```
## 215 数组中的第K个最大元素 medium
这个题结合1738来看。思路：寻找第K个大的元素，可以用快速排序法，快速排序就是每次选择一个枢轴元素，然后比他小的在左边，比他大的在右边，最终可以确定枢轴元素的最终位置。对比这个位置和第K大的位置，如果比这个位置小，就在左边递归，反之右边递归。需要注意一个点，就是选择枢轴元素要随机选，不然会遇到极端案例，导致时间复杂度高。当然本题实际执行只考虑了比枢轴元素大的数以及把大的元素放在左边，是为了符合题目第k大的元素，注意本题解法也是一开始把枢轴元素放在最右边。
```java
//Math.random()*(n-m)+m，生成大于等于m小于n的随机数
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return quickSelect(nums, 0, k - 1, nums.length - 1);//第k大的位置也就是数组中k-1的位置
    }

    private int quickSelect(int[] arr, int left, int kth, int right) {
        int curPartition = partition(arr, left, right);
        if (curPartition == kth) {
            return arr[curPartition];
        } else if (curPartition < kth) {
            return quickSelect(arr, curPartition + 1, kth, right);
        } else {
            return quickSelect(arr, left, kth, curPartition - 1);
        }
    }

    private int partition(int[] arr, int left, int right) {
        int pivotIndex = left + (int)(Math.random() * (right - left + 1));//生成大于等于left小于等于right的随机数
        swap(arr, pivotIndex, right);//先把随机抽中的数与最右边的数交换。
        int index = left - 1;//把index初始化，一开始应该是-1。
        for (int i = left; i < right; i++) {//在区间范围内寻找
            if (arr[i] >= arr[right]) {//因为这个题说找最大的第k个数，所以只需要关注比这个枢轴元素大的数
                index += 1;//因为index一开始是-1
                swap(arr, index, i);//出现大于枢轴元素的数，就从左到右开始放。
            }
        }
        index += 1;//index加1，是为了下一步操作，把枢轴元素放到正确的位置
        swap(arr, index, right);//结束这个步骤，枢轴元素左边都是大于它的数，右边都是小于它的数
        return index;//返回随机抽中枢轴元素的位置
    }

    private void swap(int[] arr, int l, int r) {
        int temp = arr[l];
        arr[l] = arr[r];
        arr[r] = temp;
    }
}

/*写法2，自我感觉这种写法更好看
    private int partition(int[] arr, int left, int right) {
        int pivotIndex = left + (int)(Math.random() * (right - left + 1));
        swap(arr, pivotIndex, right);
        int index = left;//这里改变了
        for (int i = left; i < right; i++) {
            if (arr[i] >= arr[right]) {
                swap(arr, index, i);
                index += 1;//这里改变了
            }
        }
        //这里删除了
        swap(arr, index, right);
        return index;
    }
*/

```
```java
//这个版本就是我们平时理解的快速排序。但是这个版本每次跑起来的速度和占用内存都不如上面那个好。
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return quickSelect(nums, 0, k - 1, nums.length - 1);//第k大的位置也就是数组中k-1的位置
    }

    private int quickSelect(int[] arr, int left, int kth, int right) {
        int curPartition = partition(arr, left, right);
        if (curPartition == kth) {
            return arr[curPartition];
        } else if (curPartition < kth) {
            return quickSelect(arr, curPartition + 1, kth, right);
        } else {
            return quickSelect(arr, left, kth, curPartition - 1);
        }
    }

    private int partition(int[] nums, int left, int right) {
        int pivotIndex = left + (int)(Math.random() * (right - left + 1));//生成大于等于left小于等于right的随机数
        swap(nums, pivotIndex, left);//先把随机抽中的数与最右边的数交换。
        int first = left, last = right, key = nums[first];//注意这里是last = right ，而不是right-1，因为上面已经处理好边界是lenght-1
        while (first < last) {
            while (first < last && nums[last] <= key) {//这里是从右边扫描，小于的不管
                --last;
            }
            nums[first] = nums[last];//大于的就把这个数放到前面，这样可以符合题目第K大的条件
            while (first < last && nums[first] >= key) {
                ++first;
            }
            nums[last] = nums[first];
        }
        nums[first] = key;
        return first;}

    private void swap(int[] arr, int l, int r) {
        int temp = arr[l];
        arr[l] = arr[r];
        arr[r] = temp;
    }
}
```
## 347 前K个高频元素(桶排序) medium
首先用hash来创建一个key，value(频率)对应。然后再创建一个list，把相同频率的放在一个位置。最后从后往前面取出前k个来。也就是桶的思想。
```java
/*
知识点
1.LinkedHashMap继承于HashMap,是基于HashMap和双向链表实现的
2.HashMap无序，LinkedHashMap有序，分为插入顺序和访问顺序
3.访问顺序操作的时候，put和get操作已存在的Entry时，都会把Entry移动到双向链表的表尾(也就是先删除再插入)
4.LinkedHashMap存取数据，还是跟HashMap一样使用Entry的方式，双向链表只是为了保证顺序
5.LinkenHashMap线程是不安全的
部分操作：
LinkedHashMap<Integer,Integer> map = new LinkedHashMap<>();//创建map
map.put(key,value)//存入key和value
map.get(key)//取出value
map.keySet()//打印的话是输出key的顺序
map.getOrDefault(Object key, V defaultValue)方法的作用是：当Map集合中有这个key时，就使用这个key值；如果没有就使用默认值defaultValue。

1.数组也就是Array（[]）：最高效；但是其容量固定且无法动态改变；使用时候 new和不new没有区别
2.ArrayList：容量可动态增长；但牺牲效率；详细见https://www.runoob.com/java/java-arraylist.html
*/
```
```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        LinkedHashMap<Integer,Integer> map = new LinkedHashMap<>();//以key,value来保存，本题key就是数字本身，value就是频率
        for (int i = 0; i < nums.length; i++) {//创建一个map来对应数字和频率
            if (map.containsKey(nums[i])) {//如果map中的key存在value
                map.put(nums[i], map.get(nums[i]) + 1);//注意不是map.get(nums[i] + 1)。从第二次开始就在基础频率上增加1
            } else {
                map.put(nums[i],1);//第一次大家都是空的，直接走这一步创建频率为1。
            }
        }
        List<Integer>[] ans=new List[nums.length + 1];//这个处理方式比较随意，会出现很多的null，至于多加1，举个例子把。[2,2]。这个2的频率是2。然后代码下面取的时候是ans[2(频率)]，但是我们知道下标如果是用length的话只有0,1。所以这个基础上加1。
        for(int num: map.keySet()){//把map中的key依次按顺序处理
            int i=map.get(num);//取出这个Key对应的value(频率)
            if(ans[i]==null){
                ans[i]=new ArrayList<>();//初始化，ans一开始本来就是空的嘛
            }
            ans[i].add(num);//同频率的key添加到同一个位置的ans中
        }
        int res[] = new int[k];//设置一个数组，长度为k
        int count = 0;//计数器
        for (int i = ans.length - 1; i >= 0 && count <k; i--) {//注意这里是ans.length，而不是nums.length。从后往前面操作，因为题目说是频率的前k个
            if (ans[i] != null) {//对ans中有数字的操作
                for (int j = 0; j < ans[i].size(); j++) {//然后取出这个位置中的ans
                    if (count < k) {
                        res[count++] = ans[i].get(j);//题目数据保证答案唯一，换句话说，数组中前 k 个高频元素的集合是唯一的.也就是如果题目要求k是2。但是频率最高的同时有3个数，这个是不成立的，也就不存在这个样例。所以每放进一个数，count就要增加1.
                    } else break;
                }
            }
        }
        return res;
    }
}
```
```java
//这个解法稍微改了一点点，可以避免在生成bucket是时候浪费空间，能省一点是一点，但是实际测试的时候大家好像差不多，可能没有出现极端情况。
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        LinkedHashMap<Integer,Integer> map = new LinkedHashMap<>();
        int frequency = 0;//频率计算
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(nums[i])) {
                map.put(nums[i], map.get(nums[i]) + 1);
                frequency = Math.max(map.get(nums[i]),frequency);//找到最高频率
            } else {
                map.put(nums[i],1);
                frequency = Math.max(map.get(nums[i]),frequency);//找到最高频率
            }
        }
        List<Integer>[] ans=new List[frequency + 1];//长度只需要频率+1.这样可以避免上面的解法，省空间！！！
        for(int num: map.keySet()){
            int i=map.get(num);
            if(ans[i]==null){
                ans[i]=new ArrayList<>();
            }
            ans[i].add(num);
        }
        int res[] = new int[k];
        int count = 0;//计数器
        for (int i = ans.length - 1; i >= 0 && count <k; i--) {
            if (ans[i] != null) {//对ans中有数字的操作
                for (int j = 0; j < ans[i].size(); j++) {
                    if (count < k) {
                        res[count++] = ans[i].get(j);
                    } else break;
                }
            }
        }
        return res;
    }
}
```
## 451 根据字符出现频率排序(桶排序) medium 
这个题主要和上一题对比的话，我觉得主要是一些语法上，比如对字符处理和上一题对数字的处理是不太一样的。
```java
class Solution {
    public String frequencySort(String s) {
        Map<Character,Integer> map = new HashMap<Character,Integer>();
        int maxfreq = 0;//找出最高的频率
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            int frequency = map.getOrDefault(c, 0) + 1;//有就在频率的基础上加1，没有就默认0。
            map.put(c, frequency);//放入hash中
            maxfreq = Math.max(maxfreq, frequency);//找到频率最大值
        }
        StringBuffer[] buckets = new StringBuffer[maxfreq + 1];
         for (int i = 0; i <= maxfreq; i++) {
            buckets[i] = new StringBuffer();
        }
        //Map.entrySet() 这个方法返回的是一个Set<Map.Entry<K,V>>,Map.Entry里有相应的getKey和getValue方法
        for (Map.Entry<Character, Integer> entry : map.entrySet()) {//这个写法和上一题的不同之处。上一题都是整数，比较好处理。这个是字符，这样处理比较方便。
            char c = entry.getKey();//注意是getkey
            int frequency = entry.getValue();//注意是getvalue
            buckets[frequency].append(c); 
        }
        StringBuffer sb =new StringBuffer();
        for (int i = maxfreq; i > 0; i--) {
            StringBuffer bucket = buckets[i];
            for (int j = 0; j < bucket.length(); j++) {
                for (int k = 0; k < i; k++) {
                    sb.append(bucket.charAt(j));
                }
            }             
        }
        return sb.toString();
    }
}
```
## 75 颜色分类 medium
直接插入排序，但是貌似速度和内存都不占优势？
```java
class Solution {
    public void sortColors(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i; j > 0 && nums[j] < nums[j-1]; j--) {
                int temp = nums[j];
                nums[j] = nums[j - 1];
                nums[j - 1] = temp;
            }
        }
    }
}
```
# 一切皆可搜索
## 695 岛屿的最大面积 medium
思路是深度优先遍历，分为主函数和辅助函数，主函数就是遍历每个点的位置，辅助函数就是dfs，设置好不满足的条件，满足条件的继续搜索。
```java
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        int ans = 0;//最大数量初始化为0
        for (int i = 0; i < grid.length; i++) {//表示一共有多少行（也就是每列的长度，可以理解为Y，注意就是YYYYY）
            for (int j = 0; j < grid[0].length; j++) { //表示一共有多少列（也就是每行的长度，可以理解为X，注意就是XXXXX）
                ans = Math.max(ans, dfs(grid, i, j));//比较目前的岛屿是否最大，若不是，就替换为最大岛屿
            }
        }
    return ans;
    }
    public int dfs(int[][] grid, int cur_i, int cur_j) {
        //这个非常重要，是深度优先搜索的不满足条件。有超过边界的(i和j小于0；i等于列长度，j等于行长度，注意数组的开始位置是0哦；还有当前是海洋，也就是不是陆地的地方就不搜索)
        if (cur_i < 0 || cur_j < 0 || cur_i == grid.length || cur_j == grid[0].length || grid[cur_i][cur_j] != 1) {
            return 0;
        }
        grid[cur_i][cur_j] = 0;//当上面的条件都跳过了，也就是我们找到了一个陆地，这时候把他置为0，表示我们已经搜索过这个陆地了，然后开始上下左右搜索，不然应该会死循环。
        //这样可以组成 （0,1）（1,0）（-1,0）（0,-1）四种情况。
        int[] index_i = {0, 0, 1, -1};
        int[] index_j = {1, -1, 0, 0};
        int ans = 1;//陆地的数量初始为1
        for (int index = 0; index < 4; index++) {//遍历4次，走4个方向
            int next_inedx_i = cur_i + index_i[index];
            int next_inedx_j = cur_j + index_j[index];
            ans += dfs(grid, next_inedx_i, next_inedx_j);//把连起来的陆地加起来 
        }
    return ans;
    }
}
/*对于二维数组解释下长度问题
grid.length = 8 (8个一维数组，表示有多少行，也可以表示为每列的长度)
grid[0].length = 13（1个数组中有13个数，表示有多少列，也可以表示为每行的长度）
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
[0,0,0,0,0,0,0,1,1,1,0,0,0],
[0,1,1,0,1,0,0,0,0,0,0,0,0],
[0,1,0,0,1,1,0,0,1,0,1,0,0],
[0,1,0,0,1,1,0,0,1,1,1,0,0],
[0,0,0,0,0,0,0,0,0,0,1,0,0],
[0,0,0,0,0,0,0,1,1,1,0,0,0],
[0,0,0,0,0,0,0,1,1,0,0,0,0]]
*/
```
## 547 省份数量 medium
做这个题的时候陷入到上一题的思维了，做题还是太少了！本题中有多少个二维数组中有多少个一维数组就代表多少个城市，每个一维数组里面的位置代表本城市(也就是i和j相同)或者其他城市(i和j不一样)，位置上为1代表有连接，也就是大家最后是属于一个省份的。
```java
class Solution {
/*
思路：设置一个visit数组来表示访问过的城市。
访问第一个点，因为自己本身都是有1的，把visit[0]置为1(表示这个城市已经被访问过了)，这时候就开始深度优先搜索与这个城市相连的城市，然后依次类推，一直没有找到为止，这样就算完成了一个省份的搜索。开始执行下一个visit为0的城市访问。
*/

class Solution {
    public int findCircleNum(int[][] isConnected) {
        int citys = isConnected.length;//表示一共有多少个
        boolean[] visited = new boolean[citys];//全部城市访问都置为0
        int sum = 0;//省份数量初始为0
        for (int i = 0; i < citys; i++) {//开始搜索啦
            if(!visited[i]) {//只针对没有被访问的城市进行搜索
                dfs(isConnected, visited, citys, i);
                sum++;//上面全部搜索完，就相当于找到一个省份
            }
        }
    return sum;
}
    public void dfs(int[][] isConnected,boolean[] visited, int citys, int i) {
        for (int j = 0; j < citys; j++) {//i是固定的，然后逐个位置搜索看是否有1
            if (!visited[j] && isConnected[i][j] == 1) {//满足条件是位置上为1，已经这个城市没有被搜索过
                visited[j] = true;//满足了上面条件，记得把这个城市置为1，表示已经搜索过了
                dfs(isConnected, visited, citys, j);//开始搜索与i相连的城市，注意这里最后是jjjj。
                //所以，只要说一个城市被搜索过了，这个城市所有的连接情况我们都找到了，也就是他们是属于一个省份的。
            }
        }
    }
}
```
=======
```
>>>>>>> 52288f2321ba9974432bb20ba0d8a8ebb5f312a6
