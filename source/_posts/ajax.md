---
title: Ajax
date: 2021-07-16 16:21:29
tags: 
categories: 码
---



学习Ajax过程的一些笔记。



<!--more-->



# Ajax

### 是什么

- - A-Asynchronous异步

  - J-JavaScript   

  - A-And   

  - X-Xml

  - Xml是指标准的格式规范，Json是Xml里面其中一种格式规范（Key-Value）。Json是Xml体系中的一种。

    通过form表单提交 改为 JavaScript

    xml用于封装数据

- 局部刷新技术，避免浪费带宽资源。例如 若页面只发生小部分变化，却要将整个页面重新加载一次。

  是Web2.0提出的一种技术，现在已经是Web3.0-4.0了

- 核心是XMLHttpRequest 用于请求到服务器并获得返回结果并调用回调函数

- `ajax`是一种技术方案，但并不是一种**新技术**。它依赖的是现有的`CSS`/`HTML`/`Javascript`，而其中最核心的依赖是浏览器提供的`XMLHttpRequest`对象，是这个对象使得浏览器可以发出`HTTP`请求与接收`HTTP`响应。-----  [你真的会使用XMLHttpRequest吗？](https://segmentfault.com/a/1190000004322487)

### 怎么用

- 步骤
  1. 需要一个javascript方法 用来启动ajax
  2. 在这个方法内判断浏览器是否支持XMLHttpRequest，**因为需要浏览器和XMLHttpRequest对象传递数据.**
  3. 如果支持，则new出XMLHttpRequest对象
  4. 设置XMLHttpRequest对象的参数 包括需要传递到服务器的参数，传递方式，回调函数（从服务器接受到数据库后调用的方法）等。。
  5. 写回调函数（回调方法）
- ajax不能使用转发和重定向进行页面跳转 这会使整个页面刷新，只能通过PrintWriter输出内容
- 通过status和readystate判断XMLHttpRequest是否获得服务器回传的数据

#### XMLHttpRequest常用方法与属性

- **JavaScript对象XMLHttpRequest是整个Ajax技术的核心，它提供了异步发送请求的能力**

- open(method,URL,async) ：建立与服务器的连接，method参数指定请求的HTTP方法，典型的值是GET或POSTURL参数指定请求的地址，async参数指定是否使用异步请求，其值为true（异步）或false（同步）。正常情况都选true 不然用个啥ajax
- send(content) ：发送请求content参数指定请求的参数
- setRequestHeader(header,value) ：如果采用post就要设置请求的头信息
- onreadystatechange：指定回调函数（事件)
- **readyState**
  - 0：XMLHttpRequest对象没有完成初始化
  - 1：XMLHttpRequest对象开始发送请求
  - 2：XMLHttpRequest对象的请求发送完成
  - 3：XMLHttpRequest对象开始读取响应，还没有结束
  - 4：XMLHttpRequest**对象读取响应结束**
- **status**
  - 200：**服务器响应正常**
  - 400：无法找到请求的资源
  - 403：没有访问权限
  - 500：服务器内部错误

- responseText：获得响应的文本内容 
- responseXML：获得响应的XML文档对象
- **就绪状态是4而且状态码是200，才可以处理服务器数据**

### 第一个例子

判断用户名是否已被注册

register.jsp

```jsp
<html>
<head>
    <title>AjaxDemo</title>
    <script type="text/javascript">
        var httpRequest = null;
        function checkName(oval){
            if(oval==""){
                return;
            }
            try {
                httpRequest = new ActiveXObject("Msxml2.XMLHTTP");
            } catch (e) {
                try {
                    httpRequest = new ActiveXObject("Microsoft.XMLHTTP");
                } catch (e) {
                    try {
                        httpRequest = new XMLHttpRequest();
                        if (httpRequest.overrideMimeType) {
                            httpRequest.overrideMimeType("text/xml");
                        }
                    } catch (e) {
                    }
                }
            }
            if (httpRequest == null) {
                alert("浏览器不支持XMLHttpRequest");
                return;
            }

            httpRequest.open("post", "UserServlet", true); //传递方式,传递的地址,是否开启异步

            httpRequest.setRequestHeader("Content-type","application/x-www-form-urlencoded"); //头文件 键值对

            httpRequest.onreadystatechange = callBack;//是下面回调函数的名字,不要加括号

            httpRequest.send("uname=" + oval); //将要传递的参数传递到后端
        }
        function callBack(){
            if (httpRequest.readyState==4 && httpRequest.status==200){
                document.getElementById("namespan").innerHTML=httpRequest.responseText;
            }
        }

    </script>
</head>
<body>
<form action="RegisterServlet" method="post" enctype="application/x-www-form-urlencoded">
    <p>姓名：<input type="text" onblur="checkName(this.value)"/>
        <span id="namespan">请输入用户名</span>
    </p>
    <p>密码：<input type="password"/></p>
    <p><input type="submit" value="提交"></p>
</form>
</body>
</html>
```

