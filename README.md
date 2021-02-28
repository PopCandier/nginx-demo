# nginx-demo

nginx 的出现，轻松解决了C10K的的问题，单机性能可以达到5W左右的并发，和Apache一样，虽然都是属于Http Server，但是nginx能支持的协议会更加丰富，也支持SMTP，POP3和IMAP协议。

HttpServer，WebServer，Application Server。Tomcat是Application Server，一般用于为应用服务器(Appcalition Server) 用来存放运行系统程序的服务器，负责处理程序中的业务逻辑，如Tomcat，Weblogic，jboss（现在大多数应用服务器也包含了Web 服务器的功能）

Http Server，http 服务器 一般用来访问静态的资源，而应用服务器可以动态地生成资源内容，比如Java的Servlet。所以nginx是一台httpserver，用来访问静态的资源。

nginx也可以通过模块开发来提供应用功能，Tomcat也可以直接提供Http服务，通常用在内网和不需要流控等小型服务场景（小型的应用服务器，不追求性能的业务，用不到nginx）

因为http服务器泡在应用服务器中，例如tomcat是也是一台http服务器，springboot也内嵌的tomcat，因为http服务器跑在应用服务器的前面，所以也叫代理服务器。

一般来说Http服务器和应用服务器是一起使用的，比如运行多个tomcat，通过Nginx实现负载均衡，或者把静态资源放在nginx，动态资源放在tomcat，实现动静分离。

![1614398707411](./img/1614398707411.png)

