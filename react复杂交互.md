## CSS过度和动画基础

使用ReactCSSTransitionGroup

看板的demo有时间可以写一遍

### CSS过度

是在两个不同状态之间进行插值实现动画效果,  它包含一个开始状态和一个结束状态(两点之间的动画效果)

transition属性最多接收四个特性:

- 需要进行动画处理的元素属性名称, 如果省略该参数, 所有课实现动画效果的属性都会被进行过渡处理
- 动画的持续时间
- 可选的时间方法用于控制动画的加速缺陷(ease-in, ease-out)
- 可选的延迟时间(动画开始前)

```
h1 {
  background: #fff;
}

h1:hover {
  background: #ccc;
  transition: 3s;
}
```



### CSS关键帧

关键帧动画可进行更复杂的控制, 使用关键帧的形式在开始和结束状态之间设置中间步骤

```
@keyframes size-change {
  0% { transform: none; }
  50% { transform: scale(1.5) }
  100% { transform: none; }
}

h1:hover {
  font-size: 68px;
  animation: size-change .5s infinite;
  transform-origin: center;
}
```

### 动态添加css

```
<button onclick="document.querySelector('.sidebar').classList.add('active')">
```

### ReactCSSTransitionGroup

ReactCSSTransitionGroup是一个简单元素, 其中包装了所有你实现动画效果需要的元素, 它会在组件特定的生命周期中触发CSS动画和过度效果. 它是以插件形式提供的, npm install react-addons-css-transition-group --save

js

```
import React, { Component } from 'react';
import { render } from 'react-dom';
import ReactCSSTransitionGroup from 'react-addons-css-transition-group';

class AnimatedShoppingList extends Component {
  constructor() {
    super(...arguments)
    this.state = {
      items: [
        { id: 1, name: 'milk' },
        { id: 2, name: 'apple' },
        { id: 3, name: 'people' }
      ]
    }
  }
  handleRemove(index) {
    let newItems = this.state.items
    newItems.splice(index, 1)
    this.setState({ items: newItems })
  }
  handleChange(event) {
    if (event.key === 'Enter') {
      let newItem = { id: Date.now(), name: event.target.value }
      let newItems = this.state.items.concat(newItem)
      event.target.value = ''
      this.setState({ items: newItems })
    }
  }
  render() {
    let shoppingItems = this.state.items.map((item, index) => {
      // 对于ReactCSSTransitionGroup中的所有子项, 必须提供key特性,即使其中只渲染一个条目,因为react会根据它来判断哪个子项是新增的, 删除的或是需要保留的
      return (<div key={item.id}
        className="item"
        onClick={this.handleRemove.bind(this, index)}>
        {item.name}
      </div>)
    })
    return (
      <div>
        <ReactCSSTransitionGroup
          transitionName="example"
          transitionAppear={true}
          transitionAppearTimeout={300}
          transitionEnterTimeout={300}
          transitionLeaveTimeout={300}>
          {shoppingItems}
        </ReactCSSTransitionGroup>
        <input type="text" value={this.state.newItem}
          onKeyDown={this.handleChange.bind(this)} />
      </div>
    )
  }
}

render(<AnimatedShoppingList />, document.getElementById('root'))
```



css

```
.example-appear,
.example-enter {
  opacity: 0;
  transform: translateX(-250px);
}

.example-appear-active,
.example-enter-active {
  opacity: 1;
  transform: translateX(0);
  transition: .5s;
}

.example-leave {
  opacity: 1;
  transform: translateX(0);
}

.example-leave-active {
  opacity: 0;
  transform: translateX(-250px);
  transition: .5s;
}
```



## 拖放

使用React DnD 这样一个拖放库, 让我们能用React方式开发(不需要涉及DOM，拥抱单向数据流，使用纯数据的方式来定义拖曳源和放置目标的逻辑，以及其他一些优势)。在底层，React DnD会使用浏览器开放的API，处理各个厂商之间API的不一致性和怪异性，隐藏其具体的实现细节

