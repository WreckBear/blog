# Tomcat学习笔记

### 1. Tomcat类加载机制

Container获取到Loader是先检查自己是否指定过loader，如果没有则返回父类的Loader，例如：Context容器包含多个Wrapper容器，Wrapper容器获取Loader时其实是拿到的Context的Loader。

### 2.Tomcat生命周期控制机制 

### 3. Tomcat组件思想

Tomcat最大的组件是Server，代表一个服务器。下面是Service，代表一个服务。Service下面是Connector和Container，Connect负责监听用户请求和封装用户请求，最后将request和response交给Container，Container负责运行自定义Servlet。

### 4. Container的管道机制

pipeline是管道的意思，而一根管道上有多个Valva(阀)。

Container持有一个管道，管道持有多个Valve，执行Container的invoke方法，就会执行pipeline的invoke方法，即执行该管道的所有Valve。基础Valve是最后一个执行的Valve，一般会找到Container的下级Container，执行下级Container的invoke。



