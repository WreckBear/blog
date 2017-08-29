# Tomcat学习笔记

>  Tomcat是一个WEB服务器！

我们先只来看一下一般的WEB服务器，既然是WEB服务器，那就一定会实现三件事：

* 接受用户发送的HTTP数据包
* 根据用户的请求信息处理并
* 返回给用户处理后的数据

而基于java实现的软件，想监听HTTP请求就必须用ServerSocket的accept()方法监听TCP连接；而监听请求后，得到用户的请求数据，怎么根据请求数据进行处理，最后把数据发送回客户端，也是必须实现的。我们自己很容易实现一个low版的WEB服务器：

* 首先，新建一个连接类Connector，在类的start()方法中，用new ServerSocket(port).accept()监听TCP连接
* 新建解析类Processor，在parse(Scoket)方法中对第一步得到的Scoket进行解析,得出Request,Response对象
* 新建处理类Handler，在handlerRequest(Request,Response)根据请求信息，进行相应的处理，并返回数据

上面提供了一个幼稚版的WEB服务器，当然会存在很多问题，但是确实是可用的；而Tomcat同样的，也为了实现这些功能，但它却用自己的设计做出了实现，下面我就说一下Tomcat的实现思路。

## 1. 基于组件的架构

>  我相信初学者听到类似“Tomcat的架构是基于组件的”话，是一脸懵逼的，至少我是这样...

组件是什么？我不想把组件的定义搬过来，因为这太概念化，过于抽象。

### 1. Tomcat类加载机制

Container获取到Loader是先检查自己是否指定过loader，如果没有则返回父类的Loader，例如：Context容器包含多个Wrapper容器，Wrapper容器获取Loader时其实是拿到的Context的Loader。

### 2.Tomcat生命周期控制机制 

### 3. Tomcat组件思想

Tomcat最大的组件是Server，代表一个服务器。下面是Service，代表一个服务。Service下面是Connector和Container，Connect负责监听用户请求和封装用户请求，最后将request和response交给Container，Container负责运行自定义Servlet。

### 4. Container的管道机制

pipeline是管道的意思，而一根管道上有多个Valva(阀)。

Container持有一个管道，管道持有多个Valve，执行Container的invoke方法，就会执行pipeline的invoke方法，即执行该管道的所有Valve。基础Valve是最后一个执行的Valve，一般会找到Container的下级Container，执行下级Container的invoke。