UserServlet

```java
@WebServlet(name = "UserServlet", value = "/UserServlet")
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
//        request.setCharacterEncoding("utf-8");
//        response.setContentType("text/html;charset=utf-8");
//        response.setCharacterEncoding("utf-8");
        HttpSession session=request.getSession();
        ServletContext application=getServletContext();
        UserBiz userBiz=new UserBizImpl();
        String uname=request.getParameter("uname");
        boolean flag=userBiz.checkName(uname);
        
        PrintWriter out=response.getWriter();
        out.print("<h1>你好,Darkra1</h1>");
        out.print(flag?"用户名可用":"用户名已存在");//可用1
        out.flush();
        out.close();
    }
}
```

。。感觉前端也得补补了

onblur 事件会在对象失去焦点时发生。

上一个例子实现了使用ajax传递简单参数，传递的方式是键值对匹配，但当参数多起来时就不方便传递

### Ajax传递List

实现ajax 前端向后端传递List 并在后端用Gson将List转为Json格式的字符串回传到前端

两种方式实现：

第一种是直接传，但是这样需要在前端接收回传过来的数据时 重新进行拼接form标签之类的 不太方便。

第二种在中间增加一个页面作为中转站，把List转发到一个新的页面 在页面内形成一个form表单 可以带css样式，再将这个页面作为一个整体回传到前端。

但是这里没有使用out.print作为输出方式   不太懂为什么不用out.print也能实现。埋个坑。

empdept.jsp

```jsp
<html>
<head>
    <base href="<%=basePath%>">
    <title>Title</title>
    <script type="text/javascript">
        var httpRequest = null;
        function getemp(obj) {
            try {
                httpRequest = new ActiveXObject("Msxml2.XMLHTTP");
            } catch (e) {
                try {
                    httpRequest = new ActiveXObject("Microsoft.XMLHTTP");
                } catch (e) {
                    try {
                        httpRequest = new XMLHttpRequest();
                        if (httpRequest.overrideMimeType) {
                            httpRequest.overrideMimeType("text/xml");
                        }
                    } catch (e) {
                    }
                }
            }
            if (httpRequest == null) {
                alert("浏览器不支持XMLHttpRequest");
                return;
            }
            httpRequest.open("post", "EmpDeptServlet", true); //传递方式,传递的地址,是否开启异步
            httpRequest.setRequestHeader("Content-type", "application/x-www-form-urlencoded"); //头文件 键值对
            httpRequest.onreadystatechange = getempback;//是下面回调函数的名字,不要加括号
            httpRequest.send("from=emp&did="+obj.value); //将要传递的参数传递到后端
        }

        function getempback() {
            if (httpRequest.readyState == 4 && httpRequest.status == 200) {
                var object = httpRequest.responseText;
                object=JSON.parse(object);
                var str="";
                for (var i=0;i<object.length;i++){
                    str+="编号"+object[i].eid+",姓名："+object[i].ename+"<br/>";
                }
                document.getElementById("mydiv").innerHTML =str;

            }
        }

    </script>
</head>
<body>
    部门：<select id="dep" onchange="getemp(this)">
        <c:forEach var="d" items="${deptList}">
            <option value="${d.did}">${d.dname}</option>
        </c:forEach>
        </select>
        <div id="mydiv"></div>
</body>
</html>
```

empdept2.jsp

将empdept.jsp中的getempback方法改成下面这样

```jsp
function getempback() {
    if (httpRequest.readyState == 4 && httpRequest.status == 200) {
        var object = httpRequest.responseText;
        document.getElementById("mydiv").innerHTML =object;
    }
}
```

