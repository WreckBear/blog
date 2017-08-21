# Tomcat的ClassLoader初识

***

> 我们都知道Tomcat的目录结构中有webapp文件夹，在webapp下存放java web项目，启动Tomcat时便会将这些web项目启动起来，我们就可以访问这些web项目了。但从双击startup.bat一直到可以访问自己的项目为止，Tomcat都做了那些事情？它是如何将项目加载启动的？又是加载到哪里？为什么项目之间的类和对象不会混乱？

## 1. ClassLoader的职责和加载机制

![classLoader的角色](/classLoader.png)

如上图，ClassLoader充当了一个加载器的作用，将本地的class文件或者网络的class字节码，读取进内存并在JVM中生成相应的类对象，它的职责我相信是没有什么异议的。

而到了ClassLoader具体加载某个class文件(目前先考虑class文件)的时候，我们就要想了，ClassLoader势必会有一个"路径参数"，指导ClassLoader去哪里寻找class文件，并将它加载到JVM中。

而这个"路径参数"怎么传给ClassLoader呢？JDK的想法是：放到系统的环境变量里。ClassLoader只需要从某个指定的环境变量中取值就完成了这个参数传递。这一点可以从运行java程序的

而事实真的这么简单么？

## Tomcat有必要自定义类加载器么？



## ContextClassLoader

## 2.类加载的向上委托机制

