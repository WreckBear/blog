## Eclipse技巧

1. Program arguments 和 VM arguments的区别?

   当运行一个java程序的时候，必定有一个入口类，暂且假设入口类是Main，Main中也必定有一个main入口方法，而main方法是有参数的，这个参数是调用这个方法的时候由调用者传入的，而一般的我们在命令行执行`java Main param1 param2 `，就是在调用`Main.main(param1,param2)`，这里param1和param2就是eclipse中的`peogram arguments`，指的是这部分参数是作为java程序main方法的参数传入的。

   而`VM arguments`，是针对jvm的参数，`vm arguments`不会被传到main方法的参数里去，而是对jvm运行所提供的参数。比如`-XX:MaxPermSize 200`，就是让jvm运行起来的时候，设置最大永久带内存为200M。

   PS：`vm argument`中，以-D开头的参数，都会设置到System类中的props变量里存储起来，这样jvm或者程序，都可以通过`System.getProperty(argname)`来获取参数的值。

2. 如何用自定义类加载器加载自己的程序？

   在vm arguments中加入`-Djava.system.class.loader=YourClassLoader`，这样，ClassLoader.getSystemClassLoader()的值就是YourClassLoader类加载器，而自己的程序也是被YourClassLoader去加载的。

   PS:自定义类如果如上使用，必须实现接受一个ClassLoader参数的构造方法，传入的ClassLoader参数应作为自定义类加载的父加载器

3. ClassLoader中的loadClass()、findClass()等方法的区别？

   ClassLoader默认采用双亲委托机制来加载一个类，双亲委托的机制的实现是在loadClass()方法中编写的；而每个ClassLoader的加载路径不同，每个ClassLoader在哪里加载以何种方式加载class数据是在findClass()方法中编写的。

   例如从磁盘D:\xx路径下寻找class文件并转换成byte[]的AClassLoader 和 从网络上寻找class数据并转换成byte[]的BClassLoader，他们的loadClass()应该是一样的，但是他们findClass()肯定不一样。

   PS:如果遵守双亲委托机制，那么自定义类时，最好是覆盖findClass()方法，而不是loadClass()方法。

4. contextClassLoader是怎么回事？

5. Eclipse的Libraries是什么？