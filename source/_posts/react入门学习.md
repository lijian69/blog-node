---
title: React 学习
top: true 
categories: 算法   
tags:
  - LeetCode
  - 算法
  - java

---



## React 的学习

> react 有一个组件库 https://ant.design/

引入组件库 UI

```
npm install -D antd
// 按需引入
npm install -D babel-plugin-import
```

修改配置文件 .babelrc

```
// .babelrc or babel-loader option
{
  "plugins": [
    ["import", { "libraryName": "antd", "libraryDirectory": "es", "style": "css" }] // `style: true` 会加载 less 文件
  ]
}
```

### 1. React 项目创建

```shell
npm install -g create-react-app
//创建 项目
create-react-app 项目名
```

### 2. React 元素的渲染

```react
class ShoppingList extends React.Component {
  render() {
    return (
      <div className="shopping-list">
        <h1>Shopping List for {this.props.name}</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}

// 用法示例: <ShoppingList name="Mark" />
```

JSX 语法

```react
let h1 = <h1> hello word </h1>;
// 但是 只能有一个根节点
let root = document.querySelecter('#root');
ReactDOM.render(h1,root);// (渲染的组件，渲染到某个节点上)
```

`实例1:浏览器中实现一个时间`

- 方式1

```
function clock(){
	let time = new Date().toLocaleTimeString();
	let element = <h1> 现在时间是 {time} </h1>;
	let root = document.querySelecter('#root');
	ReactDOM.render(element,root);// (渲染的组件，渲染到某个节点上)
}

clock();

// 间隔函数
setInterval(clock,1000);
```

- 方式2

```react
// react 函数式组件
function Clock(props){
	return {
		<div>
			<h1> 现在时间是 {props.date.toLocaleTimeString()} </h1>
		</div>
	}	
}
function run (){
	ReactDOM.render(
		<Clock date={new Date()} />,
		document.querySelecter('#root')
	);
}

setInterval(run,1000);

run();
```

### 3. React-JSX 语言

优点：

- JSX 执行更快，编译为 JavaScript 代码时进行了优化
- 类型更安全，编译过程如果出错就不能编译
- JSX 编写模板更加简单快速（不要和 VUE 比较）

注意：

1. JSX 必须由根节点
2. 正常的普通 HTML 元素要小写，如果大写，会被默认为组件



### 4. React 组件

> 一个大的组件 是可以 嵌套 组件的。

- `函数式组件 `

`注意：` 函数式组件 一定要大写 ！！！！

```jsx
function Clock(props){
	return {
		<div>
			<h1> 现在时间是 {props.date.toLocaleTimeString()} </h1>
		</div>
	}	
}
```

- `类组件`

```jsx
class HelloWord extends React.component{
	render(){
		return ({
			<div> </div>
			<Click />
		})
	}
}
```

**函数式和类组件的区别？**

> 函数式组件和类组件的区别和使用，函数式比较简单，一般用于静态没有交互事件内容的组件，类组件，一般为交互事件，进行数据的交互。

- `复核组件`  组建中又有组件

### 5. React 状态（State）=> Data

> 相当于 Vue 的 Data,但是 不建议主动赋值 而是采用 setState({ })

```react
export default class Clock extends React.Component {
    constructor(props){
        super(props)
        // 构造函数初始化，将需要改变的数据尽量放到 State 数据中。
        this.state = {
            time : new Date().toLocaleTimeString()
        }
    }

    render(){
        return (
            <div>
                <h1>当前时间：{this.state.time}</h1>
            </div>
        )
    }
    // 声明周期函数
    // 主键渲染完成挂载
    componentDidMount(){
        setInterval(() => {
            // 此时不像 vue 一样 自动渲染
            // error => this.state.time = new Date().toLocaleTimeString;
            // 切勿直接修改 state 数据
            // 和小程序 相似
            this.setState({
                time : new Date().toLocaleTimeString()
            })
        },1000)
    }
}
```

实例1： 点击内容进行切换

