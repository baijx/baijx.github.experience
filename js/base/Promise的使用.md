# Promise的使用

## 定义
Promise是异步编程的一种解决方案。有了 Promise 对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise 对象提供统一的接口，使得控制异步操作更加容易。
从语法上说，Promise就是一个对象，里面保存着某个未来才会结束的事件 (通常是一个异步操作)的结果，从它可以获取异步操作的消息。

## 基本的API
> Promise自己身上有all、reject、resolve等方法，原型上有then、catch等方法。
1. Promise.resolve()
2. Promise.reject()
3. Promise.prototype.then()
4. Promise.prototype.catch()
5. Promise.all() // 所有的完成
6. Promise.race() // 竞速，完成一个即可

## 常见用法
> 在方法最后return new Promise对象，调用该方法时调用then方法，相当于回调函数。实现了回调写法分离。then方法语法：promise.then(onCompleted, onRejected)，onRejected可选。
```js
function runAsync(){
    return new Promise(function(resolve, reject){
        //做一些异步操作
        var num = Math.ceil(Math.random()*10); //生成1-10的随机数
            if(num<=5){
                resolve(num); // 模拟成功
            }
            else{
                reject('数字太大了'); // 模拟失败
            }
    });
}

// 其他位置调用异步方法
runAsync().then(
    // 第一个参数onCompleted，
    function(data){
        console.log('resolved');
        console.log(data);
    }, 
    // 第二个参数onRejected，可选
    function(err, data){
        console.log('rejected');
        console.log(err);
    }
);
```

## catch用法
> catch和then的第二个参数一样，用来指定reject的回调
```js
runAsync()
.then(function(data){
    console.log('resolved');
    console.log(data);
})
.catch(function(err){
    console.log('rejected');
    console.log(err);
});
```

> catch还可以捕获多种异常
```js
somePromise.then(function() {
 return a.b.c.d();
}).catch(TypeError, function(e) {
 //If a is defined, will end up here because
 //it is a type error to reference property of undefined
}).catch(ReferenceError, function(e) {
 //Will end up here if a wasn't defined at all
}).catch(function(e) {
 //Generic catch-the rest, error wasn't TypeError nor
 //ReferenceError
});
```

## all的用法
> all方法提供了并行执行异步操作的能力，在所有异步操作执行完后才执行回调
```js
Promise
.all([runAsync1(), runAsync2(), runAsync3()])
.then(function(results){
    console.log(results);
});
```

## any 、some、race
```js
Promise.some([
ping("ns1.example.com"),
ping("ns2.example.com"),
ping("ns3.example.com"),
ping("ns4.example.com")
], 2).spread(function(first, second) {
console.log(first, second);
}).catch(AggregateError, function(err) {
```
