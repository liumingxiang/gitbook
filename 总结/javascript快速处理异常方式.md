# javascript快速处理异常方式

### 1.快速检测暴露出来的全局变量

##### 一般情况下是我们忘记加`var`定义

###### 基本做法就是进行属性的检测

```javascript
Object.defineProperty(window,'i'{
  get:function(){return window.i;},
  set:function(newValue){debugger;window.i=newValue;},
  enumerable:true,//是否可以被枚举
  configurable:true//是否可以被删除
});



这样子做就可以很快查询到我们暴露到全局中的变量，
```

###### 当然我们还有更简单的方式来解决

`window.__defineSetter__('i',function(){ debugger })`

###### 当然我们不挖坑才是最好的解决方式

1. 使用use strict;  

   在严格模式下，这种问题暴露无遗，每个函数内都加上 use strict;，虽然在语言上有所限制，但是低级错误一定不会出现，因为严格模式会给你报错！


2.    使用 jslint/jshint 等js 分析工具

      ​