## React-router

为react项目提供路由功能。

### 相关的包
- react-router: 核心库
- react-router-dom: 基于核心库在 web端 使用的库
- react-router-native: 基于核心库在 React Native 使用的库
- react-router-config:  用于配置静态路由的库
    - 随着 React Router v4 的引入，不再有集中式路由配置  

- react-router-redux: 集成 Redux 的库
    - 已经不推荐使用，推荐使用 connected-react-router 和 Redux 进行配合使用

开发时不需要安装核心库，如果只在浏览器端使用，直接安装 react-router-dom

### 两种路由方式
提供两个路由器BrowserRouter和HashRoauter。前者基于url的pathname段，后者基于hash段。

```
 前者：http://127.0.0.1:3000/article/num1

 后者：http://127.0.0.1:3000/#/article/num1（不一定是这样，但#是少不了的）

```

这两个路由都会创建一个专门的 history 对象。两者之间的主要区别是它们存储 URL 和与 Web 服务器通信的方式：

> import { BrowserRouter, Route, Link } from "react-router-dom";

- BrowserRouter 容器，用来存放 Router、Link

```
- ``BrowserRouter`` 提供了如下属性
    * ``basename (string)`` 当前位置的基准 URL
    * ``forceRefresh (boolean)``，在导航的过程中整个页面是否刷新
    * ``getUserConfirmation (func)``，当导航需要确认时执行的函数。默认是：window.confirm
    * ``keyLength (number)``  location.key 的长度。默认是 6
    * ``children (node)`` 要渲染的子节点
```

> HashRouter 不支持 location.key 和 location.state ，所以在浏览器中建议使用 BrowserRouter

### api

React Router 中有三类组件：

- router components 路由器，`<BrowserRouter>`、`<HashRouter>`
- route matching components 路线匹配器，`<Route>`、`<Switch>`
- navigation components 导航，`<Link>`、`<NavLink>`、`<Redirect>`

其他还有 
-  `<MemoryRouter>` ：内存路由组件、
-  `<NativeRouter>` Native的路由组件、
-  `<StaticRouter>` 静态路由组件这些路由组件，

其中 MemoryRouter 主要用在 ReactNative 这种非浏览器的环境中，因此直接将 URL 的 history 保存在了内容中。StaticRouter 主要用于服务器端渲染

### Route组件
当 location 与 Route 的 path 匹配时渲染 Route 中的 Component
#### 三种渲染方式
Route 接受三种渲染方式
- `<Route component>`
- `<Route render>`： render function 类型，Route 会渲染这个 function 的返回值，可以在函数中附加一些额外的逻辑，所以你可以在render中添加一些逻辑判断，再返回一个要渲染的 component

- `<Route children>` ： children function 类型，比 render 多了 match参数，可以根据 match参数来决定匹配的时候渲染什么，不匹配的时候渲染什么

#### 属性
Route 经常用的是 exact、path 以及 component 属性
- exact 是否进行精确匹配，路由 /a 可以和 /a/、/a 匹配
- strict 是否进行严格匹配，指明路径只匹配以斜线结尾的路径，路由/a可以和/a匹配，不能和/a/匹配，相比 exact 会更严格些
- path (string) 标识路由的路径，没有 path 属性的 Route 总是会匹配
- component 表示路径对应显示的组件
- location (object) 除了通过 path 传递路由路径，也可以通过传递 location 对象可以匹配
- sensitive (boolean) 匹配路径时，是否区分大小写

#### props
Route 组件都接收 location、history、match三个 props
三个 props 比较常用的是 match，通过 match.params 可以取到动态参数的值

### switch
Swtich 就近匹配路由，仅渲染一个路由，路由的默认行为是匹配了就直接渲染，大部分场景下这个逻辑是没有问题的，但考虑下面的场景
```
/// 假设你访问的URL为 /dog
<Route path='/dog' component={Dog}></Route> // 虽然这里匹配了，但不会停止查找
<Route path="/:dog" component={Husky}></Route> // 这个路由依然会被匹配，这样两个组件都会被渲染
...
<Switch>
  <Route path='/dog' component={Dog}></Route> // Switch 匹配一个路由后就不会再去查找下一个路由，那么下面的路由就不会被匹配
  <Route path="/:dog" component={Husky}></Route>
</Switch>
```

