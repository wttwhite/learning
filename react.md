##react

### props 父组件向子组件传输数据

```
class App extends Component {
  render () {
    let hello = 'hello'
    return (
      < ul >
        <ListItem a="1" > q {hello}</ListItem>
      </ul >
    )
  }
}

class ListItem extends Component {
  render () {
    return (
      <li>
        {this.props.a}*{this.props.children}
      </li>
    )
  }
}
render(< App />, document.getElementById('root'))
```

子组件中:

this.props.a 取到使用是传入的属性

this.props.children  取到两个标签内文字

**props在子组件中不能被修改**



### state

对于组件来说是私有的

this.setState() 修改值 -> 触发响应式渲染 -> 组件自身及其子组件都会被重新渲染

组件内的state尽量精简, 因为每次state发生变化, 组件都会重新渲染

```
class ListItem extends Component {
  constructor() {
    super(...arguments)
    // 'this' is not allowed before super()
    this.state = {
      show: true
    }
  }
  render () {
    let html = ''
    // 条件外移判断
    if (this.state.show) {
      html = <span>{this.props.a}*{this.props.children}</span>
    }
    return (
    // 变量外要有一个标签包着
      <li onClick={() => this.setState({ show: !this.state.show })}>
      {/* 显示地显示一个空格 , 子域中注释要{}包住 */}
      	{" "}
        {html}
      </li>
    )
  }
}
```

### 事件

```
  // 与constructor()同级
  toggleDetails () {
  	this.setState({ show: !this.state.show })
  }
  // onClick事件
  // 显式地将函数绑定到上下文
  onClick={ this.toggleDetails.bind(this) }
```

### JSX和HTML的不同之处

1. 标签特性采取驼峰式大小写风格 ( maxlength -> maxLength )
2. 所有元素都必须闭合 ( 包括br , img )
3. 特性名称基于DOM API 而非 HTML 语言规范 ( class -> calssName )

#### 单一根节点

```
return (
    <h1>hello world</h1>
)
转化成: 
return React.createElement("h1"), null, "hello world"
非法: 
return (
    <h1>hello world</h1>
)
```

#### 条件语句外移

```
三元表达式
return (
    <div className={a ? "active": ""}></div>
)
if语句
render () {
    let calssName = ''
    if (a) {
      className="active"
    }
    return (
    	<div className={className}}></div>
    )
}
```

#### 空格

想要显式地插入一个空格, 可以使用包含了一个空格的{" "}表达式

#### 注释

与js的注释方式一致, 除了在一个标签的子域中, 要用{}来包围注释

```
return (
    // 变量外要有一个标签包着
    <li onClick={() => this.setState({ show: !this.state.show })}>
      {/* 显示地显示一个空格 , 子域中注释要{}包住 */}
      {" "}
      {html}
    </li>
)
```

### 渲染动态HTML

react内置了XSS攻击保护措施, 这意味着在默认情况下, 它不允许动态生成HTML标签并附加到JSX中, react提供 dangerouslySetInnerHTML属性来跳过XSS保护并直接渲染任何内容

```
<span dangerouslySetInnerHTML={{__html: marked(md内容)}}> {双下划线}
```

### 普通js的react元素, 不用jsx

```
import React, { Component } from 'react';
import { render } from 'react-dom';

// React.createElement(标签名或组件, 一个属性对象, 数量不定的可选子参数)
let child1 = React.createElement('li', null, 'first')
let child2 = React.createElement('li', null, 'second')
let root = React.createElement('ul', {
  className: 'ul-class'
}, child1, child2)

render(root, document.getElementById('root'))
```

### 元素工厂

工厂函数:  1. 它是一个函数  2. 用来创建对象   3. 它像一个工厂, 生产出来的函数都拥有同样的属性

为了方便起见, React在React.DOM下为常见的HTML标签提供了工厂函数简写(React.DOM.HtmlTag): 

