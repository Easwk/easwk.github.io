### React中Context实现值得越级传递

在React中，数据可以以流的形式自上而下的传递，每当你使用一个组件的时候，你可以看到组件的props属性会自上而下的传递。但是，在某些情况下，我们不想通过父组件的props属性一级一级的往下传递，我们希望在某一级子组件中，直接得到上N级父组件中props中的值。

---

#### 1.一般情况下通过props传值的情况

```
class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.props.color}}>
        {this.props.children}
      </button>
    );
  }
}

class Message extends React.Component {
  render() {
    return (
      <div>
        {this.props.text} <Button color={this.props.color}>Delete</Button>
      </div>
    );
  }
}

class MessageList extends React.Component {
  render() {
    const color = "purple";
    const children = this.props.messages.map((message) =>
      <Message text={message.text} color={color} />
    );
    return <div>{children}</div>;
  }
}
```

分析一下这段代码，大致的组件分为3级:

顶层MessageLists——>Message一级子类——>Button底层子类

我们来看从父组件到子组件的值的传递情况：

（1）text:

我们可以看到，在顶层组件MessageLists中的值，传递到一级子组件Message中，并在此组件中被使用。

（2）color:

再看props中的color的传递情况，在顶层组件MessageLists中的值，先传递到一级子组件Message中，

在传递到二级子组件Button中，最后在二级子组件中被使用。

综上：这就是一般在React中，所使用的通过props属性，在父组件与子组件中进行值传递。

---

#### 2.如何利用React中的Context来进行值的越级传递。

```
class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.context.color}}>
        {this.props.children}
      </button>
    );
  }
}

Button.contextTypes = {
  color: React.PropTypes.string
};

class Message extends React.Component {
  render() {
    return (
      <div>
        {this.props.text} <Button>Delete</Button>
      </div>
    );
  }
}

class MessageList extends React.Component {
  getChildContext() {
    return {color: "purple"};
  }

  render() {
    const children = this.props.messages.map((message) =>
      <Message text={message.text} />
    );
    return <div>{children}</div>;
  }
}

MessageList.childContextTypes = {
  color: React.PropTypes.string
};
```

上述代码，我们实现了通过React的Context实现了值——color的越级传递。

##### （1）首先在顶层组件中：

```
MessageList.childContextTypes = {
  color: React.PropTypes.string
};
```
定义了顶层组件所拥有的子类context对象——该顶层组件所拥有的的子类context对象为color，且必须为字符串。

然后通过getChildcontext方法，来给子context对象的属性赋值：

```
getChildContext() {

    return {color: "purple"};
  }
```
这样就完成了顶层组件中，context对象的赋值。

##### （2）越级传递，因为color属性只在最底层使用

我们来看color属性的越级传递，因为color属性，在一级子组件Message中并没有直接用到，因此我们可以

直接传递到最底层（越级），在Button组件中使用。

首先Button组件中，再次声明了所接受到的context的子组件color的类型，声明必须为字符串：

```
Button.contextTypes = {
  color: React.PropTypes.string
};
```
 
然后可以通过this.context.color这种方式调用：

```
 <button style={{background: this.context.color}}>
        {this.props.children}
 </button>
```

##### 综上：这样，我们发现通过Context，我们就能实现值得越级传递。




