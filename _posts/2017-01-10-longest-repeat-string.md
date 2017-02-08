---
layout: post
title:  "求最长连续回文子串长度"
date:   2017-01-16 22:32:31
categories: 算法
tags: java 算法 
---

* content
{:toc}     

## 求最长回文子串  

### 朴素算法 

(1)首先，最容易想到的方法就是在字符串每一位置截取左右两边，判断相等个数。  
虽然时间复杂度为n^2，不过先实现了再说。  






```java  
// 获取最大回文子串
private int getMaxReLength(String data) {
    int returnLength = 0;
    int length = data.length();
    //右侧部分返回长度的半径不用计算
    for (int i = 0 ; i < length - returnLength%2 ; i++) {
        // 取位置i的左侧
        String dataLeft = getLeft(data, i);
        // 取位置i的右侧
        String dataRight = getRight(data,i);
        // 取位置i+1的右侧
        String dataRightTwo = getRight(data,i+1);

        //假设回文串为i开始的偶对称
        int lengthTmpOne = equalLength(dataLeft,dataRight);
        //假设回文串为i+1开始的奇对称
        int lengthTmpTwo = equalLength(dataLeft,dataRightTwo);

        //偶对称长度大于奇对称长度，则回文串总长为偶数2*length，否则为奇数2*length+1
        int lengthTmp = lengthTmpOne  > lengthTmpTwo ? lengthTmpOne * 2 :lengthTmpTwo * 2 +1;
        returnLength = returnLength > lengthTmp ? returnLength :lengthTmp;
    }
    return returnLength;
}

// 获取字符串左侧,并转置
private String getLeft(String data, int length) {
    String tmp = "";
    for (int i = length ; i > 0 ; i--) {
        tmp += data.charAt(i-1);
    }
    return tmp;
}

// 获取字符串右侧
private String getRight(String data,int i) {
    return data.substring(i);
}

// 判断相等长度
private int equalLength(String dataOne,String dataTwo) {
    if ( dataOne.isEmpty() || dataTwo.isEmpty()) {
        return 0;
    }
    int maxLength = dataOne.length() > dataTwo.length() ? dataTwo.length() : dataOne.length();
    int length = 0;
    while ( length < maxLength ) {
        if ( dataOne.charAt(length) == dataTwo.charAt(length) ) {
            length++;
        } else {
            return length;
        }
    }
    return length;
}  
```  

### Manacher算法  

由于时间复杂度为n^2，因此需考虑降低时间复杂度。网上查到了Manacher算法，时间复杂度为n。  

```java  
public static int getPalindromeLength2(String str) {
    // 1.构造新的字符串
    // 为了避免奇数回文和偶数回文的不同处理问题，在原字符串中插入'#'，将所有回文变成奇数回文
    StringBuilder newStr = new StringBuilder();
    newStr.append('#');
    for (int i = 0; i < str.length(); i ++) {
        newStr.append(str.charAt(i));
        newStr.append('#');
    }

    // rad[i]表示以i为中心的回文的最大半径，i至少为1，即该字符本身
    int [] rad = new int[newStr.length()];
    // right表示已知的回文中，最右的边界的坐标
    int right = -1;
    // id表示已知的回文中，拥有最右边界的回文的中点坐标
    int id = -1;
    // 2.计算所有的rad
    // 这个算法是O(n)的，因为right只会随着里层while的迭代而增长，不会减少。
    for (int i = 0; i < newStr.length(); i ++) {
        // 2.1.确定一个最小的半径
        int r = 1;
        if (i <= right) {
            r = Math.min(rad[id] - i + id, rad[2 * id - i]);
        }
        // 2.2.尝试更大的半径
        while (i - r >= 0 && i + r < newStr.length() && newStr.charAt(i - r) == newStr.charAt(i + r)) {
            r++;
        }
        // 2.3.更新边界和回文中心坐标
        if (i + r - 1> right) {
            right = i + r - 1;
            id = i;
        }
        rad[i] = r;
    }

    // 3.扫描一遍rad数组，找出最大的半径
    int maxLength = 0;
    for (int r : rad) {
        if (r > maxLength) {
            maxLength = r;
        }
    }
    return maxLength - 1;
}
```  





