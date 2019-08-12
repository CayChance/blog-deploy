---
title: "React初识"
date: 2019-08-12T18:53:42+08:00
draft: false
toc: false
images:
tags: 
  - react
---
----

作为前端的三大框架之一，react在国外的使用范围还是特别广泛的，最近在看react的入门学习视频。[React16免费视频教程（共28集）](https://jspang.com/posts/2019/05/04/new-react-base.html)


总结几点react中的学习心得：

- react和vue一样，render函数是不允许有多个根元素。如果我非要在页面显示多个根元素呢？可以使用 Fragment 标签
- 类似vue中的v-html，react中叫做 dangerouslySetInnerHTML={{ __html: this.state.title }}（PS：此处是两个大括号）
- react中render函数中的class叫做className，这么做的目的是为了区分关键字class
- label的属性for改为htmlFor
- react中的添加方法使用onClick
- render函数中{}大括号表示这里是js代码
- 不可以直接修改state的值，需要使用setState方法
- 父子组件传值

父组件中引入子组件标签后，在标签中使用自定义的属性来传递属性和方法。例如：key,content,index,deleteItem

子组件中使用`this.props`
``` js
// 父组件
import React, { Component, Fragment } from "react";
import Listitem from "./Listitem";
class Todolist extends Component {
  constructor(props) {
    super(props)
    this.state = {
      inputValue: 'test',
      list: ['test1', 'test2'],
      title: `<h3>Todolist</h3>`
    }
  }

  inputChange(e) {
    this.setState({
      inputValue: e.target.value
    })
  }

  addList = ()=> {
    let newList = [...this.state.list, this.state.inputValue]
    this.setState({
      list: newList,
      inputValue: '',
    })
  }

  deleteItem(index) {
    let list = this.state.list;
    list.splice(index, 1);
    this.setState({
      list
    })
  }

  render() {
    return (
      <Fragment>
        {/* 注释 */}
        <div dangerouslySetInnerHTML={{ __html: this.state.title }}></div>
        <div className="header">
          <label htmlFor="input-item">加入清单</label>
          <input
            id="input-item"
            value={this.state.inputValue}
            onChange={(e)=>{this.inputChange(e)}}
            type="text" />
          <button onClick={this.addList}>添加</button>
        </div>
        <ul className="content">
          {
            this.state.list.map(
              (ele, index) => {
                return <Listitem
                  key={ele + index}
                  content={ele} 
                  index={index}
                  deleteItem={this.deleteItem.bind(this)} />
              })
          }
        </ul>
      </Fragment>
    )
  }
}

export default Todolist

// 子组件
import React, { Component } from 'react';

class Listitem extends Component {
  constructor(props){
    super(props);
    this.state = {

    }
    // this.handleClick = this.handleClick.bind(this)
  }

  handleClick = ()=>{
    this.props.deleteItem(this.props.index)
  }

  render() {
    return (
      <li onClick={this.handleClick}>{this.props.content}</li>
    );
  }
}
 
export default Listitem;
```

- 为什么es6中的class中的自定义方法是有this的，但是react中自定义方法this是undefined？—— [[译] 为什么需要在 React 类组件中为事件处理程序绑定 this](https://juejin.im/post/5afa6e2f6fb9a07aa2137f51)

TLTR:
```js
class Foo {
  constructor(name){
    this.name = name
  }

  display(){
    console.log(this.name);
  }
}

var foo = new Foo('Saurabh');
foo.display(); // Saurabh

//下面的赋值操作模拟了上下文的丢失。 
//与实际在 React Component 中将处理程序作为 callback 参数传递相似。
var display = foo.display; 
display(); // TypeError: this is undefined
```

- 绑定this的方式——可以在constructor或者render方法中使用箭头函数或者bind方法来绑定