npm install react-dnd-html5-backend --save

npm install react-dnd --save

三个高阶组件：

- DragSource
  - 返回指定组件的增强版，使组件成为一个可被拖曳的元素
- DragTarget
  - 同样会返回增强版的组件，使其有能力被拖放到其内部的元素
- DragDropContext
  - 封装了发生拖放交互行为的父元素，在交互场景背后设置了共享的拖放状态（这也是其最简单的实现方式）

还提供了装饰器模式， 可使用js装饰器来替代高阶组件。

#### DragDropContext

```
import React, { Component } from 'react';
import ShoppingCart from './ShoppingCart'
import Snack from './Snack'
import { DragDropContext } from 'react-dnd'
import HTML5Backend from 'react-dnd-html5-backend'

class Container extends Component {
  render() {
    return (
      <div>
        <Snack name="apple" />
        <Snack name="egg" />
        <ShoppingCart />
      </div>
    )
  }
}
// 需要特别追，这个模块并没有直接导出Container组件，而在Container的基础之上导出一个高阶组件，其中注入了所有与拖放相关的状态和函数。同样要注意，这里导入了React DnD的HTML5后端，如前文所述，React拖放库可以支持不同的后端
export default DragDropContext(HTML5Backend)(Container)
```



#### DragSource和DragTarget高阶组件

- type
  - 这个参数指定了组件的名称。在复杂的UI中，可能会出现多个不同类型的拖曳源和多个不同类型的放置目标之间的交互，所以给每个源或目标一个特定的标识是非常重要的
- spec对象
  - spec对象描述了增强组件是如何响应拖曳和放置事件的。它是一个包含了若干函数的普通js对象，这些函数会在拖曳交互发生时被调用，例如beginDrag和endDrag以及canDrag和onDrop
- collect函数
  - React DnD并非直接在你的组件中注入所有props属性，而通过collect函数让你来控制哪些属性需要进行注入以及如何进行注入。这种方式给你提供了强大能力，包括在注入前对属性进行预处理、改变其名称等
  - 当拖放交互发生时，ReactDnD库会调用在你的组件中定义的collect函数，并传入两个参数：connector和monitor
    - connector参数必须映射为一个属性，它会在你的组件渲染时用于界定组件DOM 的范围。
    - 对于DragSource组件，这部分DOM用来在拖曳过程中对组件进行呈现。
    - 对于DragTarget组件，这个DOM的范围则作为可放置区域
  - monitor参数允许你把拖放状态映射为属性。拖放这个行为从本质上说是一个带有状态的操作。使用此参数你可以创建诸如isDragging或者canDrop的属性，如果需要根据不同的值来渲染不同的内容，它们会是非常有用的



 DragDrop React.createContext is not a function

重新下载react和react-dom到最新版本

##### ShoppingCart

```
import React, { Component } from 'react';
import PropTypes from 'prop-types'
import { DropTarget } from 'react-dnd'

const ShoppingCartSpec = {
  // 响应放置事件，放置事件发生时，返回一个字符串
  drop() {
    return { name: 'ShoppingCart' }
  }
}
// 在collect函数中把React DnD中的connector和状态映射为组件的props属性
let collect = (connect, monitor) => {
  return {
    // 返回这个组件中的哪一部分DOM是能接收可拖曳对象的目标区域
    connectDropTarget: connect.dropTarget(),
    // isOver和canDrop用于在用户将一个元素拖曳到购物车上方时，为购物车显示不同的文字和背景颜色
    isOver: monitor.isOver(),
    canDrop: monitor.canDrop()
  }
}
class ShoppingCart extends Component {
  render() {
    let backgroundColor = '#fff'
    const { isOver, canDrop, connectDropTarget } = this.props
    const isActive = isOver && canDrop
    if (isActive) {
      backgroundColor = '#f7f7bd'
    } else if (canDrop) {
      backgroundColor = '#f7f7f7'
    }
    const style = {
      backgroundColor: backgroundColor
    }

    return (
      connectDropTarget(
        <div className='shopping-cart' style={style}>
          {isActive ?
            'Hummmm, snack!' :
            'Drag here to order!'}
        </div>
      )
    )
  }
}
ShoppingCart.propTypes = {
  connectDropTarget: PropTypes.func.isRequired,
  isOver: PropTypes.bool.isRequired,
  canDrop: PropTypes.bool.isRequired
}
// 可放置到改组件的type：snack
export default DropTarget('snack', ShoppingCartSpec, collect)(ShoppingCart)
```