httpRequest.send方法改成下面这样

```
httpRequest.send("from=emp2&did="+obj.value); //将要传递的参数传递到后端
```

empdept2_emp.jsp

```jsp
<html>
<head>
    <base href="<%=basePath%>">
    <title>Title</title>
    <style type="text/css">
        table{
            border: aqua;
        }
        td{
            background: dodgerblue;
        }
    </style>
</head>
<body>
<table>
    <tr>
        <td>编号</td>
        <td>姓名</td>
    </tr>
    <c:forEach items="${empList}" var="e">
        <tr>
            <td>${e.eid}</td>
            <td>${e.ename}</td>
        </tr>
    </c:forEach>
</table>
</body>
</html>
```

EmpDepServlet.java

```java
@WebServlet(name = "EmpDeptServlet", value = "/EmpDeptServlet")
public class EmpDeptServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String from=request.getParameter("from");
        if ("dept".equals(from)){
            List<Dept> deptList=new ArrayList<>();
            deptList.add(new Dept(1,"技术部"));
            deptList.add(new Dept(2,"人事部"));
            request.setAttribute("deptList",deptList);
            request.getRequestDispatcher("empdept2.jsp").forward(request,response); //empdept.jsp 拼接table返回。  empdept2.jsp 跳转到新页面整个table返回。
        }else if ("emp".equals(from)){
            PrintWriter out=response.getWriter();
            int did=Integer.valueOf(request.getParameter("did"));
            System.out.println(did);
            List<Emp> empList=new ArrayList<>();
            if (did==1){
                empList.add(new Emp(1,"张工程师"));
                empList.add(new Emp(2,"陈工程师"));
                empList.add(new Emp(3,"王工程师"));
                empList.add(new Emp(4,"李工程师"));
            }else {
                empList.add(new Emp(1,"张经理"));
                empList.add(new Emp(2,"陈经理"));
                empList.add(new Emp(3,"王经理"));
                empList.add(new Emp(4,"李经理"));
            }
            Gson gson=new Gson();
            String str=gson.toJson(empList);
            System.out.println(str);
            out.print(str);
            out.flush();
            out.close();
        }else if ("emp2".equals(from)){
            int did=Integer.valueOf(request.getParameter("did"));
            System.out.println(did);
            List<Emp> empList=new ArrayList<>();
            if (did==1){
                empList.add(new Emp(1,"张工程师"));
                empList.add(new Emp(2,"陈工程师"));
                empList.add(new Emp(3,"王工程师"));
                empList.add(new Emp(4,"李工程师"));
            }else {
                empList.add(new Emp(1,"张经理"));
                empList.add(new Emp(2,"陈经理"));
                empList.add(new Emp(3,"王经理"));
                empList.add(new Emp(4,"李经理"));
            }
            request.setAttribute("empList",empList);
            request.getRequestDispatcher("empdept2_emp.jsp").forward(request,response);
        }
    }
}
```

第一种方式：

![image-20210716152252983](image-20210716152252983.png)

第二种方式：

![image-20210716152213463](image-20210716152213463.png)

第二种方式更好用  方便做样式。

---------



### jQuery-Ajax

**上面使用ajax的方式现在已经没人用了  单纯为了介绍ajax原理。**

**下面介绍jQuery封装好的ajax 更方便使用**

![image-20210716153041644](image-20210716153041644.png)

jquery-ajax请求一共有以上几种方法

第一个**$.ajax**是万能用法。可以满足所有场景的使用 且比其他方法多了success和error情况。掌握这个就够用了 其他的了解了解即可

第二个**load**用法只能将回调得到的参数直接显示在某个区域，不太好用。

![image-20210715172845275](image-20210715172845275.png)![image-20210715172851224](image-20210715172851224.png)

第三个$.get和第六个$.post用法差不多  区别在于一个是get请求 一个是post请求。

第四种$.getJSON和$.get的区别在于 它已经将Json格式转化为对象 可以省去data=JSON.parse(data);这一步 

第五种没啥用。

注：$.ajax中error方法的参数，可以通过alert(XMLHttpRequest.status + "----" + textStatus);打印错误信息

