---
layout: post
title: react 小结
category: 前端
keywords: 前端,react
---
## React 小结

#####写给未来忘记怎么写react 的自己。

React 是一个web的开发框架。

### Hello world

	html
	<div id="root"></div>

	js
	ReactDOM.render(
	<h1>Hello, world!</h1>,
	document.getElementById('root'));


### JSX

	const element = <h1>Hello, world!</h1>;
这句非html，非string，而是jsx。就是js的extension。

> 可以这么嵌套使用

	function formatName(user) {
		return user.firstName + ' ' + user.lastName;
	}

	const user = {
		firstName: 'Harper',
		lastName: 'Perez'
		};

	const element = (
	<h1>
    	Hello, {formatName(user)}!
    </h1>
	);

	ReactDOM.render(
		element,
		document.getElementById('root')
	);

> jsx 是一种表达式所以可以和`if`一起使用

### 更新
显示时间

	function tick() {
		const element = (
		<div>
      		<h1>Hello, world!</h1>
      		<h2>It is {new Date().toLocaleTimeString()}.</h2>
    	</div>
    	);
    	ReactDOM.render(
    element,
    document.getElementById('root')
    );}

	setInterval(tick, 1000);

### Components and Props
组件和props

组件和Android的view很像，就是将UI作为一个控件来搞。
在写法上和js的function很像，加入props参数
像这样：

	function Welcome(props) {
		return <h1>Hello, {props.name}</h1>;
	}

也可以这样写（es6）：

	class Welcome extends React.Component {
		render() {
    		return <h1>Hello, {this.props.name}</h1>;
    	}
    }
    
然后这样用：
	
	const element = <Welcome name="Sara" />;
	
流程为：

- We call ReactDOM.render() with the <Welcome name="Sara" /> element.
- React calls the Welcome component with {name: 'Sara'} as the props.
- Our Welcome component returns a \<h1>Hello, Sara\</h1> element as the result.
- React DOM efficiently updates the DOM to match \<h1>Hello, Sara\</h1>.

>  **注意事项**
> 
>  **<h3>组件开头大写</h3>**
> 
>  例如：\<div /> 表示一个DOM标签，但是<Welcome />表示一个组件

### Composing Components
组合组件，和Android的组合控件一样。
🌰：

	function Welcome(props) {
		return <h1>Hello, {props.name}</h1>;
	}

	function App() {
	return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
    );
    }

	ReactDOM.render(
	<App />,
		document.getElementById('root')
	);


>  **注意事项**
> 
>  **<h3>组件必须只返回一个单一root元素</h3>**
> 
>  这是为我们使用\<div>来包含所有的<Welcome />元素

#### 分解组件
	function Comment(props) {
	return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
    );
    }

如上是一个评论组件，接收author，text，date作为props。
可以分解为：
	
	function Avatar(props) {
	return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
    );}
    
    function UserInfo(props) {
    return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
    );}
    
    function Comment(props) {
    return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
    );}

#### Props只读
在java中就是静态变量吧。

**All React components must act like pure functions with respect to their props.**

ps：pure function ☞ 不修改他们的传入参数。


### State 和 生命周期

state和props很像，但是它是private的并且被组件完全控制。
🌰：

	class Clock extends React.Component {
	constructor(props) {
    super(props);
    this.state = {date: new Date()};
    }
    
    render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
    }}
    ReactDOM.render(
    <Clock />,
    document.getElementById('root')
    );

#### 添加生命周期方法

app中有很多组件，因为内存有限，必须释放掉不需要的组件。
设置一个`timer`当Clock在DOM中渲染的时候，这在React中叫`mount`。
当DOM移除Clock时，清除掉这个`timer`，叫`unmounting`。

	componentDidMount() {}
	 
	componentWillUnmount() {}
	 
这些方法叫 夺命钩（lifecycle hooks）。

`componentDidMount()`钩在组件已经在DOM中渲染后使用。可以用来设置`timer`

	componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );}
    
