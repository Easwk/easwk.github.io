## React的数据载体：state、props、context对比

#### 一、state

描述：内部状态（local state）或者局部状态；

初始化内部状态：React构造函数中

更新内部状态：this.setstate

获取内部状态：this.state

```
class Counter extends Component{
	constructor(){
		super(); //后才能用this获取实例化对象
		this.state{
			value: 0
		};
	}

	render(){
		return(
			<div>
				<button onClick = { () => this.setState({value：this.state.value+1})}>
					INCREMENT
				</button>
				Counter组件的内部状态
				<pre>JSON.stringify(this.state.value, null, 2)</pre>
			</div>
			);
	}
}
```

#### 二、Props

可以使用它向组件传递数据，React组件从props拿到数据，然后返回视图；

##### 向一个组件传递数据的方法是将数据写在组件的标签中；

```
<Content value = {this.state.value}/>
```

##### 组件如何获取props呢？无状态函数编写的组件中获取props属性非常简单；

```
function Content(props){
	return(
			<p>Content组件的props.value: {props.value}</p>
		);
}
```

##### 在使用类编写组件的时候，需通过this.props获取props,this是组件的实例。

#### 三、组合使用state与props

思路：通过Counter组件更新state.value,然后将更新的state.value通过props传递给Content组件

```
import React, {Component, ProTypes} from 'react';

function Content(props){
	return <p>Content组件的props.value: {props.value}</p>;
}

Content.ProTypes = {
	value: ProTypes.number.isRequired
};

class Counter extends Component{
	constructor(){
		super();
		this.state{
			value: 0
		}
	}

	render(){
		return(
			<div>
				<button onClick= {() => {this.setstate({value: this.state.value+1})}}
					INCREMENT
				</button>
					Counter组件的内部状态：
				<pre>JSON.stringify(this.state.value, null, 2)</pre>
				<Content value = {this.state.value}/>
			</div>
			);
	}
}
```

#### 四、Context

组件间跨级传递数据

---
Context 与props传递数据的对比

##### 1、使用props传递数据

编写三个组件：按钮(Button)、消息(Message)、消息列表(MessageList)

在最外层的消息列表组件中定义一个变量color,然后通过props将color传递给内层按钮组件；

父组件——>子组件
```
import React, {ProTypes} from react;

function Button(props){
	return(
			<button style = {{background: props.color}}>
				{props.children} //把所有子元素(子组件)显示出来，这里是Delete
			</button>
		);
}

Button.proTypes = {
	color: ProTypes.string.isRequired,
	children: ProTypes.string.isRequired
}

function Message(props){
	return(
			<li>
			  props.text<Button color = {props.color}>Delete</Button>
			</li>
		);
}

Message.proTypes = {
	color: ProTypes.string.isRequired,
	text: ProTypes.string.isRequired
}

function MessageList(){
	const color = 'red';
	const messages = [
		{text: 'hello React'},
		{text: 'hello Redux'}
	];

	const children = messages.map((message, key) => 
			<Message key = {key} text = {message.text} color = {color}/>
		);

	return(
		<div>
			<p>通过props将color逐层传递给自子组件Button</p>
			<ul>
				{children}
			</ul>
		</div>
		);
}

export default MessageList;
```

注意：

1)、在MessageList组件中遍历生成Message组件，并向其传递了key text color等props,虽然key这个props没有用到，但是当你遍历生成一个组件列表的时候，仍然要给列表中的每一项传递key。这是因为React的DIFF算法需要key来做重新排列，以高效的更新DOM；

2)、消息组件中没有子元素，要写自闭形式；

3)、在消息组件中，渲染按钮组件，并向其传递了color；

4)、在按钮组件中，渲染了一个普通按钮，并声明式的定义了它的背景颜色为上级组件传递来的color；

---

#### 2、使用context传递数据

1）将要传递的数据放在消息列表组件的context中；

2）在按钮组件中声明contextTypes,就可以通过组件实例的context属性访问到数据了；
```
import React, {Component, ProTypes} from 'react';

function Button(props, context){
	return(
			<button style = {{background: context.color}}>
				{props.children}
			</button>
		);
}

Button.proTypes = {
	color: ProTypes.string.isRequired,
	children: ProTypes.string.isRequired
};

Button.contextTypes = {
	color: ProTypes.string.isRequired
};

function Message(props){
	return(
			<li>
				props.text<Button Delete></Button>
			</li>
		);
}

Message.proTypes = {
	text: ProTypes.string.isRequired
};

class MessageList extends Component{
	getChildContext(){
		return {color: 'red'};
	}

	render(){
		const messages = [
			{text: 'hello React'},
			{text: 'hello Redux'}
		];

		const children = messages.map((message, key) =>
				<Message key = {key} text = {message.text}/>
			);

		return(
				<div>
					<p>通过context将color跨级传递给里面的Button组件</p>
					<ul>
						{children}
					</ul>
				</div>
			);
	}
}

MessageList.childContextTypes = {
	color: ProTypes.string.isRequired;
}

export default MessageList;
```
注意：

1）在消息列表组件中遍历生成消息组件，并向其传递key和text这两个props。然后通过getChildContext方法将color放在context中，并声明了childrenContextTypes。如果不声明的话，将无法在组件中使用getChildContext()方法；

2)在消息组件中，渲染按钮组件，就不用再直接传递color了；

3）在按钮组件中，首选传递父组件的props和全局的context,然后渲染一个普通的按钮，并声明式的定义它的背景颜色为context.color。如果没定义contextTypes，context将会是空对象；

另外，无状态组件可以直接在函数参数中获取context，而又状态组件可以通过this.context和生命周期函数获取context；

---

##### 3、使用场景

##### React的context和全局变量相似，应避免使用，场景包括：传递登录信息、当前语音以及主题信息；

##### 如果只传递一些功能模块数据，则尽量不要使用context，使用props传递数据会更加清晰；

##### 使用context会使组件的复用性降低，因为这些组件依赖'上下文'，当你在别的地方渲染的时候，可能会出现差异；

#### 五、总结

##### 1、state称为内部状态或者局部状态，内部状态的操作配合React事件系统，可以实现用户交互的功能

##### 2、Props与context则用于在组件间传递数据。使用props传递数据简单清晰，但是跨级传递非常麻烦。

##### 使用context可以跨级传递数据，但是会降低组件的复用性，因为这些组件依赖“上下文”，。所有尽量只使用context传递登录状态、颜色主题等全局数据。

##### 3、在React开发者工具中，可以清晰地看到每个组件的state、props以及context等信息，便于开发和调试。