##### Snack

```
import React, { Component } from 'react';
import PropTypes from 'prop-types'
import { DragSource } from 'react-dnd'

const snackSpec = {
  // 返回一个字符串
  beginDrag(props) {
    return {
      name: props.name
    }
  },
  // 需要对最终返回的值做一些处理
  // 会得到正在拖曳的元素返回的字符串、正准备放置的目标元素返回的字符串
  endDrag(props, monitor) {
    const dragItem = monitor.getItem()
    const dropResult = monitor.getDropResult()
    if (dropResult) {
      console.log(`you dropped ${dragItem.name} into ${dropResult.name}`)
    }
  }
}
// collect会连接拖曳元素的DOM，并把拖曳状态映射委组建的props属性
// 因为我们正在映射拖曳状态，可以借此机会做两件事：
// 声明额外的propTypes；在内嵌样式规则中使用isDragging属性，在元素拖曳过程中改变其透明度
let collect = (connect, monitor) => {
  return {
    connectDragSource: connect.dragSource(),
    isDragging: monitor.isDragging()
  }
}
class Snack extends Component {
  render() {
    const { name, isDragging, connectDragSource } = this.props
    const opacity = isDragging ? 0.4 : 1
    const style = {
      opacity: opacity
    }
    return (
      connectDragSource(
        <div className='snack' style={style}>
          {name}
        </div>
      )
    )
  }
}
Snack.propTypes = {
  name: PropTypes.string.isRequired,
  connectDragSource: PropTypes.func.isRequired,
  isDragging: PropTypes.bool.isRequired
}
// 封装导出高阶组件
export default DragSource('snack', snackSpec, collect)(Snack)
```

##### css

```
.snack {
  display: inline-block;
  padding: .5em;
  margin: 0 1em 1em 0.25em;
  border: 4px solid #d9d9d9;
  background: #f7f7f7;
  height: 5rem;
  width: 5rem;
  border-radius: 5rem;
  cursor: pointer;
  line-height: 5em;
  text-align: center;
  color: #333;
}

.shopping-cart {
  border: 5px dashed #d9d9d9;
  border-radius: 10px;
  padding: 5rem 2rem;
  text-align: center;
}
```

## 路由

### 使用原生方式实现路由 

```
import React, { Component } from 'react';
import { render } from 'react-dom';
import Home from '../public/route-simple/home'
import About from '../public/route-simple/about'
import Repos from '../public/route-simple/repos'

class App extends Component {
  constructor() {
    super(...arguments)
    this.state = {
    // 注意是hash
      route: window.location.hash.substr(1)
    }
  }
  componentDidMount() {
    window.addEventListener('hashchange', () => {
      this.setState({
        route: window.location.hash.substr(1)
      })
    })
  }
  render() {
    var Child
    switch (this.state.route) {
      case '/about': Child = About; break
      case '/reops': Child = Repos; break
      default: Child = Home; break
    }
    return (
      <div>
        <a href='#/about'>about</a>
        <br />
        <a href='#/repos'>repos</a>
        <Child />
      </div>
    )
  }
}

render(<App />, document.getElementById('root'))
```

#### 问题

- 上例是在监听url的变化将应用程序转换到一个不同的状态
- 每次单击都需要不同的路由代码处理，相关代码会显著地成倍增长

为处理比基本单层路由更复杂的场景，推荐的方法是使用React Router库，声明式API能内置地处理嵌套的url和嵌套的组件层级