```
let root = React.DOM.form({ className: 'form-diy' },
  React.DOM.input({ type: 'text', placeholder: '请输入姓名' }),
  React.DOM.input({ type: 'text', placeholder: '请输入评论' }),
  React.DOM.input({ type: 'submit', value: '提交' })
)
render(root, document.getElementById('root'))

// 利用解构赋值
let { form, input } = React.DOM

//自定义工厂
let factory = React.createFactory('li')
let child1 = factory(null, 'first')
let child2 = factory(null, 'second')
```



### 内联样式

好处:

1. 不需要选择器来限定样式的范围
2. 避免特异性冲突
3. 源顺序无关

```
render () {
// 单位react会自动补全
    let divStyle = {
        width: 100,
        height: 30,
        padding: 5,
        backgroundColor: '#eee'
    }
    return <div style={divStyle}></div>
}
```



### 表单

##### 受控组件: 一个包含值或已选属性的表单组件, 默认情况下不能更改

```
class Search extends Component {
  render () {
    return (
    	/*用户输入无法更改value的值*/
       <input type="search" value="react" /> 
    )
  }
}
// Warning: Failed form propType: You provided a `value` prop to a form field without an `onChange` handler. This will render a read-only field. If the field should be mutable use `defaultValue`. Otherwise, set either `onChange` or `readOnly`. Check the render method of `Search`.
// 设置readOnly后不报错

// 用state和change方法可以改变值
class Search extends Component {
  constructor() {
    super()
    this.state = {
      searchValue: 'react'
    }
  }
  searchChange (event) {
    this.setState({ searchValue: event.target.value })
  }
  render () {
    return (
      /*用户输入无法更改value的值*/
      < input type="search" value={this.state.searchValue} onChange={this.searchChange.bind(this)} />
    )
  }
}
```

好处:

1. 它遵循了react处理组件的方式, state保存于界面之外,并且完全由你的js代码来管理

2. 这种模式让实现界面来响应或验证用户交互变得简单, 例如: 限制用户输入长度:

   this.setState({ searchValue: event.target.value.substr(0, 50) })

**textarea 和 select 值都用value属性**



##### 非受控组件: 不为任何输入域提供值, 渲染后的元素的值将反映用户的输入;设置初始值, 应用defaultValue属性

```
class Form extends Component {
  handlerSubmit (event) {
    console.log('event', event.target.name.value, event.target.password.value)
    event.preventDefault()
  }
  render () {
    return (
      <form onSubmit={this.handlerSubmit.bind(this)}>
        姓名: <input type="text" name="name"  defaultValue="1"/>
        密码: <input type="password" name="password" />
        <button type="submit">提交</button>
      </form>
    )
  }
}
```



### propTypes

```
import React, { Component, PropTypes } from 'react';
import { render } from 'react-dom';
class Title extends Component {
  render () {
    return (
      <h1>{this.props.title}</h1>
    )
  }
}

// 定义为一个类构造函数属性
Title.propTypes = {
// 定义为一个字符串, 且是必须的
  title: PropTypes.string.isRequired
}
render(<Title title="aaa" />, document.getElementById('root'))

// 设置默认值
Title.propTypes = {
  title: PropTypes.string
}
Title.defaultProps = {
  title: 'Hello world'
}
```

```
内置PropTypes校验器
array  bool  func  number  object  string

属于其中的一种
PropTypes.oneOfType( [
	PropTypes.array, 
	PropTypes.number
])
PropTypes.arrayOf(PropTypes.number) 指定类型数据(数字)的数组
PropTypes.objectOf(PropTypes.number) 指定类型数据(数字)的对象
符合特定格式的对象
PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
})
PropTypes.node  一个可渲染的值: 数字,字符串,元素或数组
PropTypes.element 一个react元素
PropTypes.instanceOf  指定类的实例 PropTypes.instanceOf(Message)
PropTypes.oneOf  确保属性被限制为一组枚举值中的一种, PropTypes.oneOf(['News', 'Photos'])
```