### Link
### Link
Link 声明路由要跳转的地方

**to**

to（string | object | function） 需要跳转到的路径(pathname) 或地址（location）
- 为 string 时 就是一个明确的路径地址
- 为 object 时有如下属性（就是一个location对象）
    - pathname：URL路径。
    - search：URl中查询字符串。
    - hash：URL的hash分段，例如#a-hash。
    - state：表示location中的状态
- 为 function 时，就是一个函数接收当前 location 为参数，然后以字符串或对象的形式返回位置形式

**replace**
replace (boolean) 为 true 是替换历史记录，false 是新增历史记录

```
  <Link to="/course" />
  ...
  <Link to={{
    pathname: '/course',
    search: '?sort=name',
    hash: '#the-hash',
    state: { fromDashboard: true }
  }} />
  ...
  <Link to={location => ({ ...location, pathname: "/courses" })} />
  
    <Link to={location => `${location.pathname}?sort=name`} />
```

### NavLink
NavLink 功能与 Link 类似不过参数更多，并且可以设置被选中时的样式或者类
- exact (boolean) 是否进行精确匹配
- strict (boolean) 是否进行严格匹配
- to（string | object） 需要跳转到的路径(pathname)或地址（location）
- activeClassName (string) 是选中状态的类名，我们可以为其添加样式
- activeStyle (Object) 元素处于选中状态时，应用于元素的样式
- isActive(function) 添加额外逻辑以确定链接是否处于活动状态

```
<NavLink activeClassName='select' to='/'>Car</NavLink> // 这样当链接被点击时会用 select 替代原本的 active
```

### Redirect
Redirect 重定向组件

- from (string) 需要重定向的路径，可以包括动态参数
- push (boolean) 为 true 时，重定向会将新条目推入历史记录，而不是替换当前条目
- to (string | object) 重定向到的路径
- exact (boolean) 是否要对 from 进行精确匹配
- strict (boolean) 是否要对 from 进行严格匹配
- sensitive (boolean) 匹配 from 时是否区分大小写

```
<Route path="/husky" component={Husky}></Route>
<Redirect to='/husky'/> // 会把没有匹配的路径直接重定向到 /husky
```

### Hooks
#### useHistory
用以获取history对象，进行编程式的导航
```
const Husky = props => {
  console.log(useHistory()); // 与 props.history 结果一致
  console.log(props.history);
  return <div>哈士奇</div>;
};
...
/// 使用 useHistory 的好处是，引入组件会更自由些
<Route path="/dog" component={Dog}></Route> // 必须这么写，props 才能拿到相关值
...
<Route path="/husky">
    <Husky />
</Route> // 这样写的话 useHistory 可以正常取值，但是 props 不行
```

#### useLocation
用以获取location对象，可以查看当前路由信息

```
const Husky = props => {
  console.log(useLocation()); // 与 props.location 结果一致
  console.log(props.location);
  return <div>哈士奇</div>;
};
```

#### useParams
可以用来获取 match.params
```
const Husky = props => {
    console.log(useParams()) // 与 props.match.params 结果一致，但明显更简洁
    console.log(props.match.params)
    const {eat} = props.match.params;
    return (
        <div>哈士奇 吃 {eat}</div>
    );
}
```

#### useRouteMatch
可以接受一个 path 字符串作为参数。当参数的path与当前的路径相匹配时，useRouteMatch会返回 match 对象，否则返回 null。
```
// 接收一个字符串作为参数
const Husky = props => {
        const match = useParams('/husky'); // 假定当前匹配路由就是 /husky，如果访问的路径不是 /husky ，那么 match 就为 null
    const {eat} = match ? match.params : '';
    return (
        <div>哈士奇 吃 {eat}</div>
    );
}
```

