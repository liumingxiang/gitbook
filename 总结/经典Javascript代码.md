## 经典Javascript代码

```javascript
function fn(){
   console.log.apply(console,arguments)
}
fn(12,22);
//这一行主要是利用上下文模式，来调用console来打印传进来的参数

```

