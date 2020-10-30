---
title: React  生命周期
tags: 'model'
categories: 'frame'
top_img: '/img/design.jpeg'
---
> React生命周期主要包括三个阶段：初始化阶段、运行中阶段和销毁阶段

![](/img/react.webp)
        
组件的生命周期可分成三个状态：<ul><li>Mounting：已插入真实 DOM</li><li>Updating：正在被重新渲染</li><li>Unmounting：已移出真实 DOM</li></ul>
生命周期的方法有：<ul><li>
<b>componentWillMount</b> 在渲染前调用,在客户端也在服务端。</li><li>
<b>componentDidMount</b> : 在第一次渲染后调用，只在客户端。之后组件已经生成了对应的DOM结构，可以通过this.getDOMNode()来进行访问。 如果你想和其他JavaScript框架一起使用，可以在这个方法中调用setTimeout, setInterval或者发送AJAX请求等操作(防止异步操作阻塞UI)。</li><li>
<b>componentWillReceiveProps</b> 在组件接收到一个新的 prop (更新后)时被调用。这个方法在初始化render时不会被调用。</li><li>
<b>shouldComponentUpdate</b> 返回一个布尔值。在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。 
可以在你确认不需要更新组件时使用。</li><li>
<b>componentWillUpdate</b>在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。</li><li>
<b>componentDidUpdate</b> 在组件完成更新后立即调用。在初始化时不会被调用。</li><li>
<b>componentWillUnmount</b>在组件从 DOM 中移除之前立刻被调用。</li><li>## 可以用如下代码测试</li></ul>
``` javascript

import React from 'react'
import ReactDOM from 'react-dom';

class SubCounter extends React.Component {
    componentWillReceiveProps() {
        console.log('9、子组件将要接收到新属性');
    }

shouldComponentUpdate(newProps, newState) {
        console.log('10、子组件是否需要更新');
        if (newProps.number < 5) return true;
        return false
    }

componentWillUpdate() {
        console.log('11、子组件将要更新');
    }

componentDidUpdate() {
        console.log('13、子组件更新完成');
    }

componentWillUnmount() {
        console.log('14、子组件将卸载');
    }

render() {
        console.log('12、子组件挂载中');
        return (<p>{this.props.number}</p>)
}
}

class Counter extends React.Component {
    static defaultProps = {
        //1、加载默认属性
        name: 'sls',
        age:23
    };

constructor() {
        super();
        //2、加载默认状态
        this.state = {number: 0}
}

componentWillMount() {
        console.log('3、父组件挂载之前');
    }

componentDidMount() {
        console.log('5、父组件挂载完成');
    }

shouldComponentUpdate(newProps, newState) {
        console.log('6、父组件是否需要更新');
        if (newState.number<15) return true;
        return false
    }

componentWillUpdate() {
        console.log('7、父组件将要更新');
    }

componentDidUpdate() {
        console.log('8、父组件更新完成');
    }

handleClick = () => {
        this.setState({
            number: this.state.number + 1
        })
};

render() {
        console.log('4、render(父组件挂载)');
        return (
            <div>
                <p>{this.state.number}</p>
            <button onClick={this.handleClick}>+</button>
            {this.state.number<10?<SubCounter number={this.state.number}/>:null}
        </div>
    )
}
}
ReactDOM.render(<Counter/>, document.getElementById('root'));
```
## 根据上图：React的生命周期可以用如下一个类来表示
``` javascript

import Parent from 'Parent';

class React extends Parent {
    config: any;
    state: any;

    constructor(config: any) {
        super();
        this.config = config;
        this.state = {};
    this.config.componentWillMount();  // 初始化之前
        this.render();
    }

private render() {
        // 执行初始化相关操作
        this.config.componentDidMount();
##         // 初始化结束    }

// 当父组件重新render() props改变
    private parentRender() { 
            this.config.componentWillReceiveProps(); 
                    this.shouldUpdate();
    }
        // 当state更新时调用此方法
        private dataUpdate() { 
                this.shouldUpdate();
            }
        private shouldUpdate() { 
                // 由外部来判断是否需要更新
        <span style="font-size: large; color: rgb(194, 79, 74);">//一般通过该函数来优化性能：
        if (this.config.shouldComponentUpdate()) {
                this.config.componentWillUpdate();
                this.render();
                this.config.componentDidUpdate();
            }
    }
    // 销毁
        destroy() {
            this.config.componentWillUnmount();
            // 销毁一些监听函数 组件之类的
        }
}

export default React;
```