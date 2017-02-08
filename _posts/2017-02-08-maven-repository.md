---
layout: post
title:  "windows下maven私有仓库搭建"
date:   2017-02-08 18:02:10
categories: maven
tags: maven
---

* content
{:toc}
  
1.  下载 nexus-2.13.0-01-bundle.zip；  

2.  解压后会有两个文件夹，nexus-2.13.0-01（安装配置日志信息等目录）和sonatype-work（工作目录）；  

3.  把路径加入环境变量path中，例如：​D:\nexus\nexus-2.13.0-01\bin；  

4.  管理员身份运行cmd，输入nexus；  

      若出现Usage: nexus { console : start : stop : restart : install : uninstall }则说明路径配置成功。  

5.  输入 nexus install进行安装；  

6.  输入 nexus start启动。​  

7.  nexus默认端口8081，在浏览器输入localhost:8081/nexus即可登陆。  

备注： 启动时失败可通过查看logs下wrapper.conf文件检查失败原因，若出现端口冲突可通过修改nexus.properties中的端口号解决。​  

启动成功如图所示。  
![maven-1]({{"/css/pics/maven/maven-1.png"}})   

在浏览器输入localhost:8081/nexus即可进入。其中端口号可在nexus.properties下进行修改。  

![maven-2]({{"/css/pics/maven/maven-2.png"}})   

点击右上角log in登陆。初始账号admin，密码admin123。  

登陆后点击左侧栏​Repositories，即可看到相应仓库组。  
![maven-3]({{"/css/pics/maven/maven-3.png"}})  

在type栏可以看到，nexus的仓库类型分为以下四种：  

* group: 仓库组  

* hosted：宿主  

* proxy：代理  

* virtual：虚拟  

PublicRepositories:  仓库组​  

* 3rd party: 无法从公共仓库获得的第三方发布版本的构件仓库  

* Apache Snapshots: 用了代理ApacheMaven仓库快照版本的构件仓库  

* Central: 用来代理maven中央仓库中发布版本构件的仓库  

* Central M1 shadow: 用于提供中央仓库中M1格式的发布版本的构件镜像仓库  

* Codehaus Snapshots: 用来代理CodehausMaven 仓库的快照版本构件的仓库  

* Releases: 用来部署管理内部的发布版本构件的宿主类型仓库  

* Snapshots:用来部署管理内部的快照版本构件的宿主类型仓库  

选中任一仓库，即可看到相应信息或进行配置。  

![maven-4]({{"/css/pics/maven/maven-4.png"}}) 

可通过选中Artifact Upload上传jar包到hosted类型的仓库，也可通过拷贝本地库中存在的文件到sonatype-work\nexus\storage 下相应的库目录。  







