### 代码分割
- 打包技术带来的新问题，bundle过大，需要关注包的内容
- 解决方案：动态加载，把一些第一次不展示的内容，分离出去。webpack的，import.then的写法
- react的解决方案：React.lazy+Suspense，传入相应的加载函数，不足是不支持服务端渲染
- React.lazy的替代方案：loadable-components库，支持服务端渲染
- umi的dynamic api，把loading封装在底层。

### context
- 解决组件树之间层层传递props的问题
- 解决方案：React.createContext(),参数是默认值。
- API: createContext, provider(value = ), consumer, contextType, displayName
- 替代解决方案：组合的方式

### 错误边界
- 解决的问题：组件内的 JavaScript 错误会导致 React 的内部状态被破坏，并且在下一次渲染时 产生 可能无法追踪的 错误
- 原理:  static getDerivedStateFromError() 或 componentDidCatch() 这两个生命周期的方法，static getDerivedStateFromError() 渲染备用 UI ，使用 componentDidCatch() 打印错误信息。
- 使用：`<ErrorBoundary>` 包含子组件，位置可以在全局，或局部，局部的好处是避免其他没问题的组件正常展示

### ref转发和ref
- 解决问题：操作dom的问题
- ref转发与ref的区别: 在子组件里控制父组件，ref: 控制子元素
- ref和refs的使用：React.forwardRef, React.createRef
- ref只能用在dom组件和类组件上，不能用在函数组件上，因为它没有实例，如果需要对函数组件设置ref，使用forwardRef。

### fragment
- 一个组件返回多个元素,而无需向 DOM 添加额外节点
- 如： tr下，返回多个td

### 高阶组件
- 解决的问题：复用组件内部逻辑，术语叫：横切关注点问题
例子：比如博客列表和评论列表，订阅同一个数据源，大部分代码是一样的。

- 高阶组件是参数为组件，返回值为新组件的函数。
- 其他解决方案：mixins： HOC 不会修改传入的组件（state与mixin的强偶合，修改一个名称，需要全部修改。），也不会使用继承来复制其行为。相反，HOC 通过将组件包装在容器组件中来组成新组件。HOC 是纯函数，没有副作用。不足：就是组件层次变深，且不好理解。
- 与hooks的区别：针对类组件，而hooks只能是函数组件
- 约定1：将不相关的 props 传递给被包裹的组件
- 约定2：最大化可组合性：1. 参数，只有一个参数，或多个参数，解决组合组件，配置的问题。2. 返回高阶组件的高阶函数，组合model中的数据等，如connect函数。即优先配置
- 约定3：包装显示名称以便轻松调试
- 使用问题：1. 不要在render中使用高阶组件，2. 务必复制静态方法。3. Refs 不会被传递

### Portals
- 解决的问题：将子节点渲染到存在于父组件以外的 DOM 节点
- 用法： `ReactDOM.createPortal(child, container)`
- 事件冒泡：还是按组件的层级来，父组件可以监听到。

### Profiler
- 解决的问题：测量渲染一个 React 应用多久渲染一次以及渲染一次的“代价”
- 用法：Profiler组件包含，设置id 和 onRender
- onRender接收到组件的相应开销：时间，操作等数据

### 严格模式
- 解决的问题：对已经过时的内容进行提示，有：生命周期函数， ref写法，context，意外的副作用，废弃的findDomeNode的用法
- 使用： React.StrictMode组件

### render props
- 解决：实现组件间复用，逻辑一样，渲染不一致的情况
- 使用：传入一个函数

### DOM元素
解决的问题： DOM命名不规范的问题

所有的 DOM 特性和属性（包括事件处理）都应该是小驼峰命名的方式。例如，与 HTML 中的 tabindex 属性对应的 React 的属性是 tabIndex。例外的情况是 aria-* 以及 data-* 属性，一律使用小写字母命名。比如, 你依然可以用 aria-label 作为 aria-label

### 单元测试
相关的库有：`react-dom/test-utils`，`react-test-renderer'`, jest

对组件进行测试。
