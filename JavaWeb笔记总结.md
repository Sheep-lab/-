# JavaWeb笔记总结

## BS/CS:

CS:（客户端服务器架构模式）

> 优点：充分利用客服端机器的资源，减轻服务器的负荷（一部分安全要求不高的计算任务存储任务放在客户端执行，不需要把所有的计算和存储在服务器端执行，从而能够减轻服务器的压力，也能够减轻网络负荷）
> 缺点：需要安装；升级维护成本较高；

BS:（浏览器服务器架构模式）

> 优点:不需要安装；维护成本较低；
> 缺点：所有的计算和存储任务都放在服务器端，服务器的负荷较重；在服务器计算完成之后把结果再传输给客户端，因此客户端的和服务器会进行非常频繁的数据通信，从而网络负荷较重。



## javaweb整体流程：

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day3-tomcat-servlet\资料\03.第一次使用Servlet.png)

## Tomcat（web服务器）:

### 配置：

1. 部署Tomcat：Run-Edit Configurations-Tomcat server-Local-:Deployment选项卡：添加部署包。server选项卡修改application server(Tomcat安装目录)，URL：写到html（URL的值指的是tomcat启动完成后自动打开指定的浏览器，然后默认访问的网址。）

2. idea中新建项目 - 新建模块（每个模块为一个单独的项目结构）

3. 在模块中添加web

4. Project Structure下创建artifact - 部署包

4. lib（库/jar包） - artifact（部署）
   若先有artifact，后添加在lib中添加jar包（库）：这个jar包并没有添加到部署包中(可在Project Structure中有一个Problems中会有提示的,点击fix选择add to.../或在Project Structure-Artifacts下删除再重新创建部署包)
   
6. lib文件夹一般独立在项目模块外，也可以把lib文件夹直接新建在WEB-INF下。（缺点：lib只能是当前这个moudle独享。其他模块需要再次新建lib。）

5. web.xml文件中<url-pattern>标签中内容以/开头
   
   **注意：**
   
   > 1. 404：找不到指定的资源。如果网址是：http://localhost:8080/项目名称/ , 表明默认访问index.html.可以通过<welcome-file-list>标签进行设置欢迎页(在tomcat的web.xml中设置，或者在自己项目的web.xml中设置)
   > 2. 405：当前请求的方法不支持。例如：表单method=post , Servlet必须对应doPost。否则报405。
   > 3. 空指针或者是NumberFormatException ：错误原因可能是name="price"此处写错，在Servlet端使用request.getParameter("price")去获取。
   
   ![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day3-tomcat-servlet\资料\02.server_Tomcat_deploy.png)

### web.xml文件编写：

```html
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         		xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>AddServlet</servlet-name>
        <servlet-class>com.atguigu.servlets.AddServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>AddServlet</servlet-name>
        <url-pattern>/add</url-pattern>
    </servlet-mapping>
    <!--
    1. 用户发请求，action=add
    2. 项目中，web.xml中找到url-pattern = /add-> 
    3. 找第11行的servlet-name = AddServlet
    4. 找和servlet-mapping中servlet-name一致的servlet
    5. servlet-class -> com.atguigu.servlets.AddServlet
    6. 用户发送的是post请求（method=post），因此tomcat会执行AddServlet中的doPost方法
    -->
</web-app>
```



## 第一节：

### 设置编码-防止中文乱码

  1)get请求方式：

```java
    //get方式基于tomcat8不需要设置编码
    //tomcat8之前 get请求发送的中文数据:
    String fname = request.getParameter("fname");
    //1.将字符串打散成字节数组("ISO-8859-1"：Tomcat编码方式)
    byte[] bytes = fname.getBytes("ISO-8859-1");
    //2.将字节数组按照设定的编码重新组装成字符串
    fname = new String(bytes,"UTF-8");
```

  2)post请求方式：

```java
    request.setCharacterEncoding("UTF-8");
```

**注意：**

> 1. tomcat8开始，设置编码，只需要针对post方式
> 2. 设置编码(post)这一句代码必须在所有的获取参数动作之前



### Servlet的继承关系 - 重点查看的是服务方法（service()）

#### 继承关系

> ——javax.servlet.Servlet接口
> 	——javax.servlet.GenericServlet抽象类
> 		——javax.servlet.http.HttpServlet抽象子类

##### 相关方法(API)

javax.servlet.Servlet接口:

> void init(config) - 初始化方法
> void service(request,response) - 服务方法:客户端发送请求，service()被自动调用
> void destory() - 销毁方法

javax.servlet.GenericServlet抽象类：

> void service(request,response) - 仍然是抽象的

javax.servlet.http.HttpServlet 抽象子类：

> void service(request,response) - 不是抽象的
>
> ```java
>  //获取请求的方式(get or post)
> String method = req.getMethod();
> 
> //各种if判断，根据请求方式不同，决定去调用不同的do方法
> if (method.equals("GET")) {
>  this.doGet(req,resp);
> } else if (method.equals("HEAD")) {
>  this.doHead(req, resp);
> } else if (method.equals("POST")) {
>  this.doPost(req, resp);
> } else if (method.equals("PUT")) {...
> 
> //在HttpServlet这个抽象类中，do方法都差不多:
> protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
>     String protocol = req.getProtocol();
>     String msg = lStrings.getString("http.method_get_not_supported");
>     if (protocol.endsWith("1.1")) {
>          resp.sendError(405, msg);
>     } else {
>          resp.sendError(400, msg);
>     }
>  }
> ```

##### 小结：

> 1. 继承关系： HttpServlet -> GenericServlet -> Servlet
>
> 2. Servlet中的核心方法： init() , service() , destroy()
>
> 3. 服务方法： 
>
>    当有请求过来时，service方法会自动响应（其实是tomcat容器调用的）
>
>    在HttpServlet中我们会去分析请求的方式：到底是get、post、head还是delete等等然后再决定调用的是哪个do开头的方法
>
>    在HttpServlet中这些do方法默认都是405的实现风格-要我们子类去实现对应的方法，否则默认会报405错误
>
> 4. 因此，在新建Servlet时，才会去考虑请求方法，从而决定重写哪个do方法

#### Servlet的生命周期

##### 生命周期：

> 1. 从出生到死亡的过程就是生命周期。
> 2. 对应Servlet中的三个方法：init(),service(),destroy()。
> 3. Servlet生命周期是Tomcat维护的。

默认情况下：

> 1. 第一次接收请求时，这个Servlet会进行实例化(Tomcat在底层使用反射进行初始化，调用构造方法)、初始化(调用init())、然后服务(调用service())
> 2. 从第二次请求开始，每一次都是服务
> 3. 当容器关闭时，其中的所有的servlet实例会被销毁，调用销毁方法
>    

通过案例得知：

> 1. tomcat负责维护servlet实例的生命周期。Servlet实例tomcat只会创建一个，所有的请求都是这个实例去响应。
> 2. 默认情况下，第一次请求时，tomcat才会去实例化，初始化，然后再服务。（好处： 提高系统的启动速度 ；缺点：第一次请求时，耗时较长。）
> 3. 得出结论： 如果需要提高系统的启动速度，当前默认情况就是这样。如果需要提高响应速度，我们应该设置Servlet的初始化时机。

Servlet的初始化时机：

> 1. 默认是第一次接收请求时，实例化，初始化。
> 2. 可以在web.xml中通过<load-on-startup>来设置servlet启动的先后顺序,数字越小（启动越靠前，最小值0）

