## 优化

### 相关技术
- PWA
- SSR
- API中间层和代理层
- 长列表渲染优化

## 包含的方面
- 基础层
- react层面
- 打包层面
- 图片层面
- 网络请求层面： 加大带宽，CDN, 接口发送的并行与串行，预加载，浏览器缓存，http2的使用
- 体验优化： 首屏优化（骨架图，loading界面，pwa,）长列表， 懒加载。

[参考文章](https://juejin.cn/post/6892994632968306702)

### 基础层
- 减少重绘重排
- 使用事件委托
- if-else 对比 switch和查找表的使用
if-else条件多的时候，使用switch代码，或者使用obj[key]r查找表方式来代替。
- 使用requestAnimationFrame
- 使用webworker, 独立于主线程之外的线程
- 降低css层次的复杂度，最多3级
- 使用 flexbox 而不是较早的布局模型

### react
- 适当地使用shouldComponentUpdate生命周期方法。 它避免了子组件的不必要的渲染。 如果树中有100个组件，则不重新渲染整个组件树来提高应用程序性能。
- 在显示列表或表格时始终使用 Keys，这会让 React 的更新速度更快
- 不可变性是提高性能的关键。不要对数据进行修改，而是始终在现有集合的基础上创建新的集合，以保持尽可能少的复制，从而提高性能。
- 高开销的计算: 使用useMemo来缓存
- 使用  Chrome Performance  或开发者工具中的分析器 对组件进行分析

### 打包
- 使用production的环境进行打包
- 使用create-react-app来构建项目，这会创建整个项目结构，并进行大量优化。
- 代码分离是将代码插入到单独的文件中，只加载模块或部分所需的文件的技术。
    - babel-plugin-import 实现对一些库的按需加载
    - 路由页面的分离
    - 某些点击事件相关代码的分离。
#### webpack-bundle-analyzer
使用 webpack-bundle-analyzer 分析依赖关系。根据实际情况解决，通常的解决方案有：
- CDN：在webpack的externals中设置，然后在html中添加相应的cdn
- 拆 vendor： 把某些包及相应的依赖，单独拆分成一个vendor文件
- Dynamic import: React 懒加载, 路由懒加载, umi的dynamic高阶函数封装。
- moment语言包优化：使用webpack的ContextReplacementPlugin

### 图片
- 图片延迟加载
- webP
- 响应式图片: 图片裁剪，根据展示区域的大小，确定图片的大小。
- 调整图片大小、 降低图片质量

### 网络请求
#### 外部条件
- 带宽。
- CDN。

#### 请求
- 减少 HTTP 请求：合并接口；使用字体图标 iconfont 代替图片图标
- 使用 HTTP2
- 接口的并行和串行安排。
- 使用preload，prefetch,dns-prefetch等指定提前请求指定文件，或者根据情况，浏览器自行决定是否提前dns预解析或者按需请求某些资源。
- script脚本的defer和async属性的恰当使用
- 放在css放在header,js放在body下面

可以使用webpack4的插件 

![image](http://note.youdao.com/yws/res/101018/4CD74078DCA7411B9487A32510C1A909)

#### 返回数据
- gzip压缩
- 使用服务端渲染

#### 本地缓存
- pwa
- 使用http缓存

### 体验优化
- 使用react-lazyload，懒加载你的视窗初始看不见的组件或者图片
- List长列表的优化
- 高计算量的工作交给web work线程