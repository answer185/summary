# 渲染
渲染的目标是生成用于显示的对象数据，并将这些对象转成真实的DOM对象
需要转换的对象有：
- React元素: 通过React.createElement创建，即JSX书写的组件元素
- React节点: 专门用于渲染到UI界面的对象，React会通过React元素，创建React节点，ReactDOM一定是通过React节点来进行渲染的，有以下节点类型：
    - React DOM节点：创建该节点的React元素类型是一个字符串
    - React 组件节点：创建该节点的React元素类型是一个函数或是一个类
    - React 文本节点：由字符串、数字创建的
    - React 空节点：由null、undefined、false、true
    - React 数组节点：该节点由一个数组创建
- 真实DOM: 通过document.createElement创建的dom元素

## 首次渲染
1. 通过参数的值创建节点
2. 根据不同的节点，做不同的事情

    2.1 文本节点：通过document.createTextNode创建真实的文本节点
    
    2.2 空节点：什么都不做
    
    2.3 数组节点：遍历数组，将数组每一项递归创建节点（回到第1步进行反复操作，直到遍历结束）
    
    2.4 DOM节点：通过document.createElement创建真实的DOM对象，然后立即设置该真实DOM元素的各种属性，然后遍历对应React元素的children属性，递归操作（回到第1步进行反复操作，直到遍历结束）
    
    2.5 组件节点

    1. 函数组件：调用函数(该函数必须返回一个可以生成节点的内容)，将该函数的返回结果递归生成节点（回到第1步进行反复操作，直到遍历结束）
    2. 类组件：

        1. 创建该类的实例
        2. 立即调用对象的生命周期方法：static getDerivedStateFromProps
        3. 运行该对象的render方法，拿到节点对象（将该节点递归操作，回到第1步进行反复操作）
        4. 将该组件的componentDidMount加入到执行队列（先进先出，先进先执行），当整个虚拟DOM树全部构建完毕，并且将真实的DOM对象加入到容器中后，执行该队列

3. 生成出虚拟DOM树之后，将该树保存起来，以便后续使用
4. 将之前生成的真实的DOM对象，加入到容器中。

## 更新节点
更新场景：
1. 重新调用ReactDOM.render，触发根节点更新
2. 在类组件的实例对象中调用setState，会导致该实例所在的节点更新

### 节点更新
- 如果调用的是ReactDOM.render，进入根节点的对比（diff）更新
- 如果调用的是setState
    - 运行生命周期函数，static getDerivedStateFromProps
    - 运行shouldComponentUpdate，如果该函数返回false，终止当前流程
    - 运行render，得到一个新的节点，进入该新的节点的对比更新
    - 将生命周期函数getSnapshotBeforeUpdate加入执行队列，以待将来执行
    - 将生命周期函数componentDidUpdate加入执行队列，以待将来执行

后续步骤：

- 更新虚拟DOM树
- 完成真实的DOM更新
- 依次调用执行队列中的componentDidMount
- 依次调用执行队列中的getSnapshotBeforeUpdate
- 依次调用执行队列中的componentDidUpdate
