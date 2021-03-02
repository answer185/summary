## 微前端

出现的背景是因为单页应用变的臃肿。所以把一个单页应用再拆分成由多个应用聚合而成的应用。各个子应用还可以独立开发部署。

好处: 是提高开发效率，维护方便。

特点是：

- 应用自治：不同的团队开发。遵循统一的接口规范或框架，以便于系统集成在一起。
- 单一职责
- 技术栈无关

### 实现方式
- 路由分发式：通过反向代理，将请求路由到对应的应用上。
- 前端的微服务化：在不同的框架之上设计通信和加载机制。以使一个页面内加载对应的应用。
- 微应用：通过软件工程的试，在部署构建环境中，把多个独立的应用组合成一个单体应用。
- 微件化：开发新的构建系统，把应用打包成独立的chunk代码，然后远程加载。
- 前端容器化：使用iframe来加载
- 应用组件化：使用Web Components技术来构建

### 拆分方式
- 按照业务拆分
- 权限
- 更新频率
- 组织机构
- 和后端的微服务保持一致

### 架构设计的难点
与单体应用的不同，微前端的架构里，可能会多个共享层，而不是一个。

需要考虑：
- 组件与模式库：在应用之间提供通用的UI，共享的业务组件，以及相应的通用函数。如日期转换函数
- 应用通信机制：设计应用间的通信机制，并提供底层库的支持。
- 数据共享机制：对于通用的数据，采取一定的策略来缓存数据，而不是每个应用单独获取数据。
- 专用的构建系统（可选），单独开发一个构建系统。

#### 通信
有两种情况：

- 同级通信，即，在同一个HTML Dcoument下
- 父子级通信：即使用iframe实现

对与同级，通过监听全局的自定义事件来实现。
父子级通信 通过PostMessage,parent.window等实现。

```
let custom = new CustomEvent('test_event',{detail:{e_name : " this is a test "}})// 某个dom元素监听自定义事件

let div = document.createElement("DIV");

div.addEventListener("test_event",function(e){console.log(e.detail.e_name)},false)// 触发自定义事件（dispatchEvent 除非事件的参数是必填项，切必须为事件对象）div.dispatchEvent(custom)；// this is a test
```

实现的方式：
- 通过全局的数据状态, 设置相应的事件元素，然后在子应用中，设置事件监听

```
var content = document.getElementById("content");
content.addEventListener("click",function(){
    
    console.log("11");
},false)
content.addEventListener("click",function(){
    
    console.log("22");
},false)
content.addEventListener("mouseenter",add,false);
```

#### 共享数据
- 通过URL传递
- 使用LocalStorage共享
- 其他客户端存储，如Indexed DB， Web SQL等
- 服务端存储。
- global state

### 架构模式
有两种

- 基座模式：通过一个主应用来管理其他应用，好处是设计难度小，方便实施，但是通用性低
- 自组织模式：应用之间是平等的，不存在相互管理。好处是通用性高，但是设计难度大，不好实施。


它们都需要一个查找应用的机制。这是一个应用注册表的服务，可以是一个固定值的配置文件，如json文件，也可以是一个动态更新的配置。

主要作用是：

- 应用发现
- 应用注册
- 第三方应用注册
- 访问权限等相关配置。

### 相关的库
single-spa 及基于 single-spa的qikun。

