##  Eclipse.ini参数 --launcher.XXMaxPermSize

​	在eclipse.ini配置文件中，有一个参数是`--launcher.XXMaxPermSize`，乍一看，他是JVM中的设置最大永久带内存的配置，可以仔细一下，配置文件的下面还有一个`-XX:MaxPermSize`参数，这就有点迷惑了，这是怎么回事呢？

​	eclipse.ini中的`--launcher.`开头的参数，都是eclipse.exe的去读取并处理的，像这个`--launcher.XXMaxPermSize`就是eclipse启动的时候，读取这个参数的值，判断一下当前的jvm是否是sun公司的HotSpot，如果是则启动jvm时追加参数`XX:MaxPermSize`。为什么还要判断一下jvm的型号？是因为当时其他的jvm虚拟机还没提供永久带设置的参数,如果直接给jvm加上这个参数，那肯定是不行滴。

​	而到了2009年，Oracle收购了sun公司，并将jvm中的公司信息由sun变成oracle，这就导致了09年往后的jvm，老版eclipse是判断不出是不是sun公司的，也就不会给jvm追加`XX:MaxPermSize`参数，那也就是说我们给eclipse一个自定义的永久带的期望就破灭了，不过没关系，目前的jvm都已经提供`XX:MaxPermSize`参数了，所以我们大可以再eclipse.ini里追加一条`-XX:MaxPermSize`来直接给jvm追加永久带参数。

​	懂了吧？