### React Router

- 通过将组件与路由关联起来，使UI与URL保持同步，用户更改URL时，组件就被自动卸载并加载

- 提供了一种机制来让你可以控制应用程序的流程，不管用户是通过编程方式进入某个状态，还是用户通过单击一个URL来到达某个状态，代码都可以在不同情况下以一致的方式来运行

  npm install react-router --save

  v4的同时要下载

  npm install react-router-dom --save 

提供了3个组件： 

- Router和Route：用来采用声明方式将路由映射到你的应用程序的UI层级
- Link：用来使用恰当的href来创建一个完全可访问的锚定标记。当然这不是唯一一种在项目中导航的方法，但是这通常是用户最主要使用的一种方式

```
import React, { Component } from 'react';
import { render } from 'react-dom';
import { BrowserRouter, Route, Link } from 'react-router-dom'
import Home from '../public/route-simple/home'
import About from '../public/route-simple/about'
import Repos from '../public/route-simple/repos'

class App extends Component {
  render() {
    return (
      <div>
        <ul>
          <li><Link to='/about'>About</Link></li>
          <li><Link to='/repos'>repos</Link></li>
        </ul>
        {/* React Router会自动根据当前的路由将children这个props设置为相应的组件 */}
        11{this.props.children}
      </div>
    )
  }
}
// 最后声明路由，之前是将App组件渲染到DOM上，但现在改成将带有路由信息的Router组件传递给React DOM render方法
render((
  <BrowserRouter>
    <App>
      <Route path='about' component={About} />
      <Route path='repos' component={Repos} />
    </App>
  </BrowserRouter>
), document.getElementById('root'))
```



命名组件： 通常一个路由只映射到单个组件，这样的话只需要在父组件上使用this.props.children就可以了。但是还可以在设置路由时声明一个或多个命名组件。这种情况下，父组件就需要再props.children上通过组件的名称俩使用组件了

`<Route path="groups" components={{content: Groups, sidebar: SideBar}}/>`

`{this.props.children.siderbar}-{this.props.children.content}`

#### 上面的，书太老了， 好像不得行

####  简单使用reouter

```
import React, { Component } from 'react';
import { render } from 'react-dom';
import { BrowserRouter as Router, Route, Link, IndexRoute } from 'react-router-dom'
import Home from '../public/route-simple/home'
import About from '../public/route-simple/about'
import Repos from '../public/route-simple/repos'

class App extends Component {
  render() {
    return (
      <Router>
        <div>
          <ul>
            <li><Link to="/">home</Link></li>
            <li><Link to="/about">about</Link></li>
            <li><Link to="/repos">repos</Link></li>
          </ul>
          <Route path="/" component={Home} />
          <Route path="/about" component={About} />
          <Route path="/repos" component={Repos} />
        </div>
      </Router>
    )
  }
}
render(<App />, document.getElementById('root'))
```



## flux

flux是facebook创建的一份针对构建web应用程序的架构指引

flux的要点是允许应用程序中存在单项数据流

- action
- store
- dispatcher

### store

store存放着所有应用程序状态（包括数据甚至UI状态），并会在状态发生变化时分发事件。view（react组件）订阅了包含它们所需的数据的store，当数据发生变化时，它就会重新渲染自身

store公开了公共getter来访问数据，但谁也没有办法插入、更新或修改它们的数据，view和flux的其他部分都没有这个能力。只有store自身可以操作其数据。

修改数据使用action

store拥有state，并通过dispatcher注册了自己。每次分发action时，所有store都会被调用，并且可以决定它们是否关心该action。如果一个store器表示关心，它就会更改它内部的state，并发送一个事件，这样view就能得到store发生变化的通知了

### action

每个action都包含一个type（它的唯一名称）和一个可选的payload（数据载荷的对象）。当被分发后，action就会到达store，这就是store得知它应当更新其数据的方法

flux： react组件创建一个action，action到达store，store更新数据，view响应store事件，重新渲染

