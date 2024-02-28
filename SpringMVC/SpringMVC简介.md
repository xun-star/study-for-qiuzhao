# 什么是MVC

* MVC是一种软件架构思想，将软件按照模型，视图，控制器来划分
  * M(model)：模型层，指java工程中的bean，作用是处理数据
  * V(view)：视图层，指HTML,JSP等，与前端用户交互，展示数据
  * C(controller)：控制层，用来处理浏览器的请求和响应
* MVC的工作流程：
      用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果找到相应的View视图，渲染数据后最终响应给浏览器

# 什么是SpringMVC

SpringMVC是Spring的一个后续产品，是Spring的一个子项目，SpringMVC 是 Spring 为表述层开发提供的一整套完备的解决方案，目前业界普遍选择了 SpringMVC 作为 Java EE 项目表述层开发的**首选方案**。

# SpringMVC的特点

- **Spring 家族原生产品**，与 IOC 容器等基础设施无缝对接
- **基于原生的Servlet**，通过了功能强大的**前端控制器DispatcherServlet**，对请求和响应进行统一处理
- 表述层各细分领域需要解决的问题**全方位覆盖**，提供**全面解决方案**
- **代码清新简洁**，大幅度提升开发效率
- 内部组件化程度高，可插拔式组件**即插即用**，想要什么功能配置相应组件即可
- **性能卓著**，尤其适合现代大型、超大型互联网项目要求