Servlet在容器中是单例的、线程不安全的：

> 1. 单例：所有的请求都是同一个实例去响应
>
> 2. 线程不安全：一个线程需要根据这个实例中的某个成员变量值去做逻辑判断。但是在中间某个时机，另一个线程改变了这个成员变量的值，从而导致第一个线程的执行路径发生了变化
>
>    ![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day4-servlet-http-session\素材\01.Servlet是线程不安全的.png)
>
> 3. 我们已经知道了servlet是线程不安全的，给我们的启发是： 尽量的不要在servlet中定义成员变量。如果不得不定义成员变量，那么不要去：①不要去修改成员变量的值 ②不要去根据成员变量的值做一些逻辑判断

##### Http协议

>  Http是无状态的
>
>  Http（**H**yper **T**ext **T**ransfer **P**rotocol）： 超文本传输协议
>
>  HTTP最大的作用就是确定了请求和响应数据的格式，Http请求响应包含两个部分：请求和响应
>
> 浏览器发送给服务器的数据：请求报文；服务器返回给浏览器的数据：响应报文。

###### 请求：

请求包含三个部分：

>  1.请求行 ；
>
>  2.请求消息头 ；
>
>  3.请求主体

![./images](https://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img002.16e080e9.png)

请求行包含三个信息： 

> 1. 请求的方式 ；
> 2. 请求的URL ； 
> 3. 请求的协议（一般都是HTTP1.1）

请求消息头

作用：通过具体的参数对本次请求进行详细的说明

格式：键值对，键和值之间使用冒号隔开

相对比较重要的请求消息头：

| 名称           | 功能                                                 |
| -------------- | ---------------------------------------------------- |
| Host           | 服务器的主机地址                                     |
| Accept         | 声明当前请求能够接受的『媒体类型』                   |
| Referer        | 当前请求来源页面的地址                               |
| Content-Length | 请求体内容的长度                                     |
| Content-Type   | 请求体的内容类型，这一项的具体值是媒体类型中的某一种 |
| Cookie         | 浏览器访问服务器时携带的Cookie数据                   |

请求体:

> 1.   get方式，没有请求体，但是有一个queryString
> 2.   post方式，有请求体，form data(当前请求体是一个表单提交的请求参数。)
> 3.   json格式，有请求体，request payload(整个请求体以某种特定格式来组织数据)

###### 响应：

响应包含三个部分： 

> 1. 响应行 
> 2. 响应头  
> 3. 响应体

![./images](https://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img007.a6ab27cd.png)

响应行包含三个信息：

> HTTP协议版本
>
> 响应状态码(200) 
>
> 响应状态(ok)

响应状态码：

> 作用：以编码的形式告诉浏览器当前请求处理的结果

| 状态码 | 含义                                                      |
| ------ | --------------------------------------------------------- |
| 200    | 服务器成功处理了当前请求，成功返回响应                    |
| 302    | 重定向                                                    |
| 400    | [SpringMVC特定环境]请求参数问题                           |
| 403    | 没有权限                                                  |
| 404    | 找不到目标资源                                            |
| 405    | 请求方式和服务器端对应的处理方式不一致                    |
| 406    | [SpringMVC特定环境]请求扩展名和实际返回的响应体类型不一致 |
| 50X    | 服务器端内部错误，通常都是服务器端抛异常了                |

响应头：

> 响应体的说明书。
>
> 服务器端对浏览器端设置数据，例如：服务器端返回Cookie信息。

| 名称           | 功能                                                 |
| -------------- | ---------------------------------------------------- |
| Content-Type   | 响应体的内容类型                                     |
| Content-Length | 响应体的内容长度                                     |
| Set-Cookie     | 服务器返回新的Cookie信息给浏览器                     |
| location       | 在**重定向**的情况下，告诉浏览器访问下一个资源的地址 |

响应体：服务器返回的数据主体，有可能是各种数据类型。

> 1. HTML页面
> 2. 图片
> 3. 视频
> 4. 以下载形式返回的文件
> 5. CSS文件
> 6. JavaScript文件



##### 会话

> 1. HTTP 无状态 ：服务器无法判断这两次请求是同一个客户端发过来的，还是不同的客户端发过来的
> 2. 无状态带来的现实问题：第一次请求是添加商品到购物车，第二次请求是结账；如果这两次请求服务器无法区分是同一个用户的，那么就会导致混乱
> 3. 通过会话跟踪技术来解决无状态的问题。

###### 会话跟踪技术

> 1. 客户端第一次发请求给服务器，服务器获取session，获取不到，则创建新的，然后响应给客户端
> 2. 下次客户端给服务器发请求时，会把sessionID带给服务器，那么服务器就能获取到了，那么服务器就判断这一次请求和上次某次请求是同一个客户端，从而能够区分开客户端

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day4-servlet-http-session\素材\02.会话跟踪技术.png)

常用的API：

```java
request.getSession() -> //获取当前的会话，没有则创建一个新的会话
request.getSession(true) -> //效果和不带参数相同
request.getSession(false) -> //获取当前会话，没有则返回null，不会创建新的
session.getId() ->// 获取sessionID
session.isNew() -> //判断当前session是否是新的
session.getMaxInactiveInterval() -> //session的非激活间隔时长，默认1800秒
session.setMaxInactiveInterval()
session.invalidate() ->// 强制性让会话立即失效
....
```

######  session保存作用域

> session保存作用域是和具体的某一个session对应的，一个session对应一个作用域。

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day4-servlet-http-session\素材\03.session保存作用域.png)

常用的API：

```java
void session.setAttribute(k,v)

Object session.getAttribute(k)

void removeAttribute(k)
```

##### 服务器内部转发以及客户端重定向

######  服务器内部转发 : 

```java
request.getRequestDispatcher("...").forward(request,response);
```

> 一次请求响应的过程，对于客户端而言，内部经过了多少次转发，客户端是不知道的
>
> 地址栏没有变化

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day4-servlet-http-session\素材\04.服务器内部转发.png)

###### 客户端重定向：

```java
 response.sendRedirect("....");
```

> 两次请求响应的过程。客户端肯定知道请求URL有变化
>
> 地址栏有变化

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day4-servlet-http-session\素材\05.客户端重定向.png)

#### Thymeleaf - 视图模板技术(同html和css掌握即可)

##### 项目中配置thymeleaf

> 1. 添加thymeleaf的jar包（6个）
>
> 2. 新建一个Servlet类ViewBaseServlet（两个方法）
>
> 3. 在web.xml文件中添加配置
>
>    配置前缀 view-prefix
>
>    配置后缀 view-suffix
>
> 4. 使得我们的Servlet继承ViewBaseServlet
>
> 5.  根据逻辑视图名称 得到 物理视图名称
>
>    ```
>    //此处的视图名称是 index
>    //那么thymeleaf会将这个 逻辑视图名称 对应到 物理视图 名称上去
>    //逻辑视图名称 ：   index
>    //物理视图名称 ：   view-prefix + 逻辑视图名称 + view-suffix
>    //所以真实的视图名称是：      /       index       .html
>    
>    super.processTemplate("index",request,response);
>    ```
>
> 6.  使用thymeleaf的标签
>
>    ```html
>    <table id="tbl_fruit">
>        <tr >
>            <th class="w20" >名称</th>
>            <th class="w20">单价</th>
>            <th class="w20">库存</th>
>            <th >操作</th>
>        </tr>
>        <tr th:if="${#lists.isEmpty(session.fruitList)}">
>            <td colspan="4">对不起，库存为空！</td>
>        </tr>
>        <tr th:unless="${#lists.isEmpty(session.fruitList)}" th:each="fruit:${session.fruitList}">
>            <td th:text="${fruit.fname}">苹果</td>
>            <td th:text="${fruit.price}">5</td>
>            <td th:text="${fruit.fcount}">20</td>
>            <td><img src="imgs/del.jpg" class="delImg"/></td>
>        </tr>
>    </table>
>    ```