### dispatcher

负责协调action传递到store的过程，并确保以正确的顺序执行store的action处理程序

view -> action -> dispatcher -> stores -> view

创建一个实例，然后使用它，其余的就交由dispatcher的实现去处理

### 简易demo

#### 结构

安装 npm install flux --save

- constants.js：因为所有action都应当具备一个应用程序级别的唯一标识名，你会将这些名称存储为常量

- 标准的AppDispatcher.js

  扩展了标准的flux dispatcher 来记录每一次分发

- BankAction.js，包含了三个action创建器：CreateAccout, depositInfoAccount, WithdrawFromAccount

- BankBalanceStore.js：将跟踪用户的余额

  要发送事件，你需要一个来自npm的事件发射器包。nodejs拥有一个默认的事件发射器，但浏览器并不支持它。npm install fbemitter --save

#### constants.js

```
export default {
  CREATED_ACCOUNT: 'created account',
  WITHDREW_FROM_ACCOUNT: 'withdrew from account',
  DEPOSITED_INTO_ACCOUNT: 'deposited into account'
}
```

#### AppDispatcher.js

```
import { Dispatcher } from 'flux'

class AppDispatcher extends Dispatcher {
  dispatch(action = {}) {
    console.log('Dispatcher', action)
    super.dispatch(action)
  }
}

export default new AppDispatcher()
```

#### BankAction.js

```
import AppDispatcher from './AppDispatcher'
import bankConstants from './constants'

let BankActions = {
  // 创建空账户
  createAccount() {
    AppDispatcher.dispatch({
      type: bankConstants.CREATED_ACCOUNT,
      ammount: 0
    })
  },
  depositIntoAccount(ammount) {
    AppDispatcher.dispatch({
      type: bankConstants.DEPOSITED_INTO_ACCOUNT,
      ammount: ammount
    })
  },
  withdrawFromAccount(ammount) {
    AppDispatcher.dispatch({
      type: bankConstants.WITHDREW_FROM_ACCOUNT,
      ammount: ammount
    })
  }
}
export default BankActions

```

#### BankBalanceStore.js

```
import { EventEmitter } from 'fbemitter'
import AppDispatcher from './AppDispatcher'
import bankConstants from './constants'

const CHANGE_EVENT = 'change'
let __emitter = new EventEmitter()
// 存储账户余额
let balance = 0
let BankBalanceStore = {
  // 一个访问该值的getter方法
  getState() {
    return balance
  },
  addListener: (callback) => {
    return __emitter.addListener(CHANGE_EVENT, callback)
  }
}
// 调用了dispatcher的注册方法，并传入一个人回调函数
// register方法返回一个分发令牌：这是一个可用来协调store更新顺序的标识符
BankBalanceStore.dispatchToken = AppDispatcher.register(action => {
  // 每次分发action时都会调用该回调函数，所以你有机会在特定action type被分发时，store是否要做某些事情
  switch (action.type) {
    case bankConstants.CREATED_ACCOUNT:
      balance = 0
      // 更改余额后，手动发送一个变化事件
      __emitter.emit(CHANGE_EVENT)
      break
    case bankConstants.DEPOSITED_INTO_ACCOUNT:
      balance = balance + action.ammount
      __emitter.emit(CHANGE_EVENT)
      break
    case bankConstants.WITHDREW_FROM_ACCOUNT:
      balance = balance - action.ammount
      __emitter.emit(CHANGE_EVENT)
      break
  }
})

export default BankBalanceStore
```

#### App.js

