### react组件生命周期

#### 前言

组件会随着组件的props和state改变而发生变化，它的DOM也会有相应的变化。

```
一个组件就是一个状态机：对于特定的输入，它总会返回一致的输出。
```

React组件提供了生命周期的钩子函数去响应组件不同时刻的状态，组件的生命周期如下：

	1、实例化
	2、存在期
	3、销毁期

钩子函数是我们重点关注的地方，下面来详细了解下生命周期下的钩子函数调用顺序和作用。每个生命周期阶段调用的钩子函数会略有不同。下面的图片或许对你有帮助。

1、实例化

首次调用组件时，有以下方法会被调用（注意顺序，从上到下先后执行）：

##### getDefaultProps

这个方法是用来设置组件默认的props，组件生命周期只会调用一次。但是只适合React.createClass直接创建的组件，使用ES6/ES7创建的这个方法不可使用，ES6/ES7可以使用下面方式：

```
//es7
class Component {
  static defaultProps = {}
}
//或者也可以在外面定义es6
//Compnent.defaultProps
```

##### getInitialState

设置state初始值，在这个方法中你已经可以访问到this.props。getDefaultProps只适合React.createClass使用。使用ES6初始化state方法如下：

```
class Component extends React.Component{
  constructor(){
    this.state = {
      render: true,
    }
  }
}
```

##### componentWillMount

该方法会在组件首次渲染之前调用，这个是在render方法调用前可修改state的最后一次机会。这个方法很少用到。

##### render

这个方法以后大家都应该会很熟悉，JSX通过这里，解析成对应的虚拟DOM，渲染成最终效果。格式大致如下：

```
class Component extends React.Component{
  render(){
    return (
        <div></div>
    )
  }
}
```

##### componentDidMount

这个方法在首次真实的DOM渲染后调用（仅此一次）当我们需要访问真实的DOM时，这个方法就经常用到。访问真实的DOM与refs有关。当我们需要请求外部接口数据，一般都在这里处理。

2、存在期

实例化后，当props或者state发生变化时，下面方法依次被调用：

##### componentWillReceiveProps

每当我们通过父组件更新子组件props时（这个也是唯一途径），这个方法就会被调用。

```
componentWillReceiveProps(nextProps){}
```

##### shouldComponentUpdate

字面意思，是否应该更新组件，默认返回true。当返回false时，后期函数就不会调用，组件不会在次渲染。

```
shouldComponentUpdate(nextProps,nextState){}
```

##### componentWillUpdate

字面意思组件将会更新，props和state改变后必调用。

##### render

跟实例化时的render一样，不多说.

##### componentDidUpdate

这个方法在更新真实的DOM成功后调用，当我们需要访问真实的DOM时，这个方法就也经常用到。

3、销毁期

销毁阶段，只有一个函数被调用：

##### componentWillUnmount

每当组件使用完成，这个组件就必须从DOM中销毁，此时该方法就会被调用。当我们在组件中使用了setInterval，那我们就需要在这个方法中调用clearTimeout。
