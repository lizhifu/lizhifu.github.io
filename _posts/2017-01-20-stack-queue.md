---
layout: post
title:  "用栈实现队列"
date:   2017-01-20 12:32:01
categories: 算法
tags: java 算法 
---

* content
{:toc}    


用两个栈组成队列。  




栈的特点：先进后出，队列的特点：先进先出。使用两个栈可以把顺序反过来实现其操作。  

实现方法：  
    一个栈作为压入栈，一个栈只作为弹出栈。  

注意点：  

1. 往弹出栈压数据时，必须一次性把压入栈中数据全部压入；  
2. 弹出栈不为空时，不能忘弹出栈压数据。  

```java  
public class StackQueue {
    private Stack<Integer> stackAdd;
    private Stack<Integer> stackPoll;

    public StackQueue() {
        this.stackAdd = new Stack<Integer>();
        this.stackPoll = new Stack<Integer>();
    }

    public StackQueue(Integer[] data) {
        this.stackAdd = new Stack<Integer>();
        this.stackPoll = new Stack<Integer>();

        for(Integer integer : data) {
            add(integer);
        }
    }

    public int poll() {
        if (stackAdd.empty() && stackPoll.empty()) {
            throw new RuntimeException("queue is empty");
        } else if ( stackPoll.empty()) {
            while( !stackAdd.empty()) {
                stackPoll.push(stackAdd.pop());
            }
        }
        return stackPoll.pop();
    }

    public void add(int data) {
        this.stackAdd.push(data);
    }

    public int peek() {
        if ( stackAdd.empty() && stackPoll.empty()) {
            throw new RuntimeException("queue is empty");
        } else if ( stackPoll.empty() ) {
            while (!stackAdd.empty()) {
                stackPoll.push(stackAdd.pop());
            }
        }
        return stackPoll.peek();
    }

    public boolean isEmpty() {
        return (stackAdd.empty() && stackPoll.empty() );
    }

    public static void main(String[] args) {
        Integer [] data = {10,3,4,2,16,5,2};
        StackQueue stackQueue = new StackQueue(data);

        while ( !stackQueue.isEmpty() ) {
            System.out.println(stackQueue.poll());
        }
    }
}
```  








