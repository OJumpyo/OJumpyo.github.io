--- 
layout: post
title: "快速开平方根的几种方法"
date:  2020-02-22
tags: [git]
comments: true
author: OJumpyo
---

ps: 连分数法和长除法的详细描述近期更新为李永乐老师的专栏。之前的描述不清楚，已经删除

## 一、连分数法
~~~
                    b
√s = a + ——————————————————————
                    b          
          2a + ————————————————
                         b
                2a + ——————————
                      2a + ...
~~~
设`s = a²+b`，（a尽量接近s），理论上这个连分数法可以无限接近于s的正平方根。实际过程中，一般取一两次计算即可  

例如求150的平方根，`150 = 144 + 6`，那么`a = 12, b = 6`。所以  
~~~
√s = a = 12                                     (0)

                b
√s  =  a +  —————————— = 12 + 6/24 = 12.25      (1)
                2a

                b
√s  =   a + ———————————————— = 12.2474227       (2)
                        b
            2a  +  ——————————
                        2a
~~~
`√150 =  12.2474487...`，拟合的很好。  
~~~yml
# 证明
    因为： s = a² + b
    所以： (√s + a)(√s - a) = b
    所以： √s - a = b / (√s + a)
    所以： √s = a + b / (√s + a)  ....(*)
~~~
对(*)式，在右侧代入`√s =(*)`。得到:
~~~
                b                               b
√s = a + ——————————————————  =    a +  ——————————————————
                    b                               b
         a + a + ——————————               2a + ——————————
                  a  +  √s                       a + √s
~~~ 
循环迭代，便得到上式

## 二、长除法
1. 分段：从小数点开始，向左和向右每两位分一段。1234分段后就是12，34 . 00,00，…分段之后，每一段上面要求出一个一位数字的商，一共有4位数。  
这些商合起来就是所求的平方根的前几位。注意：平方根的小数点与原来的小数点对齐。
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-1.webp)  

2. 试根 首先计算第一段【12】上面的商，类似于除法，我们要找到一个除数和商，它们的乘积最接近12，又小于12.   
与普通除法不同的是：除数和商需要是同样的一位数字。显然，这个数字是3，3x3=9。然后把12和9做减法，得到余项3。
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-2.webp)  

3. 更新余项：将第二段【34】落下来，构成新的余项334。
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-3.webp)  

4. 乘20：这一步是理解算法的关键。我们需要将原来已经得到的商乘以20，然后把除了最后一位0以外的其他数字写到刚才得到的余项的左侧，构成新的除数的一部分。例如：已经得到了第一段的商是3，乘以20得到60，把0空着，数字6写到余项334的左侧：
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-4.webp)  

5. 再试根。现在要求第二位商，它必须和第二个除数末位数字相同。而且，按照除法规则，第二位商和除数的乘积不能超过余项334。  
经过尝试，第二位的商最大可以填5. 5x65=325，再用334-325求出新的余项9.
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-5.webp)  


6. 再将00段落下，更新余项为900, 重复上述过程：将已经得到的商35乘20，把结果700写到除数上，但留下最后一位不写。
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-6.webp)  


7. 求第三位的商，这个商与除数末位数字相同，并且二者乘积不超过900，应该商1，1x701=701，再求出余项900-701=199。
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-7.webp)  


8. 按照同样的方法，将后一段00落下，把刚得到的商351乘20，并加上一个待定的数字写在除数的位置上，这个待定的数字要与第四位商相同，二者的乘积不超过19900. 这个商是2
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-8.webp)  

按照这样的方法，只要我们愿意，就能手动得到任意精度的平方根。实际上，1234的平方根是35.128336… 前面几位正是35.12

这个方法的证明也不难。我们假设一个数字s的平方根是两位数，它的十位是a，个位是b，我们写作
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-9.webp)  

将这个式子两边平方
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-10.webp)  

再进行移项，和提公因式，得到
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/changchufa-11.webp)  

大家看，左边有s-100a²，100代表了两位数字一段，每一段要求一次余项。  
右侧有20a，就是把之前得到的商乘以20，然后加上b乘上b，意思是除数的最后一位和商必须是相同的数字。  
如果两侧刚好相等，就说明开根号完成了，这恰恰就是刚才我们的计算过程。实际上，a不一定是一位数，它可以代表一串数字，这种算法具有普遍性。


## 三、牛顿迭代法
<http://www.matrix67.com/blog/archives/361>  
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/newton-1.png)  

Java实现开平方的牛顿迭代法. 求的算术平方根就是求`f(x) = x² - a`的正根, 得迭代公式: `x(n+1) = 1/2 * (x(n) + a/(x(n)) )`代码中取初始值 , 误差控制在`10的－15次方`
~~~java
public static double sqrt(double c) {
 
    if (c < 0) {
 
return Double.NaN;
    }
     
    double e = 1e-15;
    double x = c;
    double y = (x + c / x) / 2;
    while (Math.abs(x - y) > e) {
        x = y;
        y = (x + c / x) / 2;
    }
    return x;
}
~~~
## 四、泰勒展开法
 
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/taylor-1.jpg)  
取一阶展开，二阶省略时，退化为牛顿迭代法.  

~~~
已知: x2=N, 求x。
问题转化为f(x)=N−x2=0,解x。
~~~

![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/taylor-2.png)  


## 五、API法
~~~java
Math.sqrt(a);
~~~
![](https://github.com/OJumpyo/OJumpyo.github.io/raw/master/images/2020-02-14-methods_to_sqrt_number/emoji.jfif)  