```
function (XMLHttpRequest, textStatus, errorThrown) {
    // 通常 textStatus 和 errorThrown 之中
    // 只有一个会包含信息
    this; // 调用本次AJAX请求时传递的options参数
}
```

实际用法看下面↓↓↓

#### 参数传递

multiParameter.jsp

```jsp
<html>
<head>
    <base href="<%=basePath%>">
    <title>Title</title>
    <script type="text/javascript" src="http://lib.sinaapp.com/js/jquery/2.0.3/jquery-2.0.3.min.js"></script>
    <script type="text/javascript">
        function getData(object) {
            $.get("MultiParaServlet", {name: object, from: "case1"}, function (data) {
                var strs = data.split("!");
                $('#s1').html(strs[0]);
                $('#s2').html(strs[1]);
            });
        }

        function getData2(object) {
            $.get("MultiParaServlet", {name: object, from: "case2"}, function (data) {
                data = JSON.parse(data);
                $('#s3').html(data.name);
                $('#s4').html(data.password);
            });
        }

        function getData3(object) {
            $.getJSON("MultiParaServlet", {name: object, from: "case3"}, function (data) {
                // data=JSON.parse(data);
                $('#s5').html(data.name);
                $('#s6').html(data.password);
            });
        }

        function getBtn() {
            $.ajax({
                url: "MultiParaServlet",
                type: "get",
                data: {name: "aa", from: "case4"},
                success: function (data) {
                    alert(typeof (data));
                    alert(data);
                },
                error: function (XMLHttpRequest, textStatus) {
                    alert(XMLHttpRequest.status + "----" + textStatus);
                }
            })
        }

        function postBtn() {
            $.ajax({
                url: "MultiParaServlet",
                type: "post",
                data: {name: "aa", from: "case4"},
                success: function (data) {
                    alert(typeof (data));
                    alert(data);
                },
                error: function (XMLHttpRequest, textStatus) {
                    alert(XMLHttpRequest.status + "----" + textStatus);
                }
            })
        }

        function postBtn2() {
            $.ajax({
                url: "MultiParaServlet",
                type: "post",
                data: {name: "aa", from: "case4"},
                dataType: "json",
                success: function (data) {
                    alert(typeof (data));
                    alert(data);
                    alert(data.name+"---"+data.password);
                },
                error: function (XMLHttpRequest, textStatus) {
                    alert(XMLHttpRequest.status + "----" + textStatus);
                }
            })
        }


    </script>
</head>
<body>
(用于测试$.ajax)
<input type="button" value="get请求" onclick="getBtn()"/>
<input type="button" value="post请求" onclick="postBtn()"/>
<input type="button" value="post请求Json格式" onclick="postBtn2()"/>
<br/>
<br/>
<br/>
<br/>
(用于测试$.get 分割多参数进行传递)
<br/>
请输入用户名aa或bb:
<input type="text" onblur="getData(this.value)"/>
<br/>区域1：
<span id="s1"></span>
<br/>区域2：
<span id="s2"></span>
<br/>
<br/>
<br/>
<br/>
(用于测试$.get+JSON.parse传递对象)
<br/>
请输入用户名aa或bb:
<input type="text" onblur="getData2(this.value)"/>
<br/>区域3：
<span id="s3"></span>
<br/>区域4：
<span id="s4"></span>
<br/>
<br/>
<br/>
<br/>
(用于测试$.getJSON 传递对象)
<br/>
请输入用户名aa或bb:
<input type="text" onblur="getData3(this.value)"/>
<br/>区域5：
<span id="s5"></span>
<br/>区域6：
<span id="s6"></span>
</body>
</html>
```

MultiParaServlet.java

