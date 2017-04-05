
在这里一般把根reducer拆分成多个子reducer函数分别处理state树的一个分支，然后在输出合并成一个单一的state树。Redux提供combineReducers()辅助函数来满足以上两个作用。
	
{ combineReducers()所做的只是生成一个函数，这个函数来调用你的一系列reducer，每个reducer根据它们的key来筛选出state 的一部分数据并处理，然后这个生成的函数再将所有 reducer 的结果合并成一个的对象。}

**声明阶段：**声明根reducer即todoApp，并拆分为todos,visibleTodoFilter
```
				
		function todos(state = [], action) {
		       // 省略处理逻辑...
			return nextState;
		}

		function visibleTodoFilter(state = 'SHOW_ALL', action) {
			// 省略处理逻辑...
			return nextState;
		}

		let todoApp = combineReducers({
			todos,                   //key为todos
			visibleTodoFilter        //key为visbleTodoFilter
		})
```
####  （1）上面combineReducers把根reducer拆分，各自声明逻辑.
	
**执行阶段：**触发action后combineReducers返回的todoApp调用两个子reducer，他们各自取得对应state部分（state.todos与state.visibeTodoFilter），处理相同action并各自返回新state。
``` 
		let nextTodos = todos(state.todos, action);

		let nextVisibleTodoFilter = visibleTodoFilter(state.visibleTodoFilter,action)
```
####  (2) 上面combineReducers把两个结果集合并成一个新state树.
```		
		return {
			todos: nextTodos,
			visibleTodoFilter: nextVisibleTodoFilter
		};
```
### 4、Redux store保存了根reducer返回的完整state树。
---	
这个新的树就是应用的下一个state所有订阅store.subscribe(listener)的监听器都将被调用；监听器里可以调用store.getState()获得当前state。
	
可以通过使用React Redux库的connect()方法生成容器组件，转入
  
 *（1）mapStateToProps()*
 
 *（2）mapDispatchToProps()*
 
分别建立state与组件props，组件操作与action之间的映射关系。
 
