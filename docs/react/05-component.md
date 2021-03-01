
## 组件

组件具有 props、state、内部事件维护及生命周期等特性。实际上是一个函数，返回渲染的内容。

props是外部传进来的属性，不可以在组件内部修改。而state是组件的内部状态，可以在组件内部变化，组件根据不同的state做不同的展示。

生命周期具有，创建，更新和卸载3个阶段。

从fiber的阶段来说，render之前的操作，都属于Render阶段。这个阶段，纯净且没有副作用。可能会被 React暂停，中止或重新启动。

### this.setState()的同步和异步

这里的“异步”不是说异步代码实现. 而是说 react 会先收集变更,然后再进行统一的更新.

setState 在原生事件和 setTimeout 中都是同步的. 在合成事件和钩子函数中是异步的.

在 setState 中, 会根据一个 isBatchingUpdates 判断是直接更新还是稍后更新, 它的默认值是 false. 但是 React 在调用事件处理函数之前会先调用 batchedUpdates 这个函数, batchedUpdates 函数 会将 isBatchingUpdates 设置为 true. 因此, 由 react 控制的事件处理过程, 就变成了异步(批量更新).

setState 的批量更新优化也是建立在“异步”（合成事件、钩子函数）之上的，在原生事件和setTimeout 中不会批量更新，在“异步”中如果对同一个值进行多次setState，setState的批量更新策略会对其进行覆盖，取最后一次的执行，如果是同时setState多个不同的值，在更新时会对其进行合并批量更新。