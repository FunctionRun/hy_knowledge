## ES6相关知识点介绍

- 姓名：白占宁
- 邮箱：baizhanning@hiynn.com
- 部门：项目运营中心

[在线测试地址](http://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=true&presets=es2015%2Ces2015-loose%2Ces2017&experimental=false&loose=false&spec=false&code=%0A&playground=true)

### 作用域

- let
- const

### 解构赋值

- 对象
- 数组
- 字符串
- 支持默认值

### 数组操作

- 类数组转数组

  ```
  var likeArr = {
    '0': 'a',
    '1': 'b',
    'length': 2
  }

  //ES5
  var arr1  = [].slice.call(likeArr)

  //ES6
  let arr2 = Array.from(likeArr)
  ```


- 数值转数组

  ```
  Array.of(1, 2, 3)
  ```

### 计算属性

- ES6支持计算属性

  ```
  var name = 'name'
  var obj = {
    name1: 'xxx',
    name2: 'yyy'
  }

  obj[name + '1'] 
  //ES5 不支持
  //ES6 支持
  ```

### 函数相关

- 默认参数，可结合解构赋值使用

- 不定参

- 扩展运算符：函数调用

- 箭头函数

- 函数绑定

  ```
  //ES5
  function fun(obj) {
    
  }

  //ES6
  function fun({name, age = 10}) {
    
  }

  //ES5
  function fun() {
    //通过arguments获取参数
  }

  //ES6
  function fun(...arg) {
    
  }

  //扩展运算符
  ...[1, 2, 3] // 1 2 3

  //扩展运算符代替apply方法
  var arr1 = [2, 3, 5]
  var arr2 = [10, 3, 1]

  //ES5
  Math.max.apply(null, arr1)
  //把第二个数组拼到第一个数组后面
  Array.prototype.push.apply(arr1, arr2)

  //ES6
  Math.max(...arr1)
  arr1.push(...arr2)

  function fun(arr, ...items) {
    arr.push(...items)
  }

  //ES5定义函数
  var fun = function(param) {
    return param
  }

  //ES6定义函数
  var fun = param => param
  ```

  使用箭头函数需要注意以下几点：

  (1) 函数体内this对象，就是定义时所在的对象；

  (2) 不可以当做构造函数，不能使用new；

  (3) 不可以使用arguments对象；

  (4) 不可以使用yield，箭头函数不能用作Generator函数。

  ```
  //ES5函数绑定
  bar.bind(fun)
  bar.apply(fun, argument)

  //ES6
  fun::bar
  fun::bar(...argument)
  ```

  ### Class

  ```
  class Person {
    
  }

  class Test extends Person {
    constructor(props){
      super(props)
    }
  }
  ```

  ### 模块化

  - export

  - export default

  - import

    ```
    import React from 'react' 
    import { createStore } from 'redux' 

    class Test {
      
    }

    export Test
    ```

    ​