自定义校验, 正确不用返回(vue 正确要返回callback())

```
let titlePropTypes = (props, propName, componentName) => {
  // console.log('props', props) -> {title: 'aaa'}
  // console.log('propName', propName) -> title
  // console.log('componentName', componentName) -> Title
  if (typeof props[propName] !== 'string') {
    return new Error(`${propName}必填`)
  }
}

Title.propTypes = {
  title: titlePropTypes
}
```



### 父子组件之间通信

```
let data = [
  { name: '1', uuid: '1' },
  { name: '2', uuid: '2' },
  { name: '3', uuid: '3' },
  { name: '4', uuid: '4' }
]

class SearchBar extends Component {
  constructor() {
    super()
  }
  handlerChange (event) {
  // 改变input框的值, 传递给父组件数据的函数
    this.props.searchValue(event.target.value)
  }
  render () {
    return (
    // 给个默认值
      <input type="search" onChange={this.handlerChange.bind(this)} defaultValue={this.props.filterText} />
    )
  }
}
class List extends Component {
  render () {
  // 过滤值, 一定要return
    let list = this.props.listData.filter(item => {
      return item.name.indexOf(this.props.filterText) !== -1
    })
    return (
      <ul>
        {list.map(item =>
          <li key={item.uuid}>{item.name} - {item.uuid}</li>
        )}
      </ul>
    )
  }
}

class App extends Component {
  constructor() {
    super()
    // 定义一个值, 传给子组件
    this.state = {
      filterText: ''
    }
  }
  // 接收子组件传过来的值, 传递给另一个子组件
  getSearchValue (value) {
    this.setState({ filterText: value })
  }
  render () {
    return (
      <div>
        <SearchBar searchValue={this.getSearchValue.bind(this)} filterText={this.state.filterText}></SearchBar>
        // 给子组件传值
        <List filterText={this.state.filterText} listData={this.props.listData} ></List>
      </div>
    )
  }
}
```



### 获取数据

```
npm install --save whatwg-fetch
import 'whatwg-fetch'
// 获取数据的容器, public下data.json
class ContactContainer extends Component {
  constructor() {
    super()
    this.state = {
      list: []
    }
  }
  componentDidMount () {
    fetch('./data.json').then(res => res.json())
      .then(resData => this.setState({ list: resData }))
      .catch(error => { console.log('error fetch data', error) })
  }
  render () {
    return (
      <App listData={this.state.list}></App>
    )
  }
}
```



### 不变性

直接赋值只是创建了一个新的引用, 指向了原来的对象或者是数组

返回一个新的数组, 而非直接对原数组进行修改的方法(非侵入式数组方法):  map filter     = a.concat(b)

chrome 和 Firefox 支持Object.assign方法, npm install --save bable-polyfill 提供了代替品

对象和数组都是通过引用方式传递, 并且数组的非侵入式方法和Object.assign都不会做深度复制, 所创建的新对象中包含的嵌套对象和数组仍然指向原来对象中的嵌套对象和数组:  深层的变量改变, 会影响到原变量

react的插件包提供了一个不变性助手:  update ,包装成不可变的对象, 返回一个新的可变对象

npm install --save react-addons-update

import update from 'react-addons-update'

```
// 在何处  做何种修改    b1可以用索引0
let b = update(this.state.a, { b: { b1: { $set: 'qqqqq' } } })
$push 数组尾部添加元素
$unshift  数组头部添加元素
$splice: [ [位置, 删除个数, 添加1, 添加2,...] ]
$set 完整地替换掉整个目标
$merge 合并, 类似于Object.assign
$apply 将value传给一个函数,修改后的值作为结果  {b: { $apply: (value) => value*2 }}
```









### 数组过滤filter

[].filter(card => return card.status === 'done') 返回符合的数据,一定要有return



getBoundingClientRect ()  用于获取某个元素相对于视窗的位置集合。集合中有top, right, bottom, left等属性 



