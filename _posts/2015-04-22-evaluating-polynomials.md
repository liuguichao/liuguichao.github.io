---
layout: post
title: "Evaluating Polynomials"
category: algorithms
tags: [polynomial]
---
####多项式求值
######问题
给定一串实数a<sub>n</sub>,a<sub>n-1</sub>,...,a<sub>1</sub>,a<sub>0</sub>,以及一个实数x，计算多项式P<sub>n</sub>(x)=a<sub>n</sub>x<sup>n</sup>+a<sub>n-1</sub>x<sup>n-1</sup>+...+a<sub>1</sub>x+a<sub>0</sub>的值。
######解法
去掉第一个系数a<sub>0</sub>,则更小规模的问题变成了计算由系数
a<sub>n</sub>,a<sub>n-1</sub>,...,a<sub>1</sub>表达的多项式，即
P<sub>n-1</sub>(x)=a<sub>n</sub>x<sup>n-1</sup>+a<sub>n-1</sub>x<sup>n-2</sup>+...+a<sub>1</sub>。
显然 P<sub>n</sub>(x)=xP<sub>n-1</sub>(x)+a<sub>0</sub>.

完整的算法可用如下表达式来说明：**(Horner规则)**

>
a<sub>n</sub>x<sup>n</sup>+a<sub>n-1</sub>x<sup>n-1</sup>+...+a<sub>1</sub>x+a<sub>0</sub>=((...((a<sub>n</sub>x+a<sub>n-1</sub>)x+a<sub>n-2</sub>)...)x+a<sub>1</sub>)x+a<sub>0</sub>

``` C++
int polynomialEvaluation(vector<int> a,int x)
{
	int n=a.size();
	int p=a[n-1];
	for(int i=0;i!=n-1;++i)
		p=p*x+a[n-i-2];
	return p;
}
```