```
import React, { Component } from 'react';
import { render } from 'react-dom';
import BankBalanceStore from './BankBalanceStore'
import BankActions from './BankAction'
class App extends Component {
  constructor() {
    super(...arguments)
    BankActions.createAccount()
    this.state = {
      balance: BankBalanceStore.getState()
    }
  }
  componentDidMount() {
    // 只要store发生变化，handleStoreChange方法就会被调用，组件的state也会被刷新
    this.storeSubscription = BankBalanceStore.addListener(
      data => this.handleStoreChange(data)
    )
  }
  componentWillUnmount() {
    this.storeSubscription.remove()
  }
  handleStoreChange() {
    this.setState({
      balance: BankBalanceStore.getState()
    })
  }
  deposit() {
    BankActions.depositIntoAccount(Number(this.refs.ammount.value))
    this.refs.ammount.value = ''
  }
  withdraw() {
    BankActions.withdrawFromAccount(Number(this.refs.ammount.value))
    this.refs.ammount.value = ''
  }
  render() {
    return (
      <div>
        <header>flux bank</header>
        <h1>your balance is ${(this.state.balance).toFixed(2)}</h1>
        <div className="atm">
          <input type="text" ref="ammount" />
          <br />
          <button onClick={this.withdraw.bind(this)}>withdraw</button>
          <button onClick={this.deposit.bind(this)}>deposit</button>
        </div>
      </div>
    )
  }
}

render(<App />, document.getElementById('root'))
```

### flux 工具包

从2.1版本开始，flux库就包含了用来定义store的基类，还有一个搭配容器组件一起使用的高阶函数——允许组件在相关的store发生变化后自动更新其state。这些使用程序的价值在于它们有助于减少应用程序中乏味的样板代码

工具包提供了三个用来实现store的基类：

- store
  - 最简单的一个，只是基础store的一个轻量级包装。它有助于应付样板代码，但没有带来任何新的概念或功能
- ReduceStore
  - 非常特殊的store，名字来源于它使用化简函数来修改内部state这一事实。reducer是一个用来根据之前给定的state和action来计算出新state的函数，与array.peototype.reduce的功能类似。ReduceStore中的state会被当作不可变量来处理，所以要注意只存储不可变的结构，或者以下范围内的值:
    - 单一原始值（字符串、布尔值、数字）
    - 一个原始值组成的数组: [1,2,3]
    - 一个原始值组成的对象
    - 一个包含内嵌对象的对象，并将使用react不变性助手来操作这些对象
- MapStore
  - ReduceStore的一个变种，它包含一个额外的辅助函数来存储键值对（而非但一值）

ReduceStore（以及MapStore）的另一个亮点是你无须手动触发变化事件：每次分发前后都会自动比较state并发送变化事件

store基类实现和BankBalanceStore主要区别

- 不需要创建事件发射器的实例
- 不需要手工将store注册到dispatcher：而是在创建store实例时，将dispatcher作为参数传入

```
import AppDispatcher from './AppDispatcher'
import { Store } from 'flux/utils'
import bankConstants from './constants'

let balance = 0
class BankBalanceStore extends Store {
  getState() {
    return balance
  }
  __onDispatch(action) {
    switch (action.type) {
      case bankConstants.CREATED_ACCOUNT:
        balance = 0
        this.__emitChange()
        break
      case bankConstants.DEPOSITED_INTO_ACCOUNT:
        balance = balance + action.ammount
        this.__emitChange()
        break
      case bankConstants.WITHDREW_FROM_ACCOUNT:
        balance = balance - action.ammount
        this.__emitChange()
        break
    }
  }
}
export default new BankBalanceStore(AppDispatcher)
```

与使用Store相比，使用ReduceStore的实现才真正精彩。除了更简洁外，它对不可变数据结构的使用能让编程变得更具有声明性（就像react），并且对许多其他领域（比如测试）都产生了积极影响

不需要发送事件变化，它们总是会自动进行分发

- getInitialStore
  - 定义store的初始state
- reduce
  - 根据action来修改state

```
import AppDispatcher from './AppDispatcher'
import { ReduceStore } from 'flux/utils'
import bankConstants from './constants'
class BankBalanceStore extends ReduceStore {
  getInitialState() {
    return 0
  }
  reduce(state, action) {
    switch (action.type) {
      case bankConstants.CREATED_ACCOUNT:
        return 0
      case bankConstants.DEPOSITED_INTO_ACCOUNT:
        return state + action.ammount
      case bankConstants.WITHDREW_FROM_ACCOUNT:
        return state - action.ammount
      default:
        return state
    }
  }
}
export default new BankBalanceStore(AppDispatcher)
```