```java
@WebServlet(name = "MultiParaServlet", value = "/MultiParaServlet")
public class MultiParaServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String from = request.getParameter("from");
        String name = request.getParameter("name");
        PrintWriter out = response.getWriter();
        if ("case1".equals(from)) {
            if ("aa".equals(name)) {
                out.print("aa!男");
            }
            if ("bb".equals(name)) {
                out.print("bb!女");
            }
        } else if ("case2".equals(from)){
            User user=new User();
            if ("aa".equals(name)){
                user.setName("测试get1");
                user.setPassword("123");
            } if ("bb".equals(name)){
                user.setName("测试get2");
                user.setPassword("456");
            }
            Gson gson=new Gson();
            String str=gson.toJson(user);
            out.print(str);
        }
        else if ("case3".equals(from)){
            User user=new User();
            if ("aa".equals(name)){
                user.setName("测试a");
                user.setPassword("123");
            } if ("bb".equals(name)){
                user.setName("测试b");
                user.setPassword("456");
            }
            Gson gson=new Gson();
            String str=gson.toJson(user);
            out.print(str);
        }
        else if ("case4".equals(from)){
            User user=new User();
            if ("aa".equals(name)){
                user.setName("测试1");
                user.setPassword("123");
            } if ("bb".equals(name)){
                user.setName("测试2");
                user.setPassword("456");
            }
            Gson gson=new Gson();
            String str=gson.toJson(user);
            out.print(str);
        }
    }
}
```

这个例子介绍的都是简单的传递参数方式，实际开发过程中需要传递的参数比较复杂，可能是表单，文件，对象。。。 下面介绍开发时常用的传值方式。

### 前端向后端传值

#### 普通jQuery-ajax方式

frontToBack.jsp

```jsp
<html>
<head>
    <base href="<%=basePath%>">
    <title>AjaxDemo</title>
    <script  src="js/jquery-3.2.1.js"></script>
    <script type="text/javascript">
        function send(){
            var object=new Object();
            object.name=$('#name').val();
            object.age=$('#age').val();
            object.password=$('#password').val();
            var str=JSON.stringify(object); //把对象转换成JSON格式的字符串
            $.post("FrontToBackServlet?method=send",{user:str},function (data){
               alert(data);
            });
        }
        function send2(){
            var list=[];
            for(var i=0;i<3;i++){
                var object=new Object();
                object.name="姓名"+i;
                object.age=10+i;
                object.password="密码"+i;
                list.push(object);
            }
            var str=JSON.stringify(list); //把对象转换成JSON格式的字符串
            $.post("FrontToBackServlet?method=send2",{users:str},function (data){
                alert(data);
            });
        }
        function send3(){
            var object=new Object();
            object.name=$('#name').val();
            object.age=$('#age').val();
            object.password=$('#password').val();
            var str=JSON.stringify({
                "user":object,
                "msg":"testMap",
                "num":99
            });
            alert(str);
            $.post("FrontToBackServlet?method=send3",{str:str},function (data){
                data=JSON.parse(data);
                alert(data);
                $('#s').html(data.name);
            });
        }
    </script>
</head>
<body>
<p>姓名：<input type="text" id="name"/></p>
<p>年龄：<input type="text" id="age"/></p>
<p>密码：<input type="password" id="password"/></p>
<p><input type="button" value="发送数据" onclick="send()"/></p>
<p><input type="button" value="发送数组" onclick="send2()"/></p>
<p><input type="button" value="发送Map" onclick="send3()"/></p>
<span id="s"></span>
</body>
</html>
```

FrontToBackServlet.java

```java
@WebServlet(name = "FrontToBackServlet", value = "/FrontToBackServlet")
public class FrontToBackServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String method = request.getParameter("method");
        if ("send".equals(method)) {
            this.send(request, response);
        }if ("send2".equals(method)) {
            this.send2(request, response);
        }if ("send3".equals(method)) {
            this.send3(request, response);
        }
    }
    private void send(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
        String str=request.getParameter("user");
        Gson gson=new Gson();
//        gson.toJson(对象)把对象转换成字符串传到前端
        User user=gson.fromJson(str,User.class); //将字符串转化为对象
        System.out.println(user);
        PrintWriter out=response.getWriter();
        out.print("后端接收到了，并返回"+user.getName());
        out.flush();
        out.close();
    }
    private void send2(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
        String str=request.getParameter("users");
        Gson gson=new Gson();
//        gson.toJson(对象)把对象转换成字符串传到前端

        List<User> users=gson.fromJson(str,new TypeToken<List<User>>(){}.getType());
        for (User u:users){
            System.out.println(u);
        }
        PrintWriter out=response.getWriter();
        out.print("后端接收到了，并返回"+users.size());
        out.flush();
        out.close();
    }
    private void send3(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
        String str=request.getParameter("str");
        Gson gson=new Gson();
//        gson.toJson(对象)把对象转换成字符串传到前端
        Map<String,Object> map=gson.fromJson(str,new TypeToken<Map<String,Object>>(){}.getType());
        System.out.println(map.get("num"));
        System.out.println(map.get("msg"));
        User user=gson.fromJson(map.get("user").toString(),User.class); //这里报错了
        System.out.println(user);
        String u=gson.toJson(user);
        System.out.println("打印user："+u);
        PrintWriter out=response.getWriter();
        out.print("copy that,return"+u); //接收不到u的值
        out.flush();
        out.close();
    }
}
```

