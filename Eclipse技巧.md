## Eclipse技巧

1. Program arguments 和 VM arguments的区别?

   当运行一个java程序的时候，必定有一个入口类，暂且假设入口类是Main，Main中也必定有一个main入口方法，而main方法是有参数的，这个参数是调用这个方法的时候由调用者传入的，而一般的我们在命令行执行`java Main param1 param2 `，就是在调用`Main.main(param1,param2)`，这里param1和param2就是eclipse中的`peogram arguments`，指的是这部分参数是作为java程序main方法的参数传入的。

   而`VM arguments`，是针对jvm的参数，`vm arguments`不会被传到main方法的参数里去，而是对jvm运行所提供的参数。比如`-XX:MaxPermSize 200`，就是让jvm运行起来的时候，设置最大永久带内存为200M。

   PS：`vm argument`中，以-D开头的参数，都会设置到System类中的props变量里存储起来，这样jvm或者程序，都可以通过`System.getProperty(argname)`来获取参数的值。

2. Eclipse的Libraries是什么？