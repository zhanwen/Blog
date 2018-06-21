---
title: Servlet API
date: 2018-06-21 23:27:43
tags:
	- Java
	- Servlet
	- API
---
`Sevelt` 的框架的核心是 `javax.servlet.Servlet`接口，所有的`Servlet`都必须实现这一接口。在`Servlet`接口定义了五个方法，其中有三个方法代表了`Servlet`的生命周期：  

	init	方法：负责初始化Servlet对象
	service	方法：辅助响应客户的请求
	destroy	方法：当Servlet对象退出生命周期时，负责释放占用的资源

如果你的`Servlet`类扩展了`HttpServlet`类，你通常不必实现`service`方法，因为`HttpServlet`类已经实现了`service`方法，该方法的声明形式如下：  

	protected void service(HttpServletRequest request,HttpServletResponse response)throws ServletException, IOEception;

在`HttpServlet`的`service`方法中，首先从`HttpServletRequest`对象中获取`HTTP`请求方式的信息，然后在根据请求方式调用相应的方法。例如：如果请求方式为`GET`，那么调用`doGet`方法：如果请求方式为`POST`，那么调用`doPost`方法。

在`servlet`中为什么可以直接调用`req`，`resp`的相应方法：多态，父类型的引用，可以指向子类的对象。

`Servlet`的生命周期可以分为三个阶段：  
1、初始化阶段  
	
      —servlet容器启动时自动装载某些Servlet
      —在servlet容器启动后，客户首先向Servlet发出请求
      —Servlet类文件被更新后，重新转载Servlet
      —Servlet被装载后，Servlet容器创建一个Servlet实例并且调用Servlet的init（）方法进行初始化。在Servlet的整个生命周期中，init方法只会被调用一次。
2、响应客户请求阶段  
对于到达`Servlet`容器的客户请求，`Servlet`容器创建特定于这个请求的`ServletRequest`对象和`ServletResponse`对象，然后调用`Servlet`的`service`方法，`service`方法从`ServletRequest`对象获得客户请求信息、处理该请求，并通过`ServletResponse`对象相客户返回响应结果。  

3、终止阶段  
当`web`应用被终止，或`Servlet`容器终止运行，或`Servlet`容器重新装载`Servlet`的新实例时，`Servlet`容器先调用`Servlet`的`destroy`方法，在`destroy`方法中，可以释放`Servlet`所占用的资源。

在`javax.servlet.Servlet`接口中定义了三个方法
	
	init()
	service()
	destroy()

他们将分别在`Servlet`的不同阶段被调用。


