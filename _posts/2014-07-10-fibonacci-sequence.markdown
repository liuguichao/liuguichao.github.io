---
layout: post
title: "fibonacci sequence"
date: 2014-07-10 13:25:14 +0800
comments: true
category: algorithms
tags: [斐波那契数列]
---
斐波那契数列，又称黄金分割数列，指的是这样一个数列：1、1、2、3、5、8、13、21、……在数学上，斐波纳契数列以如下被以递归的方法定义：F0=1，F1=1，Fn=F(n-1)+F(n-2)（n>=2，n∈N*）在现代物理、准晶体结构、化学等领域，斐波纳契数列都有直接的应用。
![](http://i.imgur.com/BsryFKV.jpg)

有趣的是：这样一个完全是自然数的数列，通项公式却是用无理数来表达的。而且当n趋向于无穷大时，后一项与前一项的比值越来越逼近黄金分割0.618.（或者说后一项与前一项的比值小数部分越来越逼近黄金分割0.618、前一项与后一项的比值越来越逼近黄金分割0.618）

1÷1=1，1÷2=0.5，2÷3=0.666...，3÷5=0.6，5÷8=0.625，…………，55÷89=0.617977…，…………144÷233=0.618025…46368÷75025=0.6180339886…...

从第二项开始，每个奇数项的平方都比前后两项之积少1，每个偶数项的平方都比前后两项之积多1。[f(n)]^2-f(n-1)f(n+1)=(-1)^(n-1)


斐波那契数列在自然科学的其他分支，有许多应用。例如，树木的生长，由于新生的枝条，往往需要一段“休息”时间，供自身生长，而后才能萌发新枝。所以，一株树苗在一段间隔，例如一年，以后长出一条新枝；第二年新枝“休息”，老枝依旧萌发；此后，老枝与“休息”过一年的枝同时萌发，当年生的新枝则次年“休息”。这样，一株树木各个年份的枝桠数，便构成斐波那契数列。这个规律，就是生物学上著名的“鲁德维格定律”。
另外，观察延龄草、野玫瑰、南美血根草、大波斯菊、金凤花、耧斗菜、百合花、蝴蝶花的花瓣，可以发现它们花瓣数目具有斐波那契数：3、5、8、13、21、……
其中百合花花瓣数目为3，梅花5瓣，飞燕草8瓣，万寿菊13瓣，向日葵21或34瓣，雏菊有34,55和89三个数目的花瓣

**数字谜题**

三角形的三边关系定理和斐波那契数列的一个联系：
现有长为144cm的铁丝，要截成n小段（n>2），每段的长度不小于1cm，如果其中任意三小段都不能拼成三角形，则n的最大值为多少？
分析：由于形成三角形的充要条件是任何两边之和大于第三边，因此不构成三角形的条件就是任意两边之和不超过最大边。截成的铁丝最小为1，因此可以放2个1，第三条线段就是2（为了使得n最大，因此要使剩下来的铁丝尽可能长，因此每一条线段总是前面的相邻2段之和），依次为：1、1、2、3、5、8、13、21、34、55，以上各数之和为143，与144相差1，因此可以取最后一段为56，这时n达到最大为10。
我们看到，“每段的长度不小于1”这个条件起了控制全局的作用，正是这个最小数1产生了斐波那契数列，如果把1换成其他数，递推关系保留了，但这个数列消失了。这里，三角形的三边关系定理和斐波那契数列发生了一个联系。
在这个问题中，144>143，这个143是斐波那契数列的前n项和，我们是把144超出143的部分加到最后的一个数上去，如果加到其他数上，就有3条线段可以构成三角形了。

**斐波那契—卢卡斯数列**

卢卡斯数列1、3、4、7、11、18…，也具有斐波那契数列同样的性质。（我们可称之为斐波那契—卢卡斯递推：从第三项开始，每一项都等于前两项之和f(n) = f(n-1)+ f(n-2）。

**排列组合**

有一段楼梯有10级台阶，规定每一步只能跨一级或两级，要登上第10级台阶有几种不同的走法?
这就是一个斐波那契数列：登上第一级台阶有一种登法；登上两级台阶，有两种登法；登上三级台阶，有三种登法；登上四级台阶，有五种登法……
1，2，3，5，8，13……所以，登上十级，有89种走法。
类似的，一枚均匀的硬币掷10次，问不连续出现正面的可能情形有多少种？
答案是（1/√5)*{[(1+√5)/2]^(10+2) - [(1-√5)/2]^(10+2)}=144种。
求递推数列a⑴=1，a(n+1)=1+1/a(n）的通项公式
由数学归纳法可以得到：a(n)=F(n+1)/F(n），将斐波那契数列的通项式代入，化简就得结果。

**兔子繁殖问题**

斐波那契数列又因数学家列昂纳多·斐波那契以兔子繁殖为例子而引入，故又称为“兔子数列”。一般而言，兔子在出生两个月后，就有繁殖能力，一对兔子每个月能生出一对小兔子来。如果所有兔子都不死，那么一年以后可以繁殖多少对兔子？
我们不妨拿新出生的一对小兔子分析一下：第一个月小兔子没有繁殖能力，所以还是一对两个月后，生下一对小兔对数共有两对,三个月以后，老兔子又生下一对，因为小兔子还没有繁殖能力，所以一共是三对－－－－－－
依次类推可以列出下表：
![](http://i.imgur.com/zSU3HmG.jpg)
 
	1. 幼仔对数=前月成兔对数
	2. 成兔对数=前月成兔对数+前月幼仔对数
	3. 总体对数=本月成兔对数+本月幼仔对数

可以看出幼仔对数、成兔对数、总体对数都构成了一个数列。这个数列有关十分明显的特点，那是：前面相邻两项之和，构成了后一项。
这个数列是意大利中世纪数学家斐波那契在<算盘全书>中提出的，这个级数的通项公式，除了具有a(n+2)=an+a(n+1）的性质外，还可以证明通项公式为：an=(1/√5)*{[(1+√5)/2]^n-[(1-√5)/2]^n}（n=1,2,3.....）

    //Fibonacci Sequence 1(pos=0)、1、2、3、5、8、13、21、34......
    int findByIteration(int pos)
    {
    	if(pos<0) throw invalid_argument("pos must be non-negative!");	
    	if(pos==1||pos==0) return 1;
    		int x1=1,x2=1,x3=0;
    		for(int i=2;i<=pos;++i)
    		{
    			x3=x1+x2;
    			if(x3<0) throw runtime_error("overflow!"); 			
    			x1=x2;
    			x2=x3;
    		}	
    	return x3;
    }

    //Fibonacci Sequence 1(pos=0)、1、2、3、5、8、13、21、34......
    int findByIterationWithCache(int pos)
    {
    	if(pos<0) throw invalid_argument("pos must be non-negative!");	
    	static vector<int> vct={1,1};
    	int cachePos=vct.size();	
    	if(pos<cachePos) return vct[pos];
    	else		
    	{		
    		int x1=vct[cachePos-2],x2=vct[cachePos-1],x3=0;
    		for(int i=cachePos;i<=pos;++i)
    		{
    			x3=x1+x2;
    			if(x3<0) throw runtime_error("overflow!"); 
    			else vct.push_back(x3);
    			x1=x2;
    			x2=x3;
    		}
    	}
    	return vct[pos];
    }