#### 容器组件高阶函数

要使用flux工具包中的高阶函数，容器组件就不能访问任何的props。这既是出于性能原因，也是为确保容器可被重用，并且无须在整个组件树中处理props

- 不用初始化state，自动订阅BankAcountStore并在其发生变化时自动更新自己的state
- componentDidMount 、componentWillUnmount 、handleStoreChange 可以删除，高阶组件会解决定于和取消订阅store的事宜

```
import React, { Component } from 'react';
import { render } from 'react-dom';
import { Container } from 'flux/utils'
import BankBalanceStore from './BankBalanceStore'
import BankActions from './BankAction'
class App extends Component {
  constructor() {
    super(...arguments)
    BankActions.createAccount()
  }
  deposit() {
    BankActions.depositIntoAccount(Number(this.refs.ammount.value))
    this.refs.ammount.value = ''
  }
  withdraw() {
    BankActions.withdrawFromAccount(Number(this.refs.ammount.value))
    this.refs.ammount.value = ''
  }
  render() {
    return (
      <div>
        <header>flux bank</header>
        <h1>your balance is ${(this.state.balance).toFixed(2)}</h1>
        <div className="atm">
          <input type="text" ref="ammount" />
          <br />
          <button onClick={this.withdraw.bind(this)}>withdraw</button>
          <button onClick={this.deposit.bind(this)}>deposit</button>
        </div>
      </div>
    )
  }
}
App.getStores = () => ([BankBalanceStore])
App.calculateState = (prevState) => (
  { balance: BankBalanceStore.getState() }
)
const AppContainer = Container.create(App)

render(<AppContainer />, document.getElementById('root'))
```

### 异步flux

某个store依赖于来自另一个store的数据，flux dispatcher提供了一个waitFor()方法来管理这种依赖关系，它能让store在继续执行之前，先等待来自指定store的回调被调用

新增一个等级store文件

```
import AppDispatcher from './AppDispatcher'
import BankBalanceStore from './BankBalanceStore'
import { ReduceStore } from 'flux/utils'
import constants from './constants'

class BankRewordStore extends ReduceStore {
  getInitialState() {
    return 'basic'
  }
  reduce(state, action) {
    this.getDispatcher().waitFor([
      BankBalanceStore.getDispatchToken()
    ])
    if (action.type === constants.WITHDREW_FROM_ACCOUNT || action.type === constants.DEPOSITED_INTO_ACCOUNT) {
      let balance = BankBalanceStore.getState()
      if (balance < 500) {
        return 'basic'
      } else if (balance >= 500 && balance < 1000) {
        return 'sliver'
      } else {
        return 'gold'
      }
    }
    return state
  }
}
export default new BankRewordStore(AppDispatcher)
```

app.js中增加订阅

```
import BankRewordStore from './BankRewordStore'
<h1>your reword is {(this.state.reword)}</h1>
App.getStores = () => ([BankBalanceStore, BankRewordStore])
App.calculateState = (prevState) => (
  {
    balance: BankBalanceStore.getState(),
    reword: BankRewordStore.getState()
  }
)
```

获取数据异步API工具模块应该总是分发至少三种不同类型的action：

- 一个告知store请求已经开始的action
- 一个告知store请求成功完成的action
- 一个告知store请求失败的action

把和API的交互封装到一个单独的模块里并且分发不同的action，将系统其余部分隔绝到异步执行之外

## 性能调优

- 子集校正算法
- 手动提升组件性能：
  - shouldComponentUpdate生命周期来阻止组件（及其整个子树）的重绘（shallowCompare）

## 同构应用

服务器渲染react组件

优势：更高的性能、搜索引擎优化、优雅降级