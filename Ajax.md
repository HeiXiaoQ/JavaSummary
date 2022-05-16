# Ajax&json
## 1、什么是Ajax？
Ajax是异步传输技术。可以实现**部分页面的加载更新**，不用像以前那样加载整张页面。
    应用场景：前端表单的校验、省市县三级联动等等。
## 2、JS原生的Ajax
**代码实现**
JS代码：
```
    loadDoc()
    function loadDoc() {
        //创建XMLHttpRequest对象（Ajax引擎对象）
        var xhttp = new XMLHttpRequest();
        //绑定监听  引擎一旦发生改变就会触发
        xhttp.onreadystatechange = function() {

            if (this.readyState == 4 && this.status == 200) {
                //满足条件就会执行 类似于成功回调方法
                alert(xhttp.responseText)
            }
        };
        //绑定提交地址  第三个参数是 是否为异步
        xhttp.open("GET", "/Servlet", true);
        //发送请求
        xhttp.send();
    }
```
Servlet代码：
```
@WebServlet("/Servlet")
public class Servlet2 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
       response.getWriter().write("hello ajax");
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request, response);
    }
}
```
**Ajax工作原理**

<img src="https://shopping-mlk.oss-cn-beijing.aliyuncs.com/JavaNote/Ajax/Ajax%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.jpg"  >
## 3、JQuery框架的Ajax
使用此方法的条件：需要导入JQuery的JS文件
**原始的Ajax请求**
JS代码：
```
   $(function () {
       $.ajax({
           url:"/Servlet",
           data:"username=tom",
           Type:"GET",
           dataType:"text",
           success:function (data) {
               alert(data)
           },
           error:function () {
               alert("Ajax失败")
           }
       })
   })
```
Servlet的代码同上！

**POST请求**
JS代码：
```
   $(function () {
       $.post("/Servlet21",{username:"tom"},function (date) {
                alert(date)
       })
   })
```
post方法的参数：
- 第一个参数：url路径
- 第二个参数：需要发送的数据（json格式）
- 第三个参数：成功方法

**GET请求**
JS代码：
```
   $(function () {
       $.get("/Servlet21",{username:"tom"},function (date) {
                alert(date)
       })
   })
```
get方法的参数定义，参照post方法即可
## 4、axios框架的Ajax
使用此种方法，需要导入axios的JS文件。
**Ajax请求的通用方式**
```
    axios({
        method:"get",
        url:"/Servlet21?username=tom"
    }).then(function (response) {
        alert(response.data)
    })
```
then可以理解为success回调函数

**POST/GET的请求方式**
```
    axios.get("/Servlet?username=tom").then(function (response) {
        alert(response.data)
    })

    axios.post("/Servlet",{"username":"tom"}).then(function (response) {
        alert(response.data)
    })
```

## 5、json数据格式
>json的格式为：{"":""},可以存储数组、对象和基本数据类型的数据。

**json的解析**
Ajax请求发送至后端，如果是json格式的数据，request.getParameter不能直接获取数据，需要解析响应体，将json解析后才能获取到。**所以axios的POST/GET请求方式代码，数据获取的均是null。**
json解析步骤：（这里使用的是fastjson）
- 导入依赖：fastjson
- 进行解析（**重要**）
  ```
        // 响应数据时，需要将数据转换成JSON格式
        String string = JSON.toJSONString(lists);
        // 获取前端传来的数据时，如果数据格式是json 需要获取请求行第一行 并且将JSON对象进行转换成类对象
        String s = request.getReader().readLine();
        Tree tree = JSON.parseObject(s, Tree.class);
  ```

