#### 保存作用域

保存作用域有四个： page（页面级别，现在几乎不用） , request , session , application

> -  request：一次请求响应范围
>
> ![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day5-thymeleaf-fruit\素材\01.Request保存作用域.png)
>
> 

> -  session：一次会话范围有效
>
> ![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day5-thymeleaf-fruit\素材\02.Session保存作用域.png)

> -  application： 一次应用程序范围有效
>
> ![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day5-thymeleaf-fruit\素材\03.Application保存作用域.png)

#### 路径问题

相对路径与绝对路径

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day5-thymeleaf-fruit\素材\04.相对路径_绝对路径_base标签_@{}.png)

### 第二节：

#### XML : 可扩展的标记语言

HTML : 超文本标记语言,HTML是XML的一个子集

XML包含三个部分：
> 1) XML声明 ， 而且声明这一行代码必须在XML文件的第一行
> 2) DTD 文档类型定义
> 3) XML正文

```xml
<?xml version="1.0" encoding="utf-8"?>
<beans>
    <!-- 这个bean标签的作用是 将来servletpath中涉及的名字对应的是fruit，那么就要FruitController这个类来处理 -->
    <bean id="fruit" class="com.atguigu.fruit.controllers.FruitController"/>
</beans>
```

#### FruitPro中sevrlet的优化：

- 最初的做法：

> 一个请求对应一个Servlet
>
> 缺点：servlet太多了
>
> ![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day6-servlet-mvc\素材\01.mvc01.png)

- 优化1：

> 把一些列的请求对应一个Servlet, IndexServlet/AddServlet/EditServlet/DelServlet/UpdateServlet -> 合并成FruitServlet
>
> 通过operate的值 使用switch-case 来决定调用FruitServlet中的哪一个方法
>
> 缺点：Servlet中充斥着大量的switch-case。随着项目的业务规模扩大，那么会有很多的Servlet，也就意味着会有很多的switch-case，这是一种代码冗余
>
> ![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day6-servlet-mvc\素材\02.mvc02.png)

- 优化2：

>在servlet中使用反射技术，规定operate的值和方法名一致，
>
>接收到operate的值是什么就表明我们需要调用对应的方法进行响应，
>
>如果找不到对应的方法，则抛异常
>
>缺点：每一个servlet中都有类似的反射技术的代码。可以把它们抽取出来，
>
>

- 优化3：

> 设计了中央控制器类：DispatcherServlet
>
> DispatcherServlet这个类的工作分为两大部分：
>
> 1.根据url定位到能够处理这个请求的controller组件：
>
> - 从url中提取servletPath : /fruit.do -> fruit
>
> - 根据fruit找到对应的组件:FruitController ， 对应的依据存储在applicationContext.xml中
>
> ```xml
> 		<bean id="fruit" class="com.atguigu.fruit.controllers.FruitController/>
> ```
>
> ​		通过DOM技术去解析XML文件，在中央控制器中形成一个beanMap容器，用来存放所有的Controller组件
>
> - 根据获取到的operate的值定位到我们FruitController中需要调用的方法
>
>   ![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day6-servlet-mvc\素材\03.MVC03.png)
>
> 2.调用Controller组件中的方法：
>
> - 获取参数
>
>   获取即将要调用的方法的参数签名信息:
>
>   ```java 
>    Parameter[] parameters = method.getParameters();
>   ```
>
>   获取参数的名称:
>
>   ```java
>   parameter.getName();
>   ```
>
>   准备了Object[] parameterValues 这个数组用来存放对应参数的参数值
>
>   另外，需要考虑参数的类型问题，需要做类型转化的工作。获取参数的类型:
>
>   ```java
>   parameter.getType();
>   ```
>
>   
>
> - 执行方法
>
>   ```java
>   Object returnObj = method.invoke(controllerBean , parameterValues);
>   ```
>
> - 视图处理
>
>   ```java
>   String returnStr = (String)returnObj;
>   if(returnStr.startWith("redirect:")){
>    ....
>   }else if.....
>   ```

### 第三节：

#### Servlet的初始化方法

 1. Servlet生命周期：实例化、初始化、服务、销毁

 2) Servlet中的初始化方法有两个：
    
   - init(config)带参方法如下：
     
       ```java
     public void init(ServletConfig config) throws ServletException {
         this.config = config ;
         init();
     }
     ```
   
   - init()无参方法如下：
   
     ```java
     public void init() throws ServletException{
         //如果想要在Servlet初始化时做一些准备工作，可以重写init(无参)方法
         	//可以通过如下步骤去获取初始化设置的数据
         
         		//获取config对象：
        				 ServletConfig config = getServletConfig();
     			//获取初始化参数值： 
         			config.getInitParameter(key);
     }
     ```
 3) 在web.xml文件中配置Servlet
    
    ```xml
    <servlet>
        <servlet-name>Demo01Servlet</servlet-name>
        <servlet-class>com.atguigu.servlet.Demo01Servlet</servlet-class>
        <init-param>
            <param-name>hello</param-name>
            <param-value>world</param-value>
        </init-param>
        <init-param>
            <param-name>uname</param-name>
            <param-value>jim</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>Demo01Servlet</servlet-name>
        <url-pattern>/demo01</url-pattern>
    </servlet-mapping>
    ```
    
 4) 通过注解的方式进行配置：

     ```java
     @WebServlet(urlPatterns = {"/demo01"} ,
     initParams = {
         @WebInitParam(name="hello",value="world"),
         @WebInitParam(name="uname",value="jim")
     })
     ```

#### Servlet中的ServletContext和\<context-param>

(ServletContext是一个**全局**的储存信息的空间，服务器开始就存在，服务器关闭才释放，可以获取配置的上下文参数)

1.获取ServletContext的方法

- 在初始化方法中：

```java
 ServletContxt servletContext = getServletContext();
```

- 在服务方法中可以通过request对象获取，可以通过session获取：

```java
request.getServletContext(); 
```

- 在服务方法中也可以通过session获取：

```java
session.getServletContext()；
```

2.获取初始化值：

```java
servletContext.getInitParameter();
```

#### 业务层

##### MVC : 

> Model（模型）、View（视图）、Controller（控制器）

> 视图层(view)：用于做数据展示以及和用户交互的一个界面
>
> 控制层(controller)：能够接受客户端的请求，具体的业务功能还是需要借助于模型组件来完成
>
> 模型层(model)：模型分为很多种：有比较简单的pojo/vo(value object)，有业务模型组件，有数据访问层组件
>
>  - 值对象模型（POJO）；
>  - 数据访问模型（DAO）；
>  - 业务逻辑模型（BO）；
>  - 数据传输对象（DTO）

##### 区分业务对象和数据访问对象：

> - DAO中的方法都是单精度方法(细粒度方法)。单精度:一个方法只考虑一个操作，比如添加，那就是insert操作、查询那就是select操作....
> - BO中的方法属于业务方法，也实际的业务是比较复杂的，因此业务方法的粒度是比较粗的。

 **例如：**

