# 我是b.md

# React基础知识详解

除组件化、虚拟DOM在复用以及性能上带来的一般好处外，React思想使得开发者之间更好的分工与合作，在配合上非常顺畅，规范的接口以及极强的约束，使得整个代码结构清晰，不同开发者的代码高度一致。
下面主要讲解ES6语法来写React组件代码，提前明确以下几点：

不支持 getInitialState 在设置组件初始的 state ，可在组件的 constructor 中通过 this.state 设置，也可直接作为 properties 定义
propTypes、defaultProps 作为 properties 定义，也可在组件外部通过键值对方式设置。
不支持 mixins，可以使用高阶组件写法，或者 decorator。
1. 顶层API

最简单的React组件及其渲染

import React, { Component } from 'react';

/**
 * 最简单的一个组件
 */
class SimpleComponent extends Component {
  render(){
    return <div> here we go </div>;
  }
}

// 我们可以看看React给我们提供了哪些顶层组件
console.log( React );

export default SimpleComponent;
react.js

React.Children: Object
React.Component: ReactComponent(props, context, updater)
React.DOM: Object
React.PropTypes: Object
React.cloneElement: (element, props, children)
React.createClass: (spec)
React.createElement: (type, props, children)
React.createFactory: (type)
React.createMixin: (mixin)
React.isValidElement: (object)
Component API

this.context: Object
this.props: Object
this.refs: Object
this.state: Object
this.setState: Object
react-dom.js

ReactDOM.findDOMNode: findDOMNode(componentOrElement)
ReactDOM.render: ()
ReactDOM.unmountComponentAtNode: (container)
react-dom-server.js

ReactDOMServer.renderToString
ReactDOMServer.renderToStaticMarkup
2. jsx语法

类似 xml 的语法，用来描述组件树

<div classname="x">
  <a href="#">#</a>
    <component x="y">1</component>
</div>
不用JSX，用React提供的API写的话

React.createElement('div',{
  className:'x'
    },[
    React.createElement('a',{href:'#'},'#'),
    React.createElement(Component,{x:'y'},1);
]);
2.1 注释、命名、根元素个数、JSX 嵌入变量

import React, { Component } from 'react';

// 1. 组件命名遵循驼峰命名，首字母大写
class ComponentDemo extends Component {
  render(){
    {
      /*
      2. 这是代码注释
      也可以是多行
      */
    }
    const name = this.props.name;

    // 3. render 方法 return 回来的根元素只能是一个，超过会报错
    // 4. { } 里面可以写JS代码
    return (
      <div>
        hello, {name ? name : "我是默认的"}
      </div>
    );
  }
}

export default ComponentDemo;
React只有一个限制， 组件只能渲染单个根节点。如果你想要返回多个节点，它们必须被包含在同一个节点里。
2.2 styles

import React, { Component } from 'react';

class StyleDemo extends Component {
  render(){
    // 5. 在JS文件里面给组件定义样式
    var MyComponentStyles = {
        color: 'blue',
        fontSize: '28px'
    };

    return (
      <div style={MyComponentStyles}>
          可以直接这样写行内样式
      </div>
    )
  }
}

export default StyleDemo;
2.3 JSX SPREAD

可以用通过 {...obj} 来批量设置一个对象的键值对到组件的属性，注意顺序

import React, { Component } from 'react';

class SpreadDemo extends Component {
  componentWillMount(){
    console.log(this.props);
  }
  render(){
    return <h1> {this.props.name} is a {this.props.type} </h1>;
  }
}

export default SpreadDemo;
2.4 属性名不能和 js 关键字冲突

例如：className, readOnly, htmlfor

3. 数据流：state props propType

3.1 state && setState

用状态控制组件变化 可以把一个组件看做一个状态机, 每一次状态对应于组件的一个 ui

组件内部的状态，可以使用 state

import React, { Component } from 'react';

class StateDemo extends Component {

  state = {
    secondsElapsed: 0
  }

  tick(){
    this.setState({ secondsElapsed: this.state.secondsElapsed + 1 });
  }

  componentDidMount(){
    this.interval = setInterval( this.tick.bind(this), 1000 );
  }

  componentWillUnmount(){
    clearInterval(this.interval);
  }

  render(){
    return (
      <div>目前已经计时：{this.state.secondsElapsed}秒</div>
    )
  }
}

export default StateDemo;
3.2 props

通过 this.props 可以获取传递给该组件的属性值，还可以通过定义 getDefaultProps 来指定默认属性值（这是ES5的写法，ES6定义组件的默认props可以直接写props）

