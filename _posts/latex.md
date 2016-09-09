---
layout: page
title:	latex 数学公式
category: blog
description: 
---
# Preface
[latex 数学公式](http://zh.wikipedia.org/wiki/Help:%E6%95%B0%E5%AD%A6%E5%85%AC%E5%BC%8F)

[常用数学公式](http://www.ituring.com.cn/article/32403)

# learn latex
http://www.jianshu.com/p/e59aaac15088

## maclatex
http://www.zhihu.com/question/20928639

在 OS X 上，主流的 TeX 发行版是 MacTeX。这是一个基于 TeX Live 之上的封装，和 TeX Live 的主要区别是：
采用 OS X 专用的方式打包，安装简便，不劳心配置；
封装了一系列为 OS X 设计的工具（LaTeXit、TeXshop、TeX Live Utilities 等）
@过拟合 的答案中，推荐 TeX Live 而不推荐 MacTeX 实际上是没有必要的，对新手来说尤其如此。在 OS X 上安装 TeX Live 对新手来说，有些难度；MacTeX 里封装的其他工具，如果不喜，很简单就可以移除。总之，放着好用的工具不用，不必要地去折腾自己，我是不推荐的。

BasicTeX 和 MacTeX 类似，也是对 TeX Live 的封装。
不过，相比 MacTeX，BasicTeX 中缺少很多宏包。在使用的时候，需要先手工安装这些宏包，然后使用。对于新手来说，这又是个不小的工程。所以不推荐新手使用。

# Api && Lib
[google api](http://chart.apis.google.com/chart?cht=tx&chl=O%28%5Clog+n%29)

## MathJax
这是一个js 库. 用法如`$ a^2=b$`


# 函数、符号、及字符

## 函数
	\sin a \cos b \tan c	
	O(\log n)

## 根

	\sqrt{x} \sqrt[n]{x}	

## 上标(幂)
	a^2

## 下标
	a_2

## 符号
	\pi 表示希腊字母 π，\infty 表示 ∞。更多的符号请参见：Special Symbols 。
	\sqrt{被开方数} 表示平方根。另外，\sqrt[n]{x} 表示 n 次方根。
	_{下标} 和 ^{上标} 可以用在任何地方。如果上下标只是一个字符，可以省略 { 和 } 。
	此外，\ldots 和 \cdots 都表示省略号，前者排在基线上，后者排在中间。
	还有：\pm：±、\times：×、\div：÷ 。

	\sum_{下标}^{上标} 表示求和符号。
	\prod{i=0}^N x_i 表示乘积符号，
	\int_{-N}^{N} e^x\, dx 表示积分符号。
	\iint_{D}^{W} \, dx\,dy	双重积分

	\pi 表示希腊字母 π，\infty 表示 ∞。更多的符号请参见：Special Symbols 。
	\frac{分子}{分母} 表示分数。另外，\tfrac{分子}{分母} 表示小号的分数。
	\sqrt{被开方数} 表示平方根。另外，\sqrt[n]{x} 表示 n 次方根。
	\sum_{下标}^{上标} 表示求和符号。另外，\prod 表示乘积符号，\int 表示积分符号。
	_{下标} 和 ^{上标} 可以用在任何地方。如果上下标只是一个字符，可以省略 { 和 } 。
	此外，\ldots 和 \cdots 都表示省略号，前者排在基线上，后者排在中间。
	还有：\pm：±、\times：×、\div：÷ 。


# 分数、矩阵和多行列式
	//分数
	\frac{2}{4}=0.5 2/4=0.5
	//分数嵌套
	\cfrac{2}{c + \cfrac{2}{d + \cfrac{2}{4}}} = a	
	//
	\frac{分子}{分母} 表示分数。另外，\tfrac{分子}{分母} 表示小号的分数。


\frac{分子}{分母} 表示分数。另外，\tfrac{分子}{分母} 表示小号的分数。
\sqrt{被开方数} 表示平方根。另外，\sqrt[n]{x} 表示 n 次方根。
\sum_{下标}^{上标} 表示求和符号。另外，\prod 表示乘积符号，\int 表示积分符号。
_{下标} 和 ^{上标} 可以用在任何地方。如果上下标只是一个字符，可以省略 { 和 } 。
此外，\ldots 和 \cdots 都表示省略号，前者排在基线上，后者排在中间。
还有：\pm：±、\times：×、\div：÷ 。
