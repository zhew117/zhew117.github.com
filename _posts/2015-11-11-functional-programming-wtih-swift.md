---
layout: post
title: "Functional Programming Wtih Swift"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Functional Programming with Swift
=====================


###[原文](http://www.raywenderlich.com/82599/swift-functional-programming-tutorial)


-------------------------------------------------

Swift支持Functional Programming，温习一下。

我们要做的就是就是把

- ["Cat", "Chicken", "fish", "Dog", "Mouse", "Guinea Pig", "monkey"]

转换成一个带Index并且分好类的

- [("C", [Cat, Chicken]), ("D", [Dog]), ("F", [fish]), >("G", [Guinea Pig]), ("M", [Mouse, monkey])]

```
let words = ["Cat", "Chicken", "fish", "Dog","Mouse", "Guinea Pig", "monkey"]

typealias Entry = (Character, [NSString])
```

### 把大脑的思维逻辑交给程序来做，抽象和封装不够，代码难以理解，而且变量多，容易出BUG ###

```
func buildIndex(words: [NSString]) -> [Entry] {

    //result存结果
    var result = [Entry]()
    
    //letters存头字母
    var letters = [Character]()
    
    //把不重复的头字母大写存进去
    for word in words{
        let firstLetter = Character(word.substringToIndex(1).uppercaseString)
    
        if !letters.contains(firstLetter) {
            letters.append(firstLetter)
        }
    }
    
    //然后来比较所有单词的头字母和刚才得到的头字母表匹配
    for letter in letters {
        var wordsForLetter = [NSString]()
        for word in words {
            let firstLetter = Character(word.substringToIndex(1).uppercaseString)
            
            if firstLetter == letter {
              //把头字母匹配的单词放wordsForLetter里
               wordsForLetter.append(word)
            }
        }
        //把wordsForLetter带着头字母放到结果集
        result.append((letter, wordsForLetter))
    }
    
    
    return result

}
```

好处：随想随写，手起码落。分分钟buildIndex
坏处：一日未见，如隔三秋。



### 方法作为参数传递，确保了程序的immutable。 ###


```
//通用的去重方法
func distinct<T:Equatable>(source: [T]) -> [T]{
    var unique = [T]()
    for item in source{
        if !unique.contains(item) {
            unique.append(item)
        }
    }
    return unique
}

//重头戏来了
//firstLetter为内部方法
//words先用firstLetter来map一下，然后sort()排序，然后再字母表map出一个Entry，构造Entry的同时filter出头字母一样的单词
func buildIndex(words: [String]) -> [Entry] {
    func firstLetter(str:String) -> Character{
        return Character(str.substringToIndex(str.startIndex.advancedBy(1)).uppercaseString)
    }
    return distinct(words.map(firstLetter)).sort().map{
        (letter) -> Entry in
            return (letter,words.filter{
            (word) -> Bool in
        firstLetter(word) == letter
            })
    }
}
```
###[原文](http://www.raywenderlich.com/82599/swift-functional-programming-tutorial)