> 注册这个功能属于业务功能，也就是说注册这个方法属于业务方法。那么这个业务方法中包含了多个DAO方法。也就是说注册这个业务功能需要通过多个DAO方法的组合调用，从而完成注册功能的开发。
>
> >  注册：
> >
> > ​	-检查用户名是否已经被注册 - DAO中的select操作
> >
> > ​	-向用户表新增一条新用户记录 - DAO中的insert操作
> >
> > ​	-向用户积分表新增一条记录（新用户默认初始化积分100分） - DAO中的insert操作
> >
> > ​	-向系统消息表新增一条记录（某某某新用户注册了，需要根据通讯录信息向他的联系人推送消息） - DAO中的insert操作
> >
> > ​	-向系统日志表新增一条记录（某用户在某IP在某年某月某日某时某分某秒某毫秒注册） - DAO中的insert操作
> > ​	-....

##### 在库存系统中添加**业务层**组件

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day7-mvc-ioc-servlet\素材\01.MVC04.png)

#### IOC

##### 耦合/依赖

> -   在软件系统中，层与层之间是存在依赖的。也称之为耦合。
> -   我们系统架构或者是设计的一个原则是： 高内聚低耦合。
> -   层内部的组成应该是高度聚合的，而层与层之间的关系应该是低耦合的，最理想的情况0耦合（就是没有耦合）

##### 解决耦合问题（解耦合）：

> IOC - 控制反转 / DI - 依赖注入

控制反转：

> - 之前在Servlet中，我们创建service对象 ， FruitService fruitService = new FruitServiceImpl();这句话如果出现在servlet中的某个方法内部，那么这个fruitService的作用域（生命周期）应该就是这个方法级别；如果这句话出现在servlet的类中，也就是说fruitService是一个成员变量，那么这个fruitService的作用域（生命周期）应该就是这个servlet实例级别
> - 之后我们在applicationContext.xml中定义了这个fruitService。然后通过解析XML，产生fruitService实例，存放在beanMap中，这个beanMap在一个BeanFactory中。因此，我们转移（改变）了之前的service实例、dao实例等等他们的生命周期。控制权从程序员转移到BeanFactory。这个现象我们称之为控制反转


    1) 之前我们在控制层出现代码：FruitService fruitService = new FruitServiceImpl()；
       那么，控制层和service层存在耦合。
    2) 之后，我们将代码修改成FruitService fruitService = null ;
       然后，在配置文件中配置:
       <bean id="fruit" class="FruitController">
            <property name="fruitService" ref="fruitService"/>
       </bean>

依赖注入：

> - 之前我们在控制层出现代码：FruitService fruitService = new FruitServiceImpl()；那么，控制层和service层存在耦合。
> - 之后，我们将代码修改成FruitService fruitService = null ; 然后，在配置文件中配置:
>
> ```xml
>    <bean id="fruit" class="FruitController">
>         <property name="fruitService" ref="fruitService"/>//属性
>    </bean>
> ```



#### 过滤器Filter

> - Filter也属于Servlet规范
>
> - Filter开发步骤：新建类实现Filter接口，然后实现其中的三个方法：init、doFilter、destroy
>
> - 配置Filter，可以用注解@WebFilter，也可以使用xml文件 <filter> <filter-mapping>
>
>   ​	--Filter在配置时，和servlet一样，也可以配置通配符，例如 @WebFilter("*.do")表示拦截所有以.do结尾的请求

 过滤器链：

> 执行的顺序依次是： A B C demo03 C2 B2 A2
>
> 如果采取的是注解的方式进行配置，那么过滤器链的拦截顺序是按照全类名的先后顺序排序的
>
> 如果采取的是xml的方式进行配置，那么按照配置的先后顺序进行排序

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day8-mvc-filter-transaction-listener\素材\01.Filter.png)

过滤器设置编码：

```java
@WebFilter(urlPatterns = {"*.do"},initParams = {@WebInitParam(name = "encoding",value = "UTF-8")})
public class CharacterEncodingFilter implements Filter {

    private String encoding = "UTF-8";

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        String encodingStr = filterConfig.getInitParameter("encoding");
        if(StringUtil.isNotEmpty(encodingStr)){
            encoding = encodingStr ;
        }
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        ((HttpServletRequest)servletRequest).setCharacterEncoding(encoding);
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {}
}
```

#### 事务管理

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day8-mvc-filter-transaction-listener\素材\03.编程式事务管理.png)

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day8-mvc-filter-transaction-listener\素材\04.编程式事务管理02.png)

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day8-mvc-filter-transaction-listener\素材\05.编程式事务管理03.png)

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day8-mvc-filter-transaction-listener\素材\06.ThreadLocal.png)

涉及到的组件：

  - OpenSessionInViewFilter
  - TransactionManager
  - ThreadLocal
  - ConnUtil
  - BaseDAO

##### ThreadLocal:

