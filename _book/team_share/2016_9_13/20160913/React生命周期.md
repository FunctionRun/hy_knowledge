## React生命周期

React中生命周期方法：

- getDefaultProps
- getInitialState
- componentWillMount
- render
- componentDidMount
- componentWillReceiveProps
- shouldComponentUpdate
-  componentWillUpdate
- componentDidUpdate
- componentWillUnmount

React中，组件的生命周期方法在不同状态下的执行顺序：

- 首次加载：getDefaultProps、getInitialState、componentWillMount、render、componentDidMount
- 卸载组件：componentWillUnmount
- 重新加载组件：getInitialState、componentWillMount、render、componentDidMount
- props或state改变时：componentWillReceiveProps、shouldComponentUpdate、componentWillUpdate、render、componentDidUpdate

### 生命周期方法中调用setState方法

- componentWillMount：不重新render，执行state合并
- render：
- componentDidMount：更新state
- componentWillReceiveProps：不重新render，执行state合并
- shouldComponentUpdate：造成死循环
-  componentWillUpdate：造成死循环
- componentDidUpdate：更新state
- componentWillUnmount：不重新render

在shouldComponentUpdate和componentWillUpdate中调用setState会造成死循环的原因：当执行到该生命周期方法适，内部状态已经变为RECEIVING_PROPS -> NULL，则内部方法preformUpdateIfNecessary就会调用updateComponent进行组件更新，但updateComponent又会调用shouldComponentUpdate和componentWillUpdate。