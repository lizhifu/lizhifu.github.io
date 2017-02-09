---
layout: post
title:  "判断是否互为变形词"
date:   2017-01-09 10:32:43
categories: 算法
tags: 算法 java
---

* content
{:toc}

给定字符串A和字符串B，如果A和B中字符种类且每个字符出现次数一样，则A和B互为变形词。  
比如： 
abc和bca true  
abcd和bca false  





```java   
public static boolean isDeformation(String dataOne,String dataTwo) {

    if ( dataOne == null || dataTwo == null || dataOne.length() != dataTwo.length()) {
        return false;
    }

    char [] charOne = dataOne.toCharArray();
    char [] charTwo = dataTwo.toCharArray();

    int [] map = new int[256];

    for (int i = 0 ; i < dataOne.length(); i++) {
        map[charOne[i]]++;
    }

    for (int i = 0 ; i < dataTwo.length(); i++) {
        if (map[charTwo[i]] -- == 0) {
            return false;
        }
    }

    return true;
}  
```  