下面几个是props的常用API：

this.props.children
this.props.map
this.props.filter
props是调用组件的时候传递进去的数据，一般用于组件树数据传递

import React, { Component } from 'react';

class PropsDemo extends Component {
  props = {
    title: '这是默认的title属性值'
  }
  render(){
    console.log(this.props);

    return <b>{this.props.title}</b>
  }
}

export default PropsDemo;


// 组件调用方式
// <PropsDemo title="设置的标题" />
3.3 propTypes

通过指定 propTypes 可以校验props属性值的类型，校验可提升开发者体验，用于约定统一的接口规范。

import React, { Component, PropTypes } from 'react';

class PropTypesDemo extends Component {

  static propTypes = {
    title: PropTypes.string.isRequired
  }

  props = {
      title: '默认的title'
  }

  render(){
    return <b>{this.props.title}</b>
  }
}

export default PropTypesDemo;
4. 调用API定义组件

用 React.createClass或者React.Component 定义组件时允许传入相应的配置及组件API的使用，包括组件生命周期提供的一系列钩子函数。

4.1 组件初始定义

getDefaultProps 得到默认属性对象，这个在ES6的时候不需要这样定义
propTypes 属性检验规则
mixins 组件间公用方法
4.2 初次创建组件时调用

getInitialState 得到初始状态对象
render 返回组件树. 必须设置
componentDidMount 渲染到 dom 树中是调用，只在客户端调用，可用于获取原生节点
4.3 组件的属性值改变时调用

componentWillReceiveProps 属性改变调用
shouldComponentUpdate 判断是否需要重新渲染
render 返回组件树. 必须设置
componentDidUpdate 渲染到 dom 树中是调用, 可用于获取原生节点
4.4 销毁组件

componentWillUnmount 组件从 dom 销毁前调用
4.5 示例诠释组件全生命周期

import React, { Component } from 'react';

class LifeCycle extends Component {

  Props = {
    value: '开始渲染'
  }

  componentWillReceiveProps(nextProps){
    console.log('componentWillReceiveProps');
    this.setState({
        value: nextProps.value
    });
  }

  shouldComponentUpdate(nextProps,nextState){
    console.log('shouldComponentUpdate');
    return true;
  }

  componentWillUpdate(nextProps,nextState){
    console.log('componentWillUpdate');
  }

  componentWillMount(){
    console.log('componentWillMount');
  }

  render() {
    console.log('render');
    return <span>{this.props.value}</span>
  }

  componentDidMount() {
      console.log('componentDidMount');
  }

  componentDidUpdate(prevProps,prevState) {
      console.log('componentDidUpdate');
  }

  componentWillUnmount(prevProps,prevState) {
      console.log('componentWillUnmount');
  }
}

export default LifeCycle;
调用组件并销毁组件示例

import React, { Component } from 'react';
import LifeCycleDemo from './LifeCycleDemo';

class DestroyComponent extends Component {

  state = {
    value:1,
    destroyed:false
  }

  increase = () => {
    this.setState({
      value: this.state.value + 1
    });
  }

  destroy = () => {
    this.setState({
      destroyed: true
    });
  }

  render() {
    if(this.state.destroyed){
        return null;
    }

    return <div>
      <p>
        <button onClick={this.increase}>每次加1</button>
        <button onClick={this.destroy}>干掉这两个按钮</button>
      </p>
      <LifeCycleDemo value={this.state.value}/>
    </div>;
  }
}

export default DestroyComponent;

4.6 回顾组件的渲染过程

# 创建-》渲染-》销毁

getDefaultProps()
getInitialState()
componentWillMount()
render()
componentDidMount()
componentWillUnmount()

# 更新组件

componentWillReceiveProps()
shouldComponentUpdate()
componentWillUpdate()
render()
componentDidUpdate()
5. 使用ref对操作DOM

ReactDOM.findDOMNode
this.refs.xxx
获取DOM后可以方便结合现有非 react 类库的使用，通过 ref/refs 可以取得组件实例，进而取得原生节点，不过尽量通过 state/props 更新组件，不要使用该功能去更新组件的DOM。

import React, { Component } from 'react';
import ReactDOM, { findDOMNode } from 'react-dom';

class HandleDOMComponent extends Component {
  componentDidMount(){
    // 两种方式都可以获取到元素
    let ele = findDOMNode(this.refs.content);
    let ele2 = this.refs.content;

    // 如果想用 jquery，那么这是个好时机
    console.log( ele );
    console.log( ele.inner
