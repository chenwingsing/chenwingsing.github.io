---
title: 杭电OJ--基础题篇
date: 2020-03-23 21:00:29
tags:
categories: "刷题记录"
---
记录刷题时候的知识点，顺便看看多久能刷完，没什么知识点就不记了，部分笔记内容来源于网络，目前顺着题目序号做，高亮部分为当前进度。[点击这里](https://github.com/MrChanGG/HDOJ.git)获得源代码。


基础题：
1000、1001、1004、1005、1008、1012、1013、1014、1017、1019、1021、1028、<mark>1029</mark>、1032、1037、1040、1048、1056、1058、1061、1070、1076、1089、1090、1091、1092、1093、1094、1095、1096、1097、1098、1106、1108、1157、1163、1164、1170、1194、1196、1197、1201、1202、1205、1219、1234、1235、1236、1248、1266、1279、1282、1283、1302、1303、1323、1326、1330、1334、1335、1339、1390、1391、1393、1395、1397、1405、1406、1407、1408、1412、1418、1420、1465、1491、1555、1562、1563、1570、1587、1673、1678、1708、1718、1720、1785、1799、1859、1862、1877、1898、1976、1977、1985、1994、2000、2001、2002、2003、2004、2005、2006、2007、2008、2009、2010、2011、2012、2013、2014、2015、2016、2017、2018、2019、2020、2021、2022、2023、2024、2025、2026、2027、2028、2029、2030、2031、2032、2033、2034、2035、2039、2040、2042、2043、2048、2049、2051、2053、2055、2056、2057、2060、2061、2071、2073、2075、2076、2078、2081、2083、2088、2090、2092、2093、2095、2096、2097、2098、2099、2101、2103、2106、2107、2109、2113、2114、2115、2123、2131、2132、2133、2135、2136、2137、2138、2139、2143、2148、2153、2156、2161、2162、2164、2178、2186、2192、2200、2201、2212、2304、2309、2317、2401、2500、2502、2503、2504、2519、2520、2521、2523、2524、2535、2537、2539、2547、2548、2549、2550、2551、2552、2555、2560、2561、2562、2566、2567、2568、2700、2710

***
1000：EOF的用法
***
1004：对字符数组有些生疏，char[10]相当于一次容纳10个字符,就是一个字母，理解为一个单词。而char [10][10]，可以理解为表示有10个单词，每个单词最长为10个字母，下面是图解,而且while循环要多一个判断n！=0
	0	1	2	3	4	5	6	7
c[0]	a	p	p	l	e	\0	\0	\0
c[1]	o	r	a	n	g	e	\0	\0
c[2]	b	a	n	a	n	a	\0	\0
***
1005：
1.计算此题时，极易因n过大造成超时；
    此时转换思路找规律：后面的f[i]全是基于%7运算得到的，而%7运算的结果只有{0，1，2，3，4，5，6} 这7种情况，也就是说f[i]、f[i-1]、f[i-2]、、、每个数都只有可能取7个数字的一个；
    f[i]=(a\*f[i-1]+b\*f[i-2])%7，此式中，每次参与运算的a和b都是固定的值，而f[i-1]、f[i-2]的值来源于前面的计算结果，也决定了后面的计算结果，因此这个计算的结果必定会出现循环，而f[i-1]、f[i-2]各有7种取法，因此由不同的f[i-1]、f[i-2]得出的f[i]有7*7=49种可能取法，则循环中的一个参照组至多包含49个f[i]，第50个f[i]必然循环，因此设一个f[49]数组，存储每个f[i]，只需计算49-2=47次，便可得出循环参照组（f[49]）；再用n计算其在f[49]中对应的数值即可；
    用此找规律方法将大大缩短当n值过大时的计算时间。
2.&&(a||b||n)当任何一个数都是1的时候才会继续，也就是都不为0，所以可以理解为当都为0的时候退出
***
1012：求阶层用float可以用%.11lf来控制长度
***
1013：遇到用数的最好来个大范围数组，范围问题都用数组来做
***
1014：本质是求互质，互质就是最大公约数是1
***
1017：
解释1：先输入一个数N然后会分N块输入，每块每次输入2个数，n,m，n=m=0时结束，当a和b满足0<a<b<n且使(a^2+b^2 +m)/(a\*b) 的值为整数时，那么这对a和b就是一组，输出这样的组数，一行输入，跟着一样输出。
解释2：先输入一个数N然后会分N块输入，每块每次输入2个数，n,m，n=m=0时结束，当a和b满足0<a<b<n且使(a^2+b^2 +m)/(a\*b) 的值为整数时，那么这对a和b就是一组，输出这样的组数，一行输入，跟着一样输出。
知识点：判断两个数做除法后的数是否为整数，如：(a^2+b^2 +m)/(a\*b)。则只要用if((a\*a+b\*b+m)%(a\*b)==0)即可
而且这个题，不需要同时判断输入的两个数同时为0
***
1019:
（两个数情况）最小公倍数=两数相乘/最大公约数
推广到多个最小公倍数：不断计算两个数的最小公倍数，将上一步的最小公倍数和下一个计算，直到遍历完，最终的结果即为所有数的最小公倍数。
同理求最大公约数：自然而然会想到用逐轮用2个数计算，将上一步的最大公约数与下一个数继续计算，直到计算完所有数。
这个题注意要用_int64类型，输入则是%I64d
***
1021:
判断fo(x)%3=0即可，但是这样常规做法特别容易超时。
思路：找规律，先打印出0-100之间yes和no的情况，会发现no no yes  no no no yes.....除了前面两个no，发现隔4个一个yes。所以转成判断(x-2)%4=0即可。这样连他的函数都不用写了。
***
1028：母函数（第一次遇到，需要学习，解决硬币拆分问题之类的)  [教程1,点击跳转](https://www.cnblogs.com/linyujun/p/5207730.html)[教程2,点击跳转](https://blog.csdn.net/yu121380/article/details/79914529)
本题为C++代码（教程中的代码），改成C不知道为啥一直不能AC。
***
1209：用暴力破解时间复杂度会很大，不建议
实际这个题排好序，输出(n+1)/2位置的数就行
C语言写冒泡排序貌似也会超时，C++的sort内置排序可以通过
***