---
layout: post
title:  "getMin功能的栈"
date:   2017-01-20 12:32:01
categories: 算法
tags: java 算法 
---

* content
{:toc}    


实现特殊的栈，在实现基本功能的基础上实现返回栈中最小元素的操作。  




设计上使用两个栈：  

1. 一个栈用于保存当前栈的元素；  
2. 另一个栈用来保存每一步的最小值。  

```java  
public class MinValueStack {
    private Stack<Integer> stackData;
    private Stack<Integer> stackMin;

    public  MinValueStack() {
        this.stackData = new Stack<Integer>();
        this.stackMin = new Stack<Integer>();
    }

    public MinValueStack(Integer[] data) {
        this.stackData = new Stack<Integer>();
        this.stackMin = new Stack<Integer>();

        for(Integer integer : data) {
            push(integer);
        }
    }

    //入栈
    public void push (int newNum) {
        if (this.stackMin.isEmpty()) {
            this.stackMin.push(newNum);
        } else if (newNum <= this.getMin() ) {
            this.stackMin.push(newNum);
        }
        this.stackData.push(newNum);
    }

    //出栈
    public int pop() {
        if ( this.stackData.isEmpty() ) {
            throw new RuntimeException("stack is empty");
        }
        int value = this.stackData.pop();
        if (value == this.getMin()) {
            this.stackMin.pop();
        }
        return value;
    }


    public int getMin() {
        if (this.stackMin.isEmpty()) {
            throw new RuntimeException("stack is empty");
        } else {
            return this.stackMin.peek();
        }
    }

    public static void main(String[] args) {
        Integer [] data = {10,3,4,2,16,5,2};
        MinValueStack minValueStack = new MinValueStack(data);

        System.out.println(minValueStack.getMin() );

        minValueStack.pop();
        minValueStack.pop();
        minValueStack.pop();
        minValueStack.pop();

        System.out.println(minValueStack.getMin());
    }
}

```  