```react
export default class Clock extends React.Component {
    constructor(props){
        super(props)
        this.state = {
            c1 : "",
            c2 : "",
        }
        this.clickEvent =this.clickEvent.bind(this); 
    }
    clickEvent(event){
        
        console.log(event.target.dataset.index);
        let index = event.target.dataset.index;
        // 这里 this 是 undefine
        console.log(this)
        if( 1 == index){
            this.setState({
                c1 : "content active",
                c2 : "content",
            })
        }else{
            this.setState({
                c1 : "content",
                c2 : "content active",
            })
        }
    }

    render(){
        return (
            <div>
                <button data-index="1" onClick={this.clickEvent}>点击一</button>
                <button data-index="2" onClick={this.clickEvent}>点击二</button>
                <div className={this.state.c1} > 内容一 </div>
                <div className={this.state.c2} > 内容二 </div>
            </div>
        )
    }
    
}
```



### 6. React 父子组件传递数据 （Props）

父传统给子组件数据，单向流动，不能子传递给父

`Props`的传值 可以为 `任意类型` 包括 函数。可以传递给父元素的函数，就可以修改父组件的数据。

实例1： 父组件 -> 子组件的数据传递

```react
export default class ParentCom extends React.Component {
  
    constructor(props){
        super(props)
        this.state = {
            isActive : true
        }
        this.changeActive = this.changeActive.bind(this);
    }
 
    render(){
        return (
            <div>
                <button onClick={this.changeActive}>控制子元素的显示</button>
                <ChildCom isActive = {this.state.isActive} />
            </div>
        )
    }

    changeActive(){
        this.setState({
            isActive : !this.state.isActive
        })
    }
    
}

class ChildCom extends React.Component{
    
    constructor(props){
        super(props)
    }

    render() {
        let strClass = '';
        if(this.props.isActive){
            strClass = 'active'
        }
        return (
            <div className={"content "+strClass}>
                <h1>我是子组件</h1>
            </div>
        )
    }
}
```

实例2： 子组件 -> 父组件 的数据传递

> 这里不像 Vue 这么强大了，调用父元素的 函数 来改变父的状态

```react
export default class ParentCom extends React.Component {
  
    constructor(props){
        super(props)
        this.state = {
            childData : ''
        }
       
    }
 
    render(){
        return (
            <div>
                <h1>子元素传递给父元素的数据 ： {this.state.childData} </h1>
                <ChildCom setChildData={this.setChildData} />
            </div>
        )
    }

    setChildData = (data) => {
        this.setState({
            childData : data,
        })
    }

    
}

class ChildCom extends React.Component{
    
    constructor(props){
        super(props)
        this.state = {
            msg : '我是子消息'
        }
    }

    // => 函数就不需要 .bind(this)
    sendData = () => {
        console.log(this.state.msg);
        this.props.setChildData(this.state.msg);
    }

    render() {
    
        return (
            <div>
                <button onClick={()=> {this.props.setChildData(this.state.msg);}} > 传递给父元素</button>
            </div>
        )
    }
}
```

### 7. React 事件 

1. 它的事件是以`小驼峰命名法`

2. onClick={ } 

   > React 入参是 对象的时候，它只是代理原生的事件对象，如果想要查看某一属性的值，必须属于完整属性

3. 阻止默认的 Form 的提交 , 阻止默认 `e.preventDefault();`

   ```react
   export default class ParentCom extends React.Component {
     
       constructor(props){
           super(props) 
       }
    
       render(){
           return (
               <form action="http://www.baidu.com">
                   <div>
                       <button onClick={this.parentEvent}>点击</button>
                   </div>
               </form>
           )
       }
       parentEvent = (e) => {
           console.log(e);
           // 组织跳转到 百度
           e.preventDefault();
       }
       
   }
   ```


### 8. React 条件渲染

`实例1：`条件渲染是否 登录

```react
import React from "react";
function UserGreet(props) {
    return (<h1>您已经登录</h1>)
}

function UserLogin(props) {
    return (<h1>请先登录</h1>)
}

export default class Apps extends React.Component{
    constructor(props) {
        super(props);
        this.state = {
            isLogin : false
        }
    }
    render() {

        let element = null;

        if (this.state.isLogin){
            element =  (<UserGreet />)
        }else{
            element =  (<UserLogin />)
        }

        return (
            <div>
                <h1> 这是头部 </h1>
                    <div>
                        <h1>第一种</h1>
                        {element}
                    </div>

                    <div>
                        <h1>第二种</h1>
                        {this.state.isLogin ? <UserGreet/> : <UserLogin/>}
                    </div>
                <h1> 这是尾部 </h1>
            </div>
        )
    }

}
```

`实例2` 列表的渲染









 



