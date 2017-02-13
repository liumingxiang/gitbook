# vue学习笔记

## 关于vue

（2015年 前端也开始模块化工程化了，前端框架层出不穷）
vue的作者是ex-Googler，阿里的Weex(vue作者说不介意叫Vue-Native呵呵)也是借鉴了vue

## 需要知道的基础知识

先看一个 todolist栗子

```javascript
    html
    //要操作的对象
    <div id="app">
    //绑定模型 按键事件
      <input v-model="newTodo" v-on:keyup.enter="addTodo">
      <ul>
        //循环
        <li v-for="todo in todos">
           //文本插值 
          <span>{{ todo.text }}</span>
          //  指令的值 为绑定表达式（参数）
          <button v-on:click="removeTodo($index)">X</button>
        </li>
      </ul>
    </div>
    js
    //创建vue
    new Vue({
        //创建对象        
        el: '#app',
        //属性赋值     
        data: {
            newTodo: '',
            todos: [
                { text: 'Add some todos' }
            ]
        },
        //调用方法
        methods: {
            //方法一 添加todo
            addTodo: function () {
                //去掉空格
                var text = this.newTodo.trim()
                if (text) {
                    //加入数组
                    this.todos.push({ text: text })
                    //置空
                    this.newTodo = ''
                }
             },
             //方法二 删除todo
             removeTodo: function (index) {
                //删除
                 this.todos.splice(index, 1)
             }
         }
    })
```

- 双向绑定
  数据（dom文本或结构）和视图同步变化

  ```javascript
    var data = { a: 1 }
    var vm = new Vue({
      data: data
    })
    vm.a === data.a // -> true    
    // 设置属性也会影响到原始数据
    vm.a = 2
    data.a // -> 2    
    // ... 反之亦然
    data.a = 3
    vm.a // -> 3
  ```

  - 数据绑定最基础的形式是文本插值

    ```javascript
            <span>Message: {{ msg }}</span>  //2个Mustache 标签
            <span>This will never change: {{* msg }}</span>  //*单次插值
    ```

  - HTML属性(Attributes) 插入（ Vue.js 指令和特殊特性内不能用插值）

    ```javascript
            <div id="item-{{ id }}"></div>
    ```

- 放在 Mustache 标签内的文本称为绑定表达式
  （一段绑定表达式 由一个简单的 JavaScript 表达式和可选的一个或多个过滤器构成）

  ```javascript
    {{ number + 1 }}
    {{ ok ? 'YES' : 'NO' }}
    {{ message.split('').reverse().join('') }}
    //一个限制是每个绑定只能包含单个表达式(坏栗如下，不能这么吃！！！)
    <!-- 这是一个语句，不是一个表达式： -->
    {{ var a = 1 }}
    <!-- 流程控制也不可以，可改用三元表达式 -->
    {{ if (ok) { return message } }}
  ```

- 过滤器 (Filter)

  ```javascript
    //将表达式 message 的值“管输（pipe）”到内置的 filterA过滤器。
    //过滤器函数始终以表达式的值作为第一个参数。
    //带引号的参数视为字符串，而不带引号的参数按表达式计算。
    //字符串 'arg1' 将传给过滤器作为第二个参数，表达式 arg2 的值在计算出来之后作为第三个参数。
    {{ message | filterA 'arg1' arg2 | filterB }}
  ```

- 指令 (Directives)
  是特殊的带有前缀 v- 的特性。指令的值限定为绑定表达式

  ```javascript
    //v-if 指令将根据表达式 greeting 值的真假删除/插入 <p> 元素。
    <p v-if="greeting">Hello!</p>
    //有些指令可以在其名称后面带一个“参数” (Argument)，中间放一个冒号隔开
    <a v-bind:href="url"></a> // 缩写 <a :href="url"></a>
    <a v-on:click="doSomething"> // 缩写 <a @click="doSomething">
  ```

- 修饰符 (Modifiers)
  以半角句号 . 开始的特殊后缀，用于表示指令应当以特殊方式绑定

  ```javascript
    //.literal 告诉指令 将它的值 解析为一个字面字符串 而不是一个表达式
    <a v-bind:href.literal="/a/b/c"></a>
  ```

- 计算属性

  - 计算属性默认是 getter

    ```javascript
     <div id="example">
        a={{ a }}, b={{ b }}
       </div>         
    var vm = new Vue({
    el: '#example',
    data: {
      a: 1
    },
    computed: {
      // 一个计算属性的 getter
      b: function () {
        // `this` 指向 vm 实例
        return this.a + 1
      }
    }
    })

      a=1,b=2   
      console.log(vm.b) // -> 2
      vm.a = 2
      console.log(vm.b) // -> 3
    ```

  - $watch（用于观察 Vue 实例上的数据变动）

    ```javascript
    <div id="demo">{{fullName}}</div>
     var vm = new Vue({
      data: {
      firstName: 'Foo',
      lastName: 'Bar',
      fullName: 'Foo Bar'
      }
    })    
    vm.$watch('firstName', function (val) {
    this.fullName = val + ' ' + this.lastName
    })    
    vm.$watch('lastName', function (val) {
    this.fullName = this.firstName + ' ' + val
    })
    //当一些数据需要根据其它数据变化时,通常更好的办法是使用计算属性 而不是一个命令式的 $watch 回调。
    //最优解
    var vm = new Vue({
    data: {
      firstName: 'Foo',
      lastName: 'Bar'
    },
    computed: {
      fullName: function () {
        return this.firstName + ' ' + this.lastName
      }
    }
    })
    ```

  - 计算属性setter

    ```javascript
    // ...
    computed: {
    fullName: {
      // getter
      get: function () {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set: function (newValue) {
        var names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
    }
    // 现在在调用 vm.fullName = 'John Doe' 时,setter 会被调用，vm.firstName 和 vm.lastName 也会有相应更新。
    ```