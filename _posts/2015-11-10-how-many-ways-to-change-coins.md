---
layout: post
title: "组合方式（换零钱方式的统计）how many ways to change coins"
description: ""
category: 
tags: []
---
{% include JB/setup %}

```Java

public class ChangeCoin {

  static final int[] intArray = { 1, 5, 10, 25, 50 };  
  public static void main(String[] args) {
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

```