# Servlet 简单介绍

## 什么是Servlet？

Servlet是一种程序，它可以完成**处理请求和发送响应的过程**。它的出现主要解决了**动态页面加载**的问题。Servlet也是JavaWeb三大组件之一。
用户访问过程是：
![访问过程](https://shopping-mlk.oss-cn-beijing.aliyuncs.com/JavaNote/Servlet/servlet01.jpg)

## Tomcat与Servlet

Tomcat是一个Web服务器和Servlet容器的结合体。它的内部提供了Servlet接口，编写代码的时候只要遵守相关的规范就可以了。
Javaweb项目的访问过程是：**客户端发送请求给tomcat服务器，服务器根据请求路径调用相应的Servlet的service方法。**

## 编写Servlet

导入依赖：

```xml
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
    </dependency>
```

编写Servlet：

```java
@WebServlet("/Servlet")
public class Servlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=utf-8");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        doPost(request, response);
    }
}
```

注意：

- 需要继承HttpServlet类
- 重写doPost、doGet方法
- 使用request、response实现发送请求、响应
- 请求路径需要使用注解 **@WebServlet**

## 如何高效的使用Servlet

按照之前的方法，每一个功能都需要创建一个Servlet的子类，实在太麻烦了。

现在一个更好的解决方案：

- 自定义一个类继承HttpServlet，重写service方法，这个方法的主要功能就是根据请求路径，执行不同的方法（二级请求路径与方法名称相同）
- 自定义一个类继承刚刚的自定义类即可，在类上需要添加一级目录。

**自定义类MyServlet：**

```java
public class MyServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.获取请求路径
        String uri = req.getRequestURI();///brand-case/brand/selectAll
        //2.获取最后一段路径，方法名
        int index = uri.lastIndexOf("/");
        String methodName = uri.substring(index+1);//selectAll
        //3.执行方法
        //3.1获取BrandServlet1 字节码对象 Class
        //谁调用我(this所在的方法),我(this)代表谁 （重要）
        Class<? extends MyServlet> cls = this.getClass();
        //3.2获取方法Method对象
        try {
            Method method = cls.getMethod(methodName,HttpServletRequest.class,HttpServletResponse.class);
            //3.3执行方法
            method.invoke(this,req,resp);
        }catch (NoSuchMethodException e){
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}

```

**Servlet类：**

```java
@WebServlet("/一级目录/*")
public class UserServlet extends MyServlet {
    //编写各种方法即可
}
```

注意：**访问路径是 /一级目录/方法名称；**
