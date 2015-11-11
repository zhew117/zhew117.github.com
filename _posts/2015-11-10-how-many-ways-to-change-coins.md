---
layout: post
title: "组合方式（换零钱方式的统计）how many ways to change coins"
description: ""
category: 
tags: []
---
{% include JB/setup %}


递归
===

####SICP Page26

给了半美元，四分之一美元，10美分，5美分和1美分硬币，将1美元换成零钱，一共有多少种不同的方式？

更一般的问题是，给定了任意数量的现金，如何通过程序计算出所有换零钱方式的种树吗？

采用递归的过程，这一问题有了一种很简单的算法。假定我们所考虑的可用硬币类型种类排了某种顺序，于是就有了下面的关系。

将总数为a的现金换成n种硬币的不同的方式的数目等于

* 将现金数a换成除第一种硬币之外的所有其他硬币的不同方式数目，，加上 
* 将现金数a-d换成所有种类的硬币的不同方式数据，其中的d是第一种硬币的币值。


###「Talk is cheap. Show me the code」
______________________________________



public class ChangeCoin {

  //定义组合的数字

  static final int[] intArray = { 1, 5, 10, 25, 50 };  

  public static void main(String[] args) {

    //算出有多少种方式可以组成100
    System.out.print(cc(100,intArray.length));
  }
  
  public static int cc(int amount,int kindsOfCoins){
    
    if(amount == 0){
      return 1;
    }
    else if(amount <1 || kindsOfCoins == 0){
      return 0;
    }
    else {
      return cc(amount,kindsOfCoins - 1)+cc(amount -firstDenomination(kindsOfCoins),kindsOfCoins);  
    }
    
  }
  
  public static int firstDenomination(int kindsOfCoins){

    return intArray[kindsOfCoins-1];

  }
}


####上述代码计算出了用1，5，10，25，50这五种数值一共有多少种方式组合成100。


