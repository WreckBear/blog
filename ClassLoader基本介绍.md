## ClassLoader入门

------

### 1. ClassLoader简介

首先，ClassLoader是用来将class字节码装载到内存中，并生成Class对象的一个装载器。而在JDK中，存在三个ClassLoader，如下

> - BootstrapClassLoader
> - ExtClassLoader
> - AppClassLoder

BootstrapClassLoader（由非JAVA语言实现，在jdk中无对应类）加载器负责加载jdk中的核心资源，加载的路径被放在系统环境变量` “sun.boot.class.path”` 中.	

ExtClassLoader加载器负责加载环境变量为`"java.ext.dirs"`中存储的路径中的资源，一般为jre/lib/ext目录，在某些情况下，将第三方jar直接放入此目录，便可被加载

AppClassLoader加载器负责加载环境变量为`"java.class.path"`中存储的路径中的资源，这是最常用的类加载器。

### 2. ClassLoader的向上委托机制

ClassLoader加载一个类文件时，会先检测此类是否已经被加载，如果没有被加载，类加载器是不会去加载这个类文件，而是向自己的附加载器委托，父加载器也会查自己是不是加载过这个类，如果加载了，就直接返回这个类对象，如果没有加载，继续往上委托，直到BootstrapClassLoader，此加载器是没有父加载器的，就会尝试去真正加载这个类文件，如果加载不到，就返回子类进行加载。

### 3.ClassLoader的不同作用

每个加载器的加载路径和加载类字节码的方式不同，并且不同类加载器加载的同一个类文件所生成的类对象是不一样的。