>   - get() , set(obj)
>
>   - ThreadLocal称之为本地线程 。 我们可以通过set方法在当前线程上存储数据、通过get方法在当前线程上获取数据（同一个线程内 ）
>
>   - set方法源码分析：
>
>       ```java
>       public void set(T value) {
>       Thread t = Thread.currentThread(); //获取当前的线程
>       ThreadLocalMap map = getMap(t);    //每一个线程都维护各自的一个容器（ThreadLocalMap）
>       if (map != null)
>           map.set(this, value);          //这里的key对应的是ThreadLocal，因为我们的组件中需要传输（共享）的对象可能会有多个（不止Connection）
>     else
>       createMap(t, value);           //默认情况下map是没有初始化的，那么第一次往其中添加数据时，会去初始化
>     }
>         
>     //get方法源码分析：
>       public T get() {
>       Thread t = Thread.currentThread(); //获取当前的线程
>       ThreadLocalMap map = getMap(t);    //获取和这个线程（企业）相关的ThreadLocalMap（也就是工作纽带的集合）
>       if (map != null) {
>           ThreadLocalMap.Entry e = map.getEntry(this);   //this指的是ThreadLocal对象，通过它才能知道是哪一个工作纽带
>           if (e != null) {
>               @SuppressWarnings("unchecked")
>               T result = (T)e.value;     //entry.value就可以获取到工具箱了
>               return result;
>           }
>     }
>     return setInitialValue();
>     }

2. 监听器
    ```java
    1) ServletContextListener //- 监听ServletContext对象的创建和销毁的过程,ServletContext随tomcat容器的创建而创建，销毁而销毁。
    | 方法名                                       | 作用                    |
    | ------------------------------------------- | ------------------------|
    | contextInitialized(ServletContextEvent sce) | ServletContext创建时调用 |
    | contextDestroyed(ServletContextEvent sce)   | ServletContext销毁时调用 |
    //ServletContextEvent对象代表从ServletContext对象身上捕获到的事件，通过这个事件对象我们可以获取到ServletContext对象。
    2) HttpSessionListener //- 监听HttpSession对象的创建和销毁的过程
    | 方法名                                  | 作用                     |
    | -------------------------------------- | -------------------------|
    | sessionCreated(HttpSessionEvent hse)   | HttpSession对象创建时调用 |
    | sessionDestroyed(HttpSessionEvent hse) | HttpSession对象销毁时调用 |
    //HttpSessionEvent对象代表从HttpSession对象身上捕获到的事件，通过这个事件对象我们可以获取到触发事件的HttpSession对象。
    3) ServletRequestListener //- 监听ServletRequest对象的创建和销毁的过程
    | 方法名                                       | 作用                       |
    | ------------------------------------------- | ---------------------------|
    | requestInitialized(ServletRequestEvent sre) | ServletRequest对象创建时调用 |
    | requestDestroyed(ServletRequestEvent sre)   | ServletRequest对象销毁时调用 |
    //ServletRequestEvent对象代表从HttpServletRequest对象身上捕获到的事件，通过这个事件对象我们可以获取到触发事件的HttpServletRequest对象。另外还有一个方法可以获取到当前Web应用的ServletContext对象。
    
    4) ServletContextAttributeListener //- 监听ServletContext的保存作用域的改动(add,remove,replace)
    | 方法名                                               | 作用                                |
    | ---------------------------------------------------- | -----------------------------------|
    | attributeAdded(ServletContextAttributeEvent scab)    | 向ServletContext中添加属性时调用     |
    | attributeRemoved(ServletContextAttributeEvent scab)  | 从ServletContext中移除属性时调用     |
    | attributeReplaced(ServletContextAttributeEvent scab) | 当ServletContext中的属性被修改时调用 |
        
    ServletContextAttributeEvent对象代表属性变化事件，它包含的方法如下：
    | 方法名              | 作用                    |
    | ------------------- | -----------------------|
    | getName()           | 获取修改或添加的属性名   |
    | getValue()          | 获取被修改或添加的属性值 |
    | getServletContext() | 获取ServletContext对象 |
        
    5) HttpSessionAttributeListener //- 监听HttpSession的保存作用域的改动(add,remove,replace)
    | 方法名                                        | 作用                              |
    | --------------------------------------------- | --------------------------------- |
    | attributeAdded(HttpSessionBindingEvent se)    | 向HttpSession中添加属性时调用     |
    | attributeRemoved(HttpSessionBindingEvent se)  | 从HttpSession中移除属性时调用     |
    | attributeReplaced(HttpSessionBindingEvent se) | 当HttpSession中的属性被修改时调用 |
    
    HttpSessionBindingEvent对象代表属性变化事件，它包含的方法如下：
    | 方法名       | 作用                         |
    | ------------ | ----------------------------|
    | getName()    | 获取修改或添加的属性名        |
    | getValue()   | 获取被修改或添加的属性值      |
    | getSession() | 获取触发事件的HttpSession对象 |
    6) ServletRequestAttributeListener //- 监听ServletRequest的保存作用域的改动(add,remove,replace)
    | 方法名                                               | 作用                                 |
    | ---------------------------------------------------- | ------------------------------------ |
    | attributeAdded(ServletRequestAttributeEvent srae)    | 向ServletRequest中添加属性时调用     |
    | attributeRemoved(ServletRequestAttributeEvent srae)  | 从ServletRequest中移除属性时调用     |
    | attributeReplaced(ServletRequestAttributeEvent srae) | 当ServletRequest中的属性被修改时调用 |
    
    ServletRequestAttributeEvent对象代表属性变化事件，它包含的方法如下：
    | 方法名               | 作用                             |
    | -------------------- | -------------------------------- |
    | getName()            | 获取修改或添加的属性名           |
    | getValue()           | 获取被修改或添加的属性值         |
    | getServletRequest () | 获取触发事件的ServletRequest对象 |
        
        
    7) HttpSessionBindingListener //- 监听某个对象在Session域中的创建与移除
    | 方法名                                      | 作用                            |
    | ------------------------------------------- | -------------------------------|
    | valueBound(HttpSessionBindingEvent event)   | 该类的实例被放到Session域中时调用 |
    | valueUnbound(HttpSessionBindingEvent event) | 该类的实例从Session中移除时调用   |
    
    HttpSessionBindingEvent对象代表属性变化事件，它包含的方法如下：
    | 方法名       | 作用                         |
    | ------------ | ----------------------------|
    | getName()    | 获取当前事件涉及的属性名      |
    | getValue()   | 获取当前事件涉及的属性值      |
    | getSession() | 获取触发事件的HttpSession对象 |
        
    8) HttpSessionActivationListener //- 监听某个对象在Session域中的序列化和反序列化
    ```
    
3. ServletContextListener的应用 - ContextLoaderListener

### 第四节：

##### mvc各层设计

![](F:\学习笔记\Java学习资料\尚硅谷Java学科全套教程（总207.77GB）\1.尚硅谷全套JAVA教程--基础必备（67.32GB）\尚硅谷JavaWeb2022版全新教程\代码素材资料\Day9-qqzone\素材\01.MVC各个层的设计.png)



##### Cookie

> - 在浏览器端临时存储数据
> - 键值对
> - 键和值都是字符串类型
> - 数据量很小

###### 在浏览器和服务器之间的传递Cookie在浏览器和服务器之间的传递:

1. 在服务器端没有创建Cookie并返回的情况下，浏览器端不会保存Cookie信息。双方在请求和响应的过程中也不会携带Cookie的数据。

2. 创建Cookie对象

     ```java
     // 1.创建Cookie对象
     Cookie cookie = new Cookie("cookie-message", "hello-cookie");
     
     // 2.将Cookie对象添加到响应中
     response.addCookie(cookie);
     
     // 3.返回响应
     processTemplate("page-target", request, response);
     ```

3. 服务器端返回Cookie的响应消息头

     ![./images](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAWoAAACuCAIAAACQtNCiAAAc4klEQVR42u2dXYhex3nHxxc1uSoklkFrS4vckqI66GIlHNvd0kApiWTJWl0EjCAJNonkDWyEly5rI1MkEyyCcG1ls5CVleLimApDLvaVtZJjHEMMW9sxq/dC2JVt6oqVmlcg+YNcmfoiPefMnDnPM+eZ8zHv19nV/3ch9J6P+Toz//M8M2fnueXPf/6zAgCA+txy+fLlYZcBALAmuQXWBwAgDMgHACAQyAcAIBDIBwAgEMgHACAQyAcAIBDIBwAgEMgHACAQyAcAIBDIBwAgkJ7LR+fckaOta2ps8uTkWOGF184dPdLqeC9sLzy60N44cfSpB0bMlXnIvcnl2e+SxIfJ2bNnP/300x/84Af5Ux999NEzzzxTmsKxY8duu+02ms6TTz5548YN3/WPPPLIfffdN+x6g3VIF/JBR/XGiclv/mHhjDTG1cjEU0cf2Jg7nAz4+PTeo0d3j8intXxUKEvnWmek00oSTCSjMfLx3HPPXbp0qfSyDRs2PP300/njkS48/PDDX//61/OnPvnkk2effTa6cXp6OrrswQcf1BoRHT98+PDJkyeHWm9wU9CtfKi9sW60lJYP5ShFZ+no0fRg8v9OebI7JifVwsKK/e1RHw/t5x9dWBmbfGq01Qz5cIjUJDIcohEeGQt5vdAjvzQRbX3YBCNxeeuttyL5iNJ85ZVXnIuj43v27Bl2vcH6pNfykfziJgmTj6oDWtsm1ayPWDL+l1/ZGOvDoh2TmZmZDz74IHI6du/eHSlF9FO0LFQqJVQp8metiaGtj0g+HLfoxRdf/NrXvgb5AH1CkI9f//rX77//vnh11BHHx8fND798bJw8Orp41DFJcvKhZ0nsAS0YxtZIjAiVyMdYO5lMkdkxefKgarc3dhaTa+KfJLFmyIcWjq1bt0ZehuJzH9q10SJS0fTQHDx48Msvv3zhhRf0vVY+YH2AQSLIxxdffPHzn//8888/d47fddddUa/NfgfKR+yaXEvmO7RGmCHO7IV0XmRjHZtCn9UKUsd46R9aESLhqDL9QS2RSGIi0dFyU4AWpsgG0VMk2q6B9QEGhuy8fPzxx6dOnaJHvvKVrzz++OPRv9mhMPnYMdZeSe0CPciTAW9mRuj/NfGRjYmdMpJ6RlqC2kWmRTPkw/Loo4/63JD8qeL1F9GasPIB6wMMEu/cx+uvv/673/3O/vz+979/9913sytqykdqaySnrlH9iP+rkrPJKoyKxeLOsbGVyCmZmLiztXrnxLUzrU6kBT9WJxPrY2LvtVaqNcbNqYJ1bQZOpBEFZ6l8WO0Q5UZPlFo5sD8dDYLRAQZD0dTp3NxcpxNbAePj40JfDJGPZGqjY00DM69hzAky8fHOPcmRxHy4Zn2cEeu8THS0PSIuyhgfp96STV+paH28/fbbL7zwQnFS1JqI7o1cnkgp7DRqJCiR87K0tBQdjByZ+++/3zc1C0D3FMnHZ599FinIV7/61YMHDzK3RSN891Gw8tI5lw34bGJz4o+xnzKyY0yttDupr9FpRxaJapnvPkZaqYVCv+bYmDg40jcj6bxJguejkkFTxfrQdscjjzwSDXjfmgu1PuziSyQ6kc/y9NNPR//58MMPI/nQ1kekHfgABPSVkoXblZWVO+64Y2REGoFF1oeZlci++1DJyLcCER/fOPHUxAMbR9LLOty5SD8b26dOLrbUNxMVoFOnJneuDlY6dkwevbOVTqAM3wypNfdRvARjrY9ILN56663p6WmtKZFYPPvss/pbEuu82GuGWXmwfunZR+v0CzGLdVhGziQzFPLsg/idu/TV6TWmQYzM5mDJ2FnYhpghVSj44oNaH/r/GzZsiPyd6OJIMiK7Q99C5z60OyMqFwBd0h/5oH5NKhnt54929j6qfun/iINdz+WDJOgIQRWBaGfzsgNVkFqfctiF24rWh574OHv2LJ1MtbMnPmMHgB6Cv7gFAAQC+QAABAL5AAAEAvkAAAQC+QAABAL5AAAEAvkAAAQC+QAABAL5AAAEAvkAAAQC+QAABAL5AAAEAvkAAAQC+QAABAL5AAAEAvkAAAQC+QAABAL5AAAEMhj5WJnbNHE8+7lr/t1T+xq88ejKiU0TUpymXfPtU/tuH3bpAGgKfZeP64sHxqbO82PdyceFuU17j6uZ1tXHdoQXqzCRm1g+ri9Oji3t1NWMRV+duXpoe/fJZu+PLtsw7k6v7m4vrIXH0Fk8cM/S7qyr27Zd5W9TzWzr6qEd7otWzZa3f5zs1Nnq1/eSPsuHHqURe+btI7++OLd876Fg+TBjuzv5qJaIeTDDVo3eDWO3Q4sXrE7G/bgk36BhTLUpkGHLR51n4ZWP2z0X1Ew/n2Y83C4O0rTvr3yYUUq0wzlPtTYTzrhZI4slMlImV+/RF2iDhQktuYUeT02b2onkceUjtaT0iyIrf5TC+JtGj9pbFoy1RWttCqPYcaOtNjVvKw5MPuLnpaykQj6EFmqYfPDrS18Pvaav8mFGl/z2piMqxVwpnUpG3fiyMPJdOTAyoWolIpY/Z32kBTO36PGfyMGq6O9o6yZfHa0g5fKxIli5mcVEKm4lSedlr0l+qqj89y57W5tltzCadT7dNVtqLxXfAm80uX1+29RU3CatnUsT0WXMvvPJR1LN/DuGtZtpJS0fJnHF7UdRoxNNXNjSPqYO62LTWlMvlbWGtZqz7lH8LCQGIR/mcWQDYWt3Tn1N+ikfzmDjOIZJ+iCTXpLeqJ+o8853/Q4+CPXZXWTAVErE92hyzgu9Mctr3+2s/NxOUSwv3Qtrzf6IXYr1lTj3S0xBtsXXxzdenC/urzRF58XOisqykK2AVAWOqMNawv5hedNeRcSxjnxktcgV0qoSu+b64onl8cd0CqzirKeRFvAaMtQFcIshPQvhbWdfYH2XD1qAwXvZw7I+zMiUHBZrOOTdkLQfk5EvvQyTsw9dqZwIfbHMOt3dnfvI1Gp82RldVI9MdtFl+6+4xlGSTbCBqmxz2UmKXEeMC6lmZ44fV1wfi+QjruzqQZoLzzdJ0zaOTz7i60fSXBQvZB3nhbtRtJQ0X2+C9HYufFmlkp6j8jqurRX2wqjm0LmEWh/11iiTDjlrmn3qvHeioC/0VT5S61qoUjZr0KV8eKdXOtUTqSMfVvhmZo8/c9wthmATaZXp5s0gdVliXaewrpaUJ+cWFchHlODzo7IcDEE+vFcWyIf7IqGPRslmJrnFtlXeF6ZW6vCsD5aLfdbcwPSYbP2jv1On2eNhvuiBKw+d2vyy5LxkFmZt68Mq0cqJOfWY9YAqJeItvrjyIjjGynFeSHXsVAuZcD2hDtVwUCtYH/lm1xMEzgj3y4fzyhXybbj1wRcdXOtDlcwIUAtFaorCZ+FDsj6Yfde981LFt+on/f7uQ9Jy/9SmGY2FI5++ZGZTD5+/iOkESsVEigovTTHqHLPXu/ipCJthpWjZqrryIk6J+efJsoGUvyY3G6KRxahIPqQ1wmD50O3J28Ha5Pm5D0k+kge6zaTAP+qpIh8s2aLlz1rTk+xiVsKs2XuwcMuzUOtn4dbgjB9nmcAcJbZ34cinkiQt3DrLN9UT8TwbQT7Se4nxYs2ZlprQOrLLnbPM1bSqfPDbxZUXxVZz3KUiamSRZ8EWpC9P5oZEoXwwxbQrLx758Akob8/ZIgeNrbxIzgtbh4otr7RGPvngii+IV3bujGMy5J+FD59fbJPqfuqUZjHo77nxNy/1cZUopicfsw0JZ712CJDVx2E3BqgD5KMO9KXElWLtysewP8RSxnBQA10yAD0B8lEHKx85mVi78jFkyKd30I41B+QDABAI5AMAEAjkAwAQCOQDABAI5AMAEAjkAwAQCOQDABAI5AMAEAjkAwAQCOQDABAI5AMAEAjkAwAQCOQDABDIoDYrzGh6hEoAQEUGLx8JFf6wXf8JfC+2kFnxxwQcFN6YmGSPrCobryq2Y/jxPvydO9mDaza3s555lr6N1DzbslV+YZQnVaWh7A1x3+PpeMKSlSWS/c7vL5dvqALyW73ZQDxZmDi9Wdzq8DttBQYiH3t4p9ctUfLwcluxd8/AY3CZmsSb5c3PX5py98h0uk5pW9Kh2OnPrvxR931zvDi+gRtdZfKwOiKFRNGtHUeNqhg20ZdF3YZS6SaGOm/PXbm9Fz0Jids4FjRUYVJkI1ITqlWx5yhI3pA6bUUGLB8qUxC2XbDBE2Vulm2BqzHvgTphaIfyJC7MHVjdH2/7mdtit3BH7zzxloJqRl3cckzvDHxa7b445d1svQf4Iyr5BgzZVTR6Lqc3L+hdTivJR0EWNRsqTur06CkhSpandiWlEuWjblK+EApxfAw1qy6OHtFbW59WOy9O0RwhH85eUjQmg8p5N1nAG3Ks5d1ud7Qr+eCvIL6nrhQCspuWEDY9r7XDaHT98vi7owsvbz4V1/r05iOjC3Yf8/rxGcvJmovvkJ5FwHKVS9iUuKp8FGRR3FD6MYlWfZF8FEXJIwd7JR9FEe3eHG9vWYj1Lur2L28+tmVhDPKRtVqhfLDwB1LoJmcvdW6e1HQFa8iHPwRkcEs48qFjI5xRE0YTSycIEvmIY9YlwhGJSBxGT8tH7fiMVSC1zgKUmGgDkYrlkxJfsHXkQ85CFTdUTfnIdDZ/i0c+sheZ3N+qdA/vNdoJih5lIhyRiOxfPQD5MJTLRz5Clywf4vRnzUWc6vJRHAIyrCUE+chCKAhBQITCx0UaXTxw+FW17WDyspIiRVWJz1gOC7Oix3Z79Pm0CkJcGM92x/XkQ8qibkORlihzXuoFZJMD93ni0Qj3mgdhe3Lce8ff0WIRmZOHl9S2yYX4+UI+DIJ8sHC+6fQyDVIvykdhtO2qVJePshCQIS0hWh9ZXywb22w42f+QuYma8RmLcILF5OOwuPOOfhOgjnzIWdRtqIxC+QhoHLn/HK/iEkrhx2OPLJWPHdZLcq+EfEgrLzTQnO52Tuwlj/VBQkzNvTN+qJY/EWx99KQlSuY+yqZChCKlt3S6is8oZeQuczh92pkRkF/LWQuHTJ2SLGo2FGufEvmoNSPr1qXWepDbDq58sCtpsGHIR/64Y1Mw+JKKvryVGO1OUonqq66mTvMv8Blh7iOHFFSxtCWklRc7tsuDrfjlI+6CXcRnFKqW/7CCDhVu9pcERqwsHwVZFDZU4NSpL8im/1Map2U8DVXQskxkIR8VEOTDMzUVjf/Jy2MsVEp+lkuIWtjtwm328c9Mq80mvaUQkPyuypnyJsiSqrO4I3ynYN/DteMz+hAi9QqPg7gYvuDyI0JS5Y6nmEVJQ0nyURJU2BSnytQprQUtUlFDVW3euKHiQPH5tSrIxzqn2oQZAOsbyEcABQYzADcRkI+aIKgiACmQDwBAIJAPAEAgkA8AQCCQDwBAIJAPAEAgkA8AQCCQDwBAIJAPAEAgkA8AQCCQDwBAIJAPAEAgkA8AQCCQDwBAIP2XD2fvFvytKgDrhSHvNtY8asZVrJ94Fs2ke/JBDxtEbk9jb6TOoubq27MAPaCv8sEDysXQiCSNpLN44Cm1e+vUUq1NdKvSU/ngO56ZoIe9HmaBe7Urd9M9b6ROH7VjXIIhMGD5oNDNRHno1ujnmd1Le+PoHvH+o0mEhhYPp2aGjRC2MtvePd71c+o8CVtX/h6L7j2sjp0aPc32m+wZPZSPkm3Ee0WofPCa+iN1+m+vF+MSDIX+Oi++nWbdjYhjaOiGlEgR4lhqeb0oCFu5w/GY6shHGutshO1Pq4MGpEKmxLA17nHFpW1PFq5taWdr96sTuu6zedteaCuJogjPki7n5IZtWS6psLAbMNleuCS+vBzp4npl+aDVhHw0l35PnToykfZmqgJ04/J7l81onMnFmk2OkECWo76wlTakQ+2tjLNOz16ePIYDHYfe6JCefs+CIbGAafT6CgHQ/MHW+BAlyfIQB2RighXVHeGi9UGD14kl8dgskI/1xmAWbsuDtsRktgYzE0isqc2nY29In/WGrUzinpwPmKOl0Y/oYCsOkmRxQuRKTko+ELSxcZyQRaUug1c+3H39SY70FDFe3LyKwoab0uXiNjlh33yRnCAf640BfveRWsjRu3f8TTlKdjb34QZzIhMiPDhu3sQQA+tWwHXX7SgS5IMqixAd0hdI0ScfeVcuN5WjsZXyOS+5407QOS1SRK1ySscFSJAPKbgXfQr+qPSQj/VGn6dOJ6/st2PYyAcxEEi3Wzkxpx5L4yHLUdTVrj27zp89n65QesNWBsqHf1Q48sFD4YrRIbu1PiqWVnBwCqwPZVdDIiNuedxnX9S2PhzS+aPt8inIx3piICsvlD1OnHHLbOuqTz7oGzg7dV0OW3m7Py530dRpPkykHTnsFBm3ucmL49xkUOLchygfAeMkmQairaEXbtkQzfk4SQHULrXzWCZVLGtphlWc2rjkUWcnSJrTxqJ8dGio41xqkI8GM7iVlxi2gsssdj4Dmhvn1jQoWN1I+1+YfAjv//Q9zGdqaCLe6JBKdjr88uHWpdK8L82CfDZGpdkdk6KuedLJPyZx5cU9LphRRZE6ZfkIiXEJBg7+5qWc8vjVa4f+1kVerwXrFshHOetHPvocbzn8E1WwNoF8lLMe5IMse8EFAL0C8gEACATyAQAIBPIBAAgE8gEACATyAQAIBPIBAAgE8gEACOSWN954o09JX/mLvx527YbJ5i//G+0AGo7tpWHc8tlnn/WpZP957dYhtEdj+LuN/4d2AA3H9tIwIB/9AvIBmg/ko6FAPkDzgXw0FMgHaD6Qj4YC+QDNB/LRUCAfoPk0Xj7+65cHf/JcdvRb//rMvzz4l92n3sNkr7+ysP+fL6hv/+j0/DfZ3+S3z/3TQ4tK7fvFhw/8be1UIR+g+TRaPv70xtTMsdfY0dJxrnXh4Zef/96Y54rrf/jp+K9+r+olW8Dako+4fdQTr//4r/x1UYeXJ/9xDW9O0hPivrf8nTq9wjzuiO28AT9+6W9+9u/J/3r18uueHnWDJsvHe+Z5ZFpw/Q8vvb31e0UPwDwqv3zYZ0mecXmyRXjloysgH8Olvnxo4pfThXGhAUMT7A83pXywKhIjQg9d16yQXvvmFbHd1zRGC2iy7r1u4kw+7DXTT7x+3yXH+qAeU1opn94NRz5Ayk0tH5Vpsnzcmll9rhbkHZBo9B5RC2XyYQawx1JwJkRoIkxWDEaDiHxsvai9renkwXDnJZ94IhnV5YO2BpE/KmrTpEPk5dXWMe03zNHz2t4sqXLzO0rn3zYc/savjj0XXfzE+G9/FjWIrR1pRp4Le6D0wXlqLWux9BBt3WvVwox2U34nC28tVE358DyjQhrVDRotHzmZSOtvuohpJt2gurbFzks6mTIt6W6al7k3/Zm01A2dbNpq6SNM0rHy8YvvXPhJ3KvS3k/lw6TGFWe6QP4d+Yhz/J98p4+zWE0fc1K7byRpJtndlTZC3FzvkaZL+g09yBuB936WlKcYbpEWo4aaVP8R1TFqzB1vmxyTWo+SxrElj5NVwiPzv7Hb515SDxBJGiUqT/7/2+1UO4RaMItSox+f6Sq6M/iy4LXwNKCvLv5n5Kdp3WBns+WDFN2ISDzkNuSnVJX4Ms9JcpH1oS92FFqL1HdvJAWQHRbFDZNMuah8CN20+IXD5IONBIJrgsa5qCi7Dc71pDckt/zo8Hu/Oqak3HP9Js766rdtFr6S8GZkZVAmhfip/fGHViPocEqemiCmyUhQZYOK1+43m9LRFR+/8V075mvVwhntWRZ/8tdCbkA5wXwZvDeSijauGzy2bQ3Ih22j2CiNRuPWFWYLUErkIzU7hbmP/AxoJh+5WQxBPr69/VuvXfi9IqYNkY8NJt/qqzBMPjyeqrfvjvzGuT57vVvb3j+jxPqN4NDZJhIng7zyoYjVbSBP0Lc8QVbfpt0akdSoWTeaeYvvcXM1X4tC60OSj48La1FDPnLPlJpgLJeHqfnQrG7QZPl479xPr/49a25jOKRTDNlo/PilX6rvZY6Mf+UlM2SY3xhndN8l0XmhRo3jvOifmZT88MZ+OtcrWR+2q11/5dzF+x7wv2r6an0Ye+qufCuVvXbKqWp9eOD2NoHa7cQ+52XmU1Rc7mvVoqr1IZZ/rVgf3XeDJs99vCcY/N/yuazm1UQ/FZFf9dIkqEm24E0r3cVnVZMrb2QmkpUM5ksTiidr+NxHMqhUidNLBhgbhGywZV0tKZ6bb4nTWwGvfPC5Dy9V5kFyUvKabkz/2K5bC698qJJaBM59cEH00bhu0GT5uNU1FFkl2awq+4gjPV7gKcimr3KMNO6N+9Z0mddjc8+MEXnhls/7Vlh58dSXlopZ0Z6pePqmojO4noWhfNZlyxZ++XDb0NbCmz5/TNPOC3YxLecT6qHX7tBJue8VsWNUqIVfPny18Dag91XnXy4poFndYE1Mnd6M4KP1AFzLpdpyBgim0dbHENqjMUA+6uMa2NU8AhAO5KOhQD6CcDyFih4BCATy0VAgH6D5QD4aCuQDNB/IR0OBfIDmA/loKJAP0Hy6lY/+hYkCGoSJAo2l2zBRCFIJAAgD8gEACATyAQAIBPIBAAgE8gEACATyAQAIBPIBAAgE8gEACKSf8nFhbtPe48LxPfPthX3928Dh+uKBsanzXWe0Mrdpgpd+1/y7p/aN9K3cUZYnNk08o9RM6+pjO0wt+txWAHTDupMPminkowviAry6uye5x/VScaUCCzI5trSzfQoq2jwG4bwMciT0Li8jH7Nnrh7a3u8WSrMk8jGgLP1APkApQ5EPOzJbau/E8eT4qh45Bvuet1e2R58fmzobn8vGM7NuZltX91+ZNNekqSR9rrN44B7tzGT2iC1Sa+fSxNR5SSM88mFSi0o4uXqPNk+YVbJCKmIKQP0p5dpE1NWa3zo1JVofJZlmhtLs/PzFKX3lqX3K3hViNEE+QCnDlA9DfHx8mY/8RA4O7RA8iPSU6xl55OPe5Uw7suzikTVGDhfIh3Pj7VSMnFNJR8/r1wpTRlo7j3/nlQ8xU7GJasoHS9+UTcuHlldbKmXKrEz5mTREJVkYlQSOyoduDfEdwJ4Cr7IVYtAohiofsotBX/vplbrvmq4W98vxd2QnxcmLuwM6NXJ7kW9SIh+6Q6e2A1O0NM2VxcXRfal+mYPsdmXkhhWvSD78mZqxmp6qY3EkiW/LtYNJSpeNXlMkH6Z5o6zj45fIU0iuoQdj4qQumqLSLFiRYH00l+E6L1mXvc7NAcXlww7I7KdyPBfTm3lerjlgU96/yoskODilzkvW6c8XKJoe3uQgUTTlZEFHrMd5Kc2UjeEq+DwL7ryQMVwoH4qZFeay5Jr5+UtTU4o1TnR8YUumCzapOOvLk2mRIB/NpRnykb63k7frat76EOSD/Ezgr18uAXnT1y1SL+Rj88vCxGd+KjeTj4euOM6Fd+WleqaduvMd3sHZY/lI3DfemJK4z6RGSqZokI/m0gj5yNvJ5yvJh4Yd9Fgf1jxZmTuhDlVaFq0tH/s6NZwXwTUrd1781kcXzsugrA8jl9RLcqwPT5EgH82lSfLBKZYP431kiNaHNDdZ9asKYUoyLsaIXz5GKk+dOlMzDnXkY99I11OnSRPNinMfonzEuS/tpq09U00+0tkrbnVeFIqX3WuaFFOnzaQR8kHs2Gxtso58ZCNE0AU2h+9RGYEA+VCOQW4ryGSCezfZqZlWS03UdV7IzzjD1rujC/ZU9ZWXC8JEklc+eJnbWxbSeYoK8uFYmm7WQotFwjF5eUw0UsDQwd+8rCdS8cKn7mAgQD7WOh4raVBfyoKbGcjHWseRj2wNG4B+A/kAAAQC+QAABAL5AAAEAvkAAAQC+QAABAL5AAAEAvkAAAQC+QAABPL/HC0Qi2lURHAAAAAASUVORK5CYII=)

4. 浏览器拿到Cookie之后，以后的每一个请求都会携带Cookie信息。

     ![./images](data:image/png;base64,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)

5. 服务器端读取Cookie的信息

     ```java
     // 1.通过request对象获取Cookie的数组
     Cookie[] cookies = request.getCookies();
     
     // 2.遍历数组
     for (Cookie cookie : cookies) {
         System.out.println("cookie.getName() = " + cookie.getName());
         System.out.println("cookie.getValue() = " + cookie.getValue());
         System.out.println();
     }
     ```

6. 在客户端保存Cookie

     

###### Cookie时效性

1. 设置Cookie的有效时长

   ```java
   cookie.setMaxAge(60) ; //设置cookie的有效时长是60秒
   cookie.setDomain(pattern);
   cookie.setPath(uri);
   ```

2. Cookie的应用：

     > 记住用户名和密码十天 setMaxAge(60 * 60 * 24 * 10)
     >
     > 十天免登录