`componentWillUnmount()`夺命钩用来清理掉`timer`.

	 componentWillUnmount() {
    	clearInterval(this.timerID);
    }
    
 [点我进CodePen](http://codepen.io/gaearon/pen/amqdNA?editors=0010)
 
 
#### 正确使用 State

- Do Not Modify State Directly
- State Updates May Be Asynchronous
- State Updates are Merged

#### The Data Flows Down
只有state的拥有者才知道state，我的state，我做主

### 处理 事件
处理事件和DOM处理很像。
不同点：
>- React 使用camelCase命名，而不是小写字母命名
>- 传递函数，而不是传递string

🌰：
	
	
	//html
	<button onclick="activateLasers()">
		Activate Lasers
	</button>
	//React
	<button onClick={activateLasers}>
		Activate Lasers
	</button>

另一个不同是可以返回false避免默认行为

	//html
	<a href="#" onclick="console.log('The link was clicked.'); return false">
	Click me
	</a>
	
	//React
	function ActionLink() {
		function handleClick(e) {
    	e.preventDefault();
    	console.log('The link was clicked.');
    }
    return (
    	<a href="#" onClick={handleClick}>
      	Click me
    	</a>
    );}
	
### Conditional Rendering

	function Greeting(props) {
		const isLoggedIn = props.isLoggedIn;
		if (isLoggedIn) {
    		return <UserGreeting />;
    	}
    	return <GuestGreeting />;
    }

[点我进CodePen](https://codepen.io/gaearon/pen/QKzAgB?editors=0010)

#### Inline If with Logical && Operator

	function Mailbox(props) {
	const unreadMessages = props.unreadMessages;
	return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
    );}
    
    const messages = ['React', 'Re: React', 'Re:Re: React'];
    ReactDOM.render(
    	<Mailbox unreadMessages={messages} />,
    	document.getElementById('root'));

#### Inline If-Else with Conditional Operator

	render() {
	const isLoggedIn = this.state.isLoggedIn;
	return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
    );
    }

#### Preventing Component from Rendering

如果想不显示组件，就`return null`.

	function WarningBanner(props) {
	if (!props.warn) {
    return null;
    }
    
    return (
    <div className="warning">
      Warning!
    </div>
    );
    }

### Lists and Keys

JS 的 list：

	const numbers = [1, 2, 3, 4, 5];
	const doubled = numbers.map((number) => number * 2);
	console.log(doubled);

#### 渲染多个Components

	const numbers = [1, 2, 3, 4, 5];
	const listItems = numbers.map((number) =>
	<li>{number}</li>
	);
	ReactDOM.render(
	<ul>{listItems}</ul>,
	document.getElementById('root')
	);
	
[点我进CodePen](https://codepen.io/gaearon/pen/jrXYRR?editors=0011)

#### keys
keys 帮助react辨别哪个items改变了，添加了或者删除。
	
	const todoItems = todos.map((todo) =>
	<li key={todo.id}>
    {todo.text}
    </li>
    );
最好是选一个能唯一表示这个元素的id。

#### Extracting Components with Keys

	function ListItem(props) {
	// Correct! There is no need to specify the key here:
	return <li>{props.value}</li>;
	}
	
	function NumberList(props) {
	const numbers = props.numbers;
	const listItems = numbers.map((number) =>
    // Correct! Key should be specified inside the array.
    <ListItem key={number.toString()}
              value={number} />);
    return (
    <ul>
      {listItems}
    </ul>);}
    
    const numbers = [1, 2, 3, 4, 5];
    ReactDOM.render(
    <NumberList numbers={numbers} />,
    document.getElementById('root'));
    
#### Keys Must Only Be Unique Among Siblings

#### Embedding map() in JSX
	
	function NumberList(props) {
	const numbers = props.numbers;
	return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
    );
    }
    
### Forms

#### Controlled Components

	class NameForm extends React.Component {
		constructor(props) {
    		super(props);
    		this.state = {value: ''};

    		this.handleChange = this.handleChange.bind(this);
    		this.handleSubmit = this.handleSubmit.bind(this);
    	}
	
	handleChange(event) {
    	this.setState({value: event.target.value});
	}
	
	handleSubmit(event) {
    	alert('A name was submitted: ' + this.state.value);
    	event.preventDefault();
    }
    
    render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
    }}

`handleChange`根据按键的改变而更新state

#### The textarea Tag

	<textarea value={this.state.value} onChange={this.handleChange} />
	
#### The select Tag

	<select value={this.state.value} onChange={this.handleChange}>
	
[点我进codepen](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

#### Handling Multiple Inputs
如果有多个输入就给每个元素名字，函数就会根据`event.target.name`搞。

[点我进codepen](https://codepen.io/gaearon/pen/wgedvV?editors=0010)

#### Alternatives to Controlled Components
有时候这么写很烦，试试这个[uncontrolled components](https://facebook.github.io/react/docs/uncontrolled-components.html)

### Lifting State Up
[Try it on CodePen.](http://codepen.io/valscion/pen/VpZJRZ?editors=0010)
#### Adding a Second Input
待续
### Composition vs Inheritance

推荐Composition