最后这个send3 有报错 一个坑，待填。

#### 表单序列化serialize

ref [ajax提交form表单 jQuery ajax - serialize() 方法](https://blog.csdn.net/mao1055229269/article/details/18260497)

```java
<head>
    <base href="<%=basePath%>">
    <title>serialize测试</title>
    <script type="text/javascript" src="http://lib.sinaapp.com/js/jquery/2.0.3/jquery-2.0.3.min.js"></script>
    <script type="text/javascript">
        function getBtn() {
            $.ajax({
                url: "serialize",
                type: "post",
                data: $('#myForm').serialize(),  //使用表单序列化提交数据
                enctype: 'multipart/form-data',
                success: function (data) {
                    console.log(data);
                    alert(data);
                },
                error: function (err) {
                    alert(err);
                }
            });
        }
    </script>
</head>
<body>
<form id="myForm">
    <p>First name: <input type="text" name="fname"/></p>
    <p>Last name: <input type="text" name="lname"/></p>
    <input type="button" value="表单序列化" onclick="getBtn()" />
</form>
</body>
</html>
```

```java
@WebServlet(name = "SerializeServlet", value = "/serialize")
public class SerializeServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String fname = request.getParameter("fname");
        String lname = request.getParameter("lname");
        System.out.println(fname);
        System.out.println(lname);
        PrintWriter out=response.getWriter();
        out.print(fname+"-----"+lname);
        out.flush();
        out.close();
    }
}
```

这里只说明了简单用法

表单序列化原理埋个坑 以后再说



# JDBC

补补JDBC..

解决java中内存泄漏的最好办法 是谁创建谁消除。所以在每次调用JDBCUtil中的方法时,都会在结束时接着调用closeAll方法

```java
public void closeAll() {
    try {
        if (rs != null)
            rs.close();
        if (ps != null)
            ps.close();
        if (con != null)
            con.close();
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

但发生这种情况时，程序会报错。

### 可能出现的报错

当Dao层方法调用一次JDBCUtil对象内的增删改查方法，且在该Dao层方法内还未进行至第一次调用JDBCUtil.closeAll()方法时，又调用了一次Dao层方法，JDBCUtil对象内的增删改查方法也跟着又被调用了。即嵌套调用JDBCUtil对象内的增删改查方法。此时先进入嵌套内的JDBCUtil.增删改查方法，结束嵌套内方法时才开始第一次调用JDBCUtil.closeAll()。但由于PreparedStatement，ResultSet，Connection是全局变量，所以在第一次调用JDBCUtil.closeAll()时就将这三个全局变量给close了。嵌套内的方法结束后，外层的方法继续执行，但由于JDBCUtil对象内的PreparedStatement，ResultSet，Connection都被嵌套内的方法close了，嵌套外的方法就无法继续执行增删改查操作。

还有一种报错情况就是数据库连接数超过了最大连接数。

不同数据库都有默认的最大连接数，mysql可能是在安装时设置的。

### 解决方案

加个缓存  将查询到的东西先放进缓存 这样rs关就关咯 用缓存了 谁还管rs啊（大概知道怎么回事。。说不清楚。。蒙混过关。。以后再填。。）

ref

[为什么要使用CachedRowSetImpl?](https://www.jianshu.com/p/1c25569b4949)

**单个用户使用前提。什么情况下会使数据库连接数超过数据库最大连接数？**   埋个坑..

![image-20210712214210875](image-20210712214210875.png)





# Servlet

补补Servlet..

### 是什么

- 侠义的Servlet是指Java语言实现的一个接口，广义的Servlet是指任何实现了这个Servlet接口的类，一般情况下，人们将Servlet理解为后者。引自[一篇百度知道回答](https://zhidao.baidu.com/question/157245241.html)

- 是在服务器上运行以处理客户端请求并做出响应的程序。是sun推出的用来在服务器处理HTTP协议的组件。
- Servlet 是一个 Java程序，是在服务器上运行以处理客户端请求并做出响应的程序

### 历史，以及与jsp的关系

- 一篇知乎文章:[浅谈jsp](https://zhuanlan.zhihu.com/p/42343690)

- 大概总结一下：

  servlet是用来帮助程序员更好进行web开发的一些"工具类"。最早没有servlet的时候java和html不好结合在一起。于是出现了servlet帮助java和html结合起来。换句话说，servlet是帮助联系前端和后端的东西。

- 但是servlet还是有一点点的不好用，因为servlet再怎么厉害也是个java类，它虽然能帮助接受和传递前端传来的东西，但不好处理前端的东西，于是出现了servlet升级版，jsp(本质还是servlet，但是可以更方便的处理和接受传递前端传来的信息，很大的原因在于jsp有九大内置对象)。jsp靠服务器生存，因为必须要服务器内的一个专门用来将Jsp解析成servlet的东西才能用jsp。

- Servlet能够很好地组织业务逻辑代码，但是在Java源文件中通过字符串拼接的方式生成动态HTML内容会导致代码维护困难、可读性差

- JSP虽然规避了Servlet在生成HTML内容方面的劣势，但是在HTML中混入大量、复杂的业务逻辑同样也是不可取的。

- jsp更擅长表现于页面显示,servlet更擅长于逻辑控制。

- 所以后来出现了mvc 。

### 什么是jsp

- JSP全名为Java Server Pages
- 在HTML中嵌入Java脚本代码
- 由应用服务器中的JSP引擎来编译和执行嵌入的Java脚本代码
- 然后将生成的整个页面信息返回给客户端
- JSP的本质就是Servlet，JVM只能识别java的类，不能识别JSP的代码,Web容器将JSP的代码编译成JVM能够识别的java类

### JSP九大内置对象

JSP九大内置对象与他们对应的java对象

| servlet     | java                | 作用                                                         |
| ----------- | ------------------- | ------------------------------------------------------------ |
| Request     | HttpServletRequest  | 封装了用户提交的数据(请求)                                   |
| Response    | HttpServletResponse | 封装了要发送给客户端的数据（相应）                           |
| Session     | HttpSession         | 会话，有效期是在浏览器开启到关闭之间在服务器端保存数据       |
| Application | ServletContext      | 有效期是服务器开启到关闭之间，多个用户和浏览器之间可以共享数据 |
| Out         | PrintWriter         | 往客户端输出数据，可以写文字，可以写html标签，可以写js       |
| pageContext | PageContext         | 只在本页面有效，但是由于通过servlet转到到界面，所以pageContext没什么用 |
| Page        | this                | 获取当前页面的对象，默认可以省略不写                         |
| Config      | ServletConfig       | sevlet的配置文件，用于获取ServletContext和web.xml/注解中定义的初始化参数 |
| Exception   | throwable           | 页面中的异常对象，默认不需要写                               |



### 配置error界面

![image-20210714004100463](image-20210714004100463.png)





### 过滤器

过滤器的生命周期分为 

实例化 

初始化 

过滤 

销毁

 在服务器启动的时候自动创建并Init实例化

#### 一个问题：

##### ![image-20210714021917729](image-20210714021917729.png)

只是调整了chain.doFilter(request,response);的执行顺序。

1，2，4都可以起到过滤器的效果 运行不会出现乱码，但是3不行。

未解决。



----------

**Learn from 莫筱婷**

-------------

## Ref

[你不知道的 XMLHttpRequest](https://juejin.cn/post/6844903472714743816)

[XMLHttpRequest—必知必会](https://www.jianshu.com/p/918c63045bc3)

[浏览器之XMLHttpRequest实现机制](https://zhuanlan.zhihu.com/p/350046966)

[Ajax中的XMLHttpRequest对象详解](https://www.cnblogs.com/tianma3798/p/4176904.html)

[AJAX入门这一篇就够了](https://zhuanlan.zhihu.com/p/33809782)

