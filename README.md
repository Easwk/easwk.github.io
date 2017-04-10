

#### connect()方法生成容器组件

	React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。

1、组件

   UI组件特征：
   1.只负责 UI 的呈现，不带有任何业务逻辑
   2.没有状态（即不使用this.state这个变量）
   3.所有数据都由参数（this.props）提供
   4.不使用任何 Redux 的 API

   容器组件特征：
   1.负责管理数据和业务逻辑，不负责 UI 的呈现
   2.带有内部状态
   3.使用 Redux 的 API

   React-Redux 规定，所有的 UI 组件都由用户提供，容器组件则是由 React-Redux 自动生成。也就是说，用户负责视觉层，状态管理则是全部交给它。

2、connect()方法   
   React-Redux 提供connect方法，用于从 UI 组件生成容器组件。
 ```
   import { connect } from 'react-redux'
   const VisibleTodoList = connect(
     mapStateToProps,
     mapDispatchToProps
   )(TodoList);
 ```
connect方法接受两个参数：mapStateToProps和mapDispatchToProps。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action。

3、mapStateToProps()方法
   mapStateToProps是一个函数，执行后应该返回一个对象，里面的每一个键值对就是一个映射。建立一个从（外部的）state对象到（UI 组件的）props对象的映射关系。
   ```
   const mapStateToProps = (state) => {
	  return {
	    todos: getVisibleTodos(state.todos, state.visibilityFilter)
	  }
	}
	```
	
	上面代码中，mapStateToProps是一个函数，它接受state作为参数，返回一个对象。这个对象有一个todos属性，代表 UI 组件的同名参数，后面的getVisibleTodos也是一个函数，可以从state算出 todos 的值。
    
    ```
	const getVisibleTodos = (todos, filter) => {
	  switch (filter) {
	    case 'SHOW_ALL':
	      return todos
	    case 'SHOW_COMPLETED':
	      return todos.filter(t => t.completed)
	    case 'SHOW_ACTIVE':
	      return todos.filter(t => !t.completed)
	    default:
	      throw new Error('Unknown filter: ' + filter)
	  }
	}
    ```
    mapStateToProps会订阅 Store，每当state更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染。

    mapStateToProps的第一个参数总是state对象，还可以使用第二个参数，代表容器组件的props对象。
   
   ```
    // 容器组件的代码
	//    <FilterLink filter="SHOW_ALL">
	//      All
	//    </FilterLink>

	const mapStateToProps = (state, ownProps) => {
	  return {
	    active: ownProps.filter === state.visibilityFilter
	  }
	}
	```
	
	使用ownProps作为参数后，如果容器组件的参数发生变化，也会引发 UI 组件重新渲染。

	connect方法可以省略mapStateToProps参数，那样的话，UI 组件就不会订阅Store，就是说 Store 的更新不会引起 UI 组件的更新。

4、mapDispatchToProps()方法
   mapDispatchToProps是connect函数的第二个参数，用来建立 UI 组件的参数到store.dispatch方法的映射。也就是说，它定义了哪些用户的操作应该当作 Action，传给 Store。它可以是一个函数，也可以是一个对象。

   如果mapDispatchToProps是一个函数，会得到dispatch和ownProps（容器组件的props对象）两个参数。

   ```
   const mapDispatchToProps = (
	  dispatch,
	  ownProps
	) => {
	  return {
	    onClick: () => {
	      dispatch({
	        type: 'SET_VISIBILITY_FILTER',
	        filter: ownProps.filter
	      });
	    }
	  };
	}
    ```
    从上面代码可以看到，mapDispatchToProps作为函数，应该返回一个对象，该对象的每个键值对都是一个映射，定义了 UI 组件的参数怎样发出 Action。

    如果mapDispatchToProps是一个对象，它的每个键名也是对应 UI 组件的同名参数，键值应该是一个函数，会被当作 Action creator ，返回的 Action 会由 Redux 自动发出。举例来说，上面的mapDispatchToProps写成对象就是下面这样。

    ```
    const mapDispatchToProps = {
	  onClick: (filter) => {
	    type: 'SET_VISIBILITY_FILTER',
	    filter: filter
	  };
	}
    ```
    
5、<Provider> 组

connect方法生成容器组件以后，需要让容器组件拿到state对象，才能生成 UI 组件的参数。

一种解决方法是将state对象作为参数，传入容器组件。但是，这样做比较麻烦，尤其是容器组件可能在很深的层级，一级级将state传下去就很麻烦。
	
React-Redux 提供Provider组件，可以让容器组件拿到state。

```
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp);

render(
	  <Provider store={store}>
	    <App />
	  </Provider>,
	  document.getElementById('root')
	)
```
	
上面代码中，Provider在根组件外面包了一层，这样一来，App的所有子组件就默认都可以拿到state了。

它的原理是React组件的context属性，请看源码。
	
```
class Provider extends Component {
	 getChildContext() {
	    return {
	      store: this.props.store
	    };
	  }
	  render() {
	    return this.props.children;
	  }
	}

	Provider.childContextTypes = {
	  store: React.PropTypes.object
	}	
```
	
上面代码中，store放在了上下文对象context上面。然后，子组件就可以从context拿到store，代码大致如下。

	
```
	class VisibleTodoList extends Component {
	  componentDidMount() {
	    const { store } = this.context;
	    this.unsubscribe = store.subscribe(() =>
	      this.forceUpdate()
	    );
	  }

	  render() {
	    const props = this.props;
	    const { store } = this.context;
	    const state = store.getState();
	    // ...
	  }
	}

	VisibleTodoList.contextTypes = {
	  store: React.PropTypes.object
	}
```

React-Redux自动生成的容器组件的代码，就类似上面这样，从而拿到store。
