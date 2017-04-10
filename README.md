### React 组件数据流 && 组件间简单沟通


---
### 数据流
---

React是单向数据流，从父节点传递到子节点（通过props）。如果顶层的某个props改变了，React会重渲染所有的子节点（未做性能优化）。严格意义上React只提供，也强烈建议使用这种数据交流方式。

##### Props

props是property的缩写，可以理解为HTML标签的attribute。请把props当做只读的（不可以使用this.props直接修改props），props是用于整个组件树中传递数据和配置。在当前组件访问props，使用this.props。

```
class Component {
  constructor(props){
    super(props);
  }
  render(){
    return (
        <div title={this.props.title}></div>
    )
  }
}
<Component title="test"/>//调用title就传进去了
```

##### PropTypes

PropsTypes是React中用来定义props的类型，不符合定义好的类型会报错。建议可复用组件要使用prop验证！接着上面的列子设置PropsTypes如下：

```
class Component {
  ...
}
Component.PropsType = {
  title: React.PropTypes.string,
}
```
##### defaultProps

如何设置组件默认的props？

```
//React提供的crateClass创建方式
var Component = React.createClass({
  getDefaultProps(){
    return {
      //这里设置defaultProps
    }
  }
})
//ES6
class Component {
  ...
}
Component.defaultProps = {}
//ES7 stage-0
class Component {
  static defaultProps = {
    
  }
  ...
}
```

##### state

每个组件都有属于自己的state，state和props的区别在于前者之只存在于组件内部，只能从当前组件调用this.setState修改state值（不可以直接修改this.state）。一般我们更新子组件都是通过改变state值，更新子组件的props值从而达到更新。

那如何设置默认state?

```
//React提供的crateClass创建方式
var Component = React.createClass({
  getInitialState(){
    return {
      //这里设置初始state值
    }
  }
})
//ES6 && ES7
class Component {
  constructor(){
    this.state = {}//在ES6中的构造函数中初始化，可以之直接赋值，在其他方法中，只能使用this.setState
  }
  ...
}
```

###### 尽可能使用props当做数据源，state用来存放状态值（简单的数据），如复选框、下拉菜单等。

---
### 组件沟通
---

组件沟通因为React的单向数据流方式会有所限制，下面述说组件之间的沟通方式。

##### 父子组件沟通

这种方式是最常见的，也是最简单的。

1、父组件更新组件状态

父组件更新子组件状态，通过传递props，就可以了。

2、子组件更新父组件状态

这种情况需要父组件传递回调函数给子组件，子组件调用触发即可。

###### 示例：

```
class Child extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  render(){
    return (
      <div>
        {this.props.text}
        <br />
        <button onClick={this.props.refreshParent}>
            更新父组件
        </button>
      </div>
    )
  }
}
class Parent extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  refreshChild(){
    return (e)=>{
      this.setState({
        childText: "父组件沟通子组件成功",
      })
    }
  }
  refreshParent(){
    this.setState({
      parentText: "子组件沟通父组件成功",
    })
  }
  render(){
    return (
      <div>
        <h1>父子组件沟通</h1>
        <button onClick={this.refreshChild()} >
            更新子组件
        </button>
        <Child 
          text={this.state.childText || "子组件未更新"} 
          refreshParent={this.refreshParent.bind(this)}//传递cb
        />
        {this.state.parentText || "父组件未更新"}
      </div>
    )
  }
}
```

#### 兄弟组件沟通

当两个组件有相同的父组件时，就称为兄弟组件（堂兄也算的）。按照React单向数据流方式，我们需要借助父组件进行传递，通过父组件回调函数改变兄弟组件的props。


###### 方式一:通过props传递父组件回调函数。

```
class Brother1 extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  render(){
    return (
      <div>
        <button onClick={this.props.refresh}>
            更新兄弟组件
        </button>
      </div>
    )
  }
}
class Brother2 extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  render(){
    return (
      <div>
         {this.props.text || "兄弟组件未更新"}
      </div>
    )
  }
}
class Parent extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  refresh(){
    return (e)=>{
      this.setState({
        text: "兄弟组件沟通成功",
      })
    }
  }
  render(){
    return (
      <div>
        <h2>兄弟组件沟通</h2>
        <Brother1 refresh={this.refresh()}/>
        <Brother2 text={this.state.text}/>
      </div>
    )
  }
}
```

但是如果组件层次太深，上面的兄弟组件沟通方式就效率低了（不建议组件层次太深）。

###### 方式二:React提供了一种上下文方式（挺方便的），可以让子组件直接访问祖先的数据或函数，无需从祖先组件一层层地传递数据到子组件中。

```
class Brother1 extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  render(){
    
    return (
      <div>
        <button onClick={this.context.refresh}>
            更新兄弟组件
        </button>
      </div>
    )
  }
}
Brother1.contextTypes = {
  refresh: React.PropTypes.any
}

class Brother2 extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  render(){
    return (
      <div>
         {this.context.text || "兄弟组件未更新"}
      </div>
    )
  }
}
Brother2.contextTypes = {
  text: React.PropTypes.any
}

class Parent extends React.Component{
  constructor(props){
    super(props);
    this.state = {}
  }
  
  getChildContext(){
    return {
      refresh: this.refresh(),
      text: this.state.text,
      }
    }
  
  refresh(){
    return (e)=>{
      this.setState({
        text: "兄弟组件沟通成功",
      })
    }
  }
  render(){
    return (
      <div>
        <h2>兄弟组件沟通</h2>
        <Brother1 />
        <Brother2 text={this.state.text}/>
      </div>
    )
  }
}
Parent.childContextTypes = {
  refresh: React.PropTypes.any,
  text: React.PropTypes.any,
}
```

##### 全局事件
---
```
For communication between two components that don't have a parent-child relationship,
you can set up your own global event system. 
Subscribe to events in componentDidMount(), unsubscribe in componentWillUnmount(), 
and call setState() when you receive an event.Flux pattern is one of the possible ways to arrange this.
```

官网中提到可以使用全局事件来进行组件间的通信，官网推荐Flux（Facebook官方出的），还有Relay、Redux、trandux等第三方类库。这些框架思想都一致，都是统一管理组件state变化情况，达到数据可控目的。

###### 总结:

简单的组件交流我们可以使用上面非全局事件的简单方式，但是当项目复杂，组件间层次越来越深，上面的交流方式就不太合适（当然还是要用到的，简单的交流）。强烈建议使用Flux、Relay、Redux、trandux等类库其中一种，这些类库不只适合React，像Angular等都可以使用。
