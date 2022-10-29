```js
for(var i = 0; i < 5; i++) {
    setTimeout(() => {
        console.log(i)
    }, 1000)
}
console.log(i)//5
```

输出5个5

原因 : 由于变量通过var定义,所以这个变量(i)是全局变量,有根据js事件执行机制,setTimeout属于宏任务, //for循环属于同步代码,因此先执行for循环,最后再执行setTimeout. // 题: 在for循环中使用setTimeout. 

解决办法:

 1.借助let暂时性死区. 

```js
for(let i = 0; i < 5; i++) {
    setTimeout(() => {
        console.log(i)
    }, 1000)
}
console.log(i)
```

2.借助setTimeout的第三个参数 

```js
for(var i = 0; i < 5; i++) {
    setTimeout((j) => {
        console.log(j)
    }, 1000, i)
}
console.log(i)
```

3.借助函数的闭包 

```js
for(var i = 0; i < 5; i++) {
    ((j) => {
        setTimeout(() => {
            console.log(j)
        }, 1000)
    })(i)
}
console.log(i)
```

