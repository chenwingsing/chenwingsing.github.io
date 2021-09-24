---
title: LeetCode-JAVA
date: 2021-09-20 10:48:01
tags: 
categories: "刷题记录"
---
按照[《Leetcode101-A Leetcode Gringding Guide》](https://github.com/changgyhub/leetcode_101)顺序记录
<!--more--> 
# 贪心算法  
## 445 分发饼干 easy
依次满足胃口最小的孩子。可以想到先排序，然后再去分配。注意当满足了孩子后,cookie再加一，因为一个饼干只能用一次。
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
        Arrays.fill(right, 1);
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
                 i +=3;
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
# 指针类问题