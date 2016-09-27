## React服务端渲染执行流程

### 首次加载页面

- 服务端路由匹配
- 执行组件static方法，加载初始state
- 执行createStore
- dispatch默认的action，type=@@redux/INIT
- dispatch默认的action，type=@@PROBE_UNKNOWN_ACTION_x.x.x
- 执行所有的reducer，返回默认的state
- dispatch默认的action，type=@@redux/INIT
- 执行所有reducer，返回默认的state，初始化store中数据结构
- 执行默认组件（Route path="/"对应的组件）的mapStateToProps方法
- 执行默认组件（Route path="/"对应的组件）的mapDispatchToProps方法
- 执行默认组件（Route path="/"对应的组件）的render方法
- 执行默认组件（Route path="/"对应的组件）的componentDidMount方法
- 执行IndexRoute路由对应的组件的mapStateToProps方法
- 执行IndexRoute路由对应的组件的render方法
- 执行IndexRoute路由对应的组件的componentDidMount方法

### 路由跳转

- 执行默认组件（Route path="/"对应的组件）的render方法
- 执行路由对应组件的mapStateToProps方法
- 执行路由对应组件的mapDispatchToProps方法
- 执行路由对应组件的constructor方法
- 执行路由对应组件的render方法
- 执行路由对应组件的componentDidMount方法（该方法中dispatch(action({type="XXX"}))）
- 执行middware的api.js方法
- 传入state和type=XXX的action，执行所有reducer
- 执行默认组件的mapStateToProps方法
- 执行路由对应组件的mapStateToProps方法，将state作为props向下传
- 执行路由对应组件的render方法，在render方法中就可以通过this.props.xxx拿到父组件传过来的数据，进行页面渲染


redux中，每一次dispatch(action)，所有的reducer都会执行

## 开发说明

### action说明

- mock数据测试：URL的定义参考示例项目中requestURL和server/mockServer中的写法
- 调用后台接口：参考requestURL及action写法，包括传参方式