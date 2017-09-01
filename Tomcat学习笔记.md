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





## ===============================================

## 思路整理，想到啥写啥

### Tomcat连接器组件

Tomcat中两大核心组件：Connector和Container。这里说一下Connector，即连接器。

连接器是负责监听并接受用户的请求，将用户的请求信息转换成Container容器类可以处理的信息对象，即HttpServletRequest接口的实现类形式，并将信息传递给Container，最后再返回给用户信息。

先说第一点，连接器需要监听用户的请求。在java中监听用户的TCP请求很简单，用ServerSocket.accept()方法即可，但这里存在一个问题，即同时多用户来请求，如果第一个请求被监听到，被交给Container去处理，如果这个Container处理不完，那ServerSocket.accept()就不会再次执行，去监听下一个用户的请求，所以此处要引入多线程，当一个请求过来后，将请求建立的Socket用新的线程去处理，这样代码就可以继续回到accept()继续监听下一个请求。

第二点，连接器转换用户请求信息。Tomcat用HttpProcessor处理socket信息，将Socket转换为Request，所以连接器接收到socket之后，便会开启新的线程调用HttpProssor.process(socket)处理请求，这个地方Tomcat用了线程池的概念，Tomcat开启时会先初始化一定数量的线程，连接器获得HttpProceesor的时候，不是new来获得对象，而是createProcessor()，此方法中去线程池中去获得线程来节省性能和时间。

### 日志记录器组件

###  Session管理器组件

用户和服务器交互，服务器需要记住用户的身份，因此引入session会话机制。session在J2EE规范中的接口是javax.servlet.HttpSeesion,其中有addAttribute()和removeAttribute()等方法，session对象会被servlet开发者所使用。

Tomcat实现原理和Request一致，首先Tomcat内部自己有一个Seesion接口，而StandardSession即实现了HttpSeesion又实现了Session接口，而session暴露给外面的是StandardSessionFacede门面类。

而Seesion是由SeesionManager管理的，Tomcat对于SessionManager有一个接口，是Manger，同样，Manager的实现类Tomcat提供了一个父类ManagerBase,此类中实现了大量的可复用方法供子类使用，ManagerBase有两个子类，一个是StandardManager和PresistenseManager，前者是标准的session管理器，后者是持久化session管理器。Tomcat支持关闭的时候session持久化到某个文件或者数据库，再次启动的时候再回复session，分别靠的是Manager接口的unLoade()和load()方法。

一个session管理器是和一个Context容器关联的。

### Tomcat加载组件

serlvet容器需要加载自己项目的类，为了隔离不同项目的类，不同项目需要不同的类加载器以实现隔离。Tomcat的加载器接口为WebAppLoader接口，WebAppLoader接口持有一个WebClassloader实例。

WebClassLoader除了有加载类的使命，还有优化的一点细节。对于已经加载过的类，WebClassLoader缓存起来，其实其父类URIClassLoader已经实现此功能。因为CLassLoader将加载路径repository下的类文件看为资源，所以需要缓存起来的类对象都存放在变量名为resouces的变量中，并且还记录了加载失败的类的列表，如果以后在此加载此类，则放弃加载此类；

WebClassLoader还有安全需求，它的内部持有一个变量，记录着不许从项目中加载的类的名称，如：javax.Servlet.Servlet，如果WEbClassloader加载了一个名为javax.Servlet.Servlet的类，则放弃加载，因为这会引发安全问题。

Tomcat支持实时重载，就是监控某个项目底下的资源文件，如果有改动，则重新加载这个项目。那么是怎么实现的呢？首先，WebClassLoader下有个modified()方法，用来返回WebClassLoader加载路径下的文件有没有改动，然后WebLoader中，单独开启了一个线程，不断的去调用modified()方法，以此来判断是否是有文件改动了，如果有改用，则调用WebLoader对应的Context容器的reload()方法。reload()方法中用stop()和start()方法，对整个Context重新开启来实现重载。

Container获取到Loader是先检查自己是否指定过loader，如果没有则返回父类的Loader，例如：Context容器包含多个Wrapper容器，Wrapper容器获取Loader时其实是拿到的Context的Loader。

### Tomcat生命周期控制机制 

Tomcat的各个组件有包含关系，父容器启动的时候要带动子容器启动，如此往复；同样，父容器要关闭的时候，也要先把子容器关闭。

一个组件的开启、暂停、结束可以视为这个组件的生命周期的不同阶段，而组件之间要想控制彼此的生命周期，必须要实现一定的接口，统一规范。这个接口就是LifeCyle。

LifeCyle最核心的接口方法就是start()和stop()，对应生命周期的开始和结束。所有纳入生命周期管理的组件或类都要实现这个接口，以方便父组件或者其他组件可以控制你的生命周期。这个是很简单的，我相信都能看懂。下面说一个LifeCyle的另一个接口方法——AddLifecycleListener()方法。

当一个组件或者类触发了strat()方法或者stop()方法，在真正执行组件的开启动作之前或之后，或许我们有需求去做一些额外的工作，比如校验工作，所以提供一种监听机制还是很有必要的，我们可以监听生命周期的方法，来保证当生命周期的某个方法被触发的时候我们可以监听得到，如果需要在方法之前或者之后做一些事情，那么我们就在之前或者之后，触发监听事件来达到目的。

坚挺机制分为事件源、事件和监听器三个要素。事件源就是LifeCycle的实现类，因为按理应该是实现类触发的某个生命周期方法，事件才发生了。

而事件监听器就是事件发生了之后，要怎么处理的类。这里会引入一个接口——LifecycleListener，这个接口只有一个方法lifecycleEvent()，这个方法是当事件发生时该方法就会触发，并会被传入一个事件对象参数，此方法实现对事件发生后的处理逻辑。那么对应的事件对象就是LifecycleEvent,继承自ObjectEvent。

Tomcat提供了一个LifeCyleSupport类，提供了几个方法工子类使用，首先是LifeCycle接口中的addLifeCycleListener()方法的默认实现，通过持有一个LifeCycleListener数组，来记录该类有的listenner，添加删除都是对这个数组进行操作；再一个就是fireLifecycleEvent()方法，此方法触发listenner数组的所有监听器的lifecycleEvent()方法。

### Tomcat组件思想

Tomcat最大的组件是Server，代表一个服务器。下面是Service，代表一个服务。Service下面是Connector和Container，Connect负责监听用户请求和封装用户请求，最后将request和response交给Container，Container负责运行自定义Servlet。

### Container的管道机制

pipeline是管道的意思，而一根管道上有多个Valva(阀)。

Container持有一个管道，管道持有多个Valve，执行Container的invoke方法，就会执行pipeline的invoke方法，即执行该管道的所有Valve。基础Valve是最后一个执行的Valve，一般会找到Container的下级Container，执行下级Container的invoke。



