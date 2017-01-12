---
layout: post
title:  "http报文"
date:   2017-01-12 21:22:24
categories: web
tags: http web
---

* content
{:toc}

http（超文本传输协议）工作在应用层，是一种请求/响应式协议。即客户端和服务器建立连接后，向服务器发送请求；服务器街道请求后，给予所需的响应。  




## http报文组成  

http报文即在http应用程序之间发送的数据块，它是简单的格式化数据块，主要由三部分组成：  

* 用于描述的起始行（start line）  
* 包含属性的首部块（header）  
* 包含数据的主体部分（body）  

格式如图所示：  
![http-message]({{"/css/pics/http-message.png"}})   

## http报文分类  

所有http报文都可以分为两类：  

* 请求报文    
* 响应报文  

### 请求报文格式：  

```html  
<method> <request-URL> <version>
<headers>  

<entity-body>  
```  

### 响应报文格式：  
```html  
<version> <status> <reason-phrase>
<headers>  

<entity-body>  
```  

由上可见，请求和响应报文只有起始行的语法不一致。  

### 报文各部分简述  

**方法（method）**  
客户端希望服务器对资源执行的动作。比如get、head、post等。  
  
| 方法        | 描述    |  是否包含主体  |    

| --------   | -----:   | :----: |  
  
| get        | 从服务器获取一份文档      |   否    |   
  
| head        | 只从服务器获取文档的首部      |   否    |   
  
| post        | 向服务器发送需要处理的数据     |   是    |    
  
| put        | 将请求的主体部分存储在服务器上      |   否    |   
  
| trace        | 对报文进行追踪      |   否    |   
  
| options        | 决定可以在服务器上执行哪些方法      |   否    |   
  
| delete        | 从服务器上删除一份文档      |   否    |   



**请求url（request-URL）**  
命名所请求资源或者url路径的完整url。  

**版本(version)**  
报文所使用的http版本。格式如下： 

```html     
HTTP/major.minor
```

**状态码(status-code)**   
为3位的数字，描述了请求过程中发生的情况，第一位数字用于描述状态的一般类别。 
   
* 1**： （100-101）信息提示      
* 2**： （200-206）表示成功      
* 3**： （300-305）表示资源已经被移走（重定向）    
* 4**： （400-415）客户端请求出错    
* 5**： （500-505）服务器出错     

**原因短语(reason-phrase)**  
上述状态码的可读版本，即原因短语，只对阅读有意义，一般以状态码为准。  

**首部（header)**  
可以有0或者多个首部，每个首部都包含名字，后面跟冒号(:)，然后为可选空格，接着为值，最后是CRLF（结束符）。   
首部又可分为：  

* 通用首部  
* 请求首部  
* 响应首部  
* 实体首部  
* 扩展首部  

示例如下：  
![header-example]({{"/css/pics/header-example.png"}}) 

**实体的主体部分(entity-body)**  
   包含一个由任意数据组成的数据块。并不是所有报文都包含这一部分。  
可以承载：图片、视频、html文档、邮件等数据类型。  

请求和响应报文示例如下：  
![message-example]({{"/css/pics/message-example.png"}})   

>                                       from 《http权威指南》




