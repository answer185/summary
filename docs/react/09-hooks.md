# Hooks
## 什么是 Hooks
Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

它并没有改变react的概念，反而是为react的概念，比如 props, state, context, refs 以及生命周期等概念提供了更直接的api。还提供了一种更强大的方式来组合他们。

## 动机
1. 在组件之间复用状态逻辑很难

之前的解决方案是render props和高阶组件，但是这样需要重新组织组件结构，而且代码不好理解，因为相关的内容在其他位置。

2. 复杂组件变得难以理解

在各种生命周期里包含很多无关的内容，同一个内容的相关操作拆分到生命周期的各个函数中。

3. 难以理解的 class

因为 javascript特殊的this工作方式，导致需要去绑定事件，造成代码冗余，目前比较稳定的就是类实例属性。也与其他语言不一致。

4. 不需要将函数组件转class组件
因为一些业务变更，需要将原来的函数组件转换成class组件，以使用state等特性。
这时不需要对代码重构。

## 原理

它的实现原理，就是为函数式组件提供了一些勾子函数，引入class组件特性，比如: useState，useEffect，useContext，useReducer等。

## useState的使用
- useState与class的等价转换
- 参数：默认state
- 数组解构的赋值
- 函数式更新：更新的时候传入一个函数，可以获取到之前的状态
- 惰性初始 state： 初始化时传入一个函数，一般用的于复杂的计算
- 跳过 state 更新： 直接传入当前的state, 值没变的情况

## useEffect的使用
- 与class组件的转换：componentDidMount、componentDidUpdate 和 componentWillUnmount
- 参数：一个函数，传递第二个参数（effect 的条件执行
），作为控制，避免每次都触发
- 使用多个 Effect 实现关注点分离
- 副作用：改变 DOM、添加订阅、设置定时器、记录日志以及执行其他包含副作用的操作

## 规则
- Hooks 应该只在函数组件中使用。
- Hooks 应该在外层使用，不应该在循环，条件或嵌套函数中使用（可以hooks内部判断）（原因：是因为react需要知道哪个state对应哪个hooks，如果有条件判断，并且按顺序执行，如果后面取消某个hooks,会导致执行报错。）
- 对于自定义Hooks，必须使用use开头命名
- 可以使用 eslint-plugin-react-hooks 插件来检测

## 自定义的hook
- 书写代码及返回值：使用use开头的函数
- 使用自定义hooks,加载相应的函数，获取返回值，然后使用返回值即可
- 多次调用state是独立的。
- hooks之间传递消息：通过更新state,并把state的值作为参数传递

## hooks的其他API
### useContext
- 传入React.createContext初始化的对象

### useReducer
传入reducer操作和初始值，返回当前的state及dispatch, 可以考虑在某些场景替换掉useState，或者redux

### useCallback
返回一个 memoized 回调函数，只有当依赖值变化时才会触发更新。

### useMemo
返回一个 memoized 值。当某个依赖项改变时才会触发重新计算。

### useRef
useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。

### useLayoutEffect
DOM 变更之后同步调用 effect，尽量使用。

尽可能使用标准的 useEffect 以避免阻塞视觉更新

### useDebugValue
自动输出带hooks名称的标签


## 使用场景
### umi
名称 | 说明
---|---
useHistory | 获取 history 对象
useLocation | 获取 location 对象
useParams | 获取 params 对象。 params 对象为动态路由（例如：/users/:id）里的参数键值对。
useRouteMatch | 获取当前路由的匹配信息。

### ahooks
名称 | 说明
---|---
useRequest | 网络请求的封装
useAntdTable | 搜索和列表的联动
useFusionTable | 
useDrop & useDrag | 拖拽
useDynamicList | 动态列表

### 其他
- 表单处理、动画、订阅声明、计时器，朋友的在线状态，组件内部状态(useReducer)
- 用户和组织机构的数据。
- 当前的登录用户数据。

## API规范
### 返回值
#### 1. 无输出
允许 Hooks 无输出，一般常见于生命周期类 Hooks。

#### 2. value 型
Hooks 输出仅有一个值。

#### 3. value setValue 型
输出值为 value 和 setValue 类型的，结构为 [value, setValue] 。

```
const [state, setState] = useLocalStorageState(...)
```

#### 4. value actions 型
输出值为单 value 与多 actions 类型的，结构为 [value, actions] 。

#### 5. values 型
输出值为多 value 类型的，结构为 {...values} 

#### 6. values actions 型

输出值为多 value 与多 actions 类型的，结构为 {...values, ...actions} 。

### 参数
原则上不允许超过两个参数。

#### 1. 无参数
允许 Hooks 无参数。

#### 2. 单输入
单参数无论是否必填直接输入。

#### 3. 多必选参数
必选参数小于 2 个，应平级输入。

如果多于 2 个，应以 object 形式输入。

#### 4. 多非必选参数
多非必选参数以 object 形式输入。

#### 5. 必选参数 + 非必选参数
必选参数在前，非必选参数在后。

