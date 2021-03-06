---
slug: understand Promise, async and await
title: Asynchronous programming
authors: Yuntu
tags: [Promise, async, await, Java]
---

课代表来了(本视频所有概念也适用于其他异步编程的语言·)： 

JavaScript两种异步方式(单线程编程语言)：

   - 传统回调函数 (Callback Function) 例如 setTimeOut 

   - 回调函数缺点： 函数回调地狱 ： 一个函数执行完在执行内部另外一个，一层一层嵌套，
[视频](https://www.bilibili.com/video/BV1WP4y187Tu?t=128)

   - 单线程优点： 由于所有操作运行在一个线程中，无需考虑线程同步和资源竞争的开销，避免了线程之间的频繁切换和竞争问题，降低了开销


### Promise (承诺) ： 寓意这个去这个请求会在未来某一个时刻返回

解决了回调地狱的问题
例如： 
fetch 向服务器请求，返回Promise对象,渲染前端，如果可以使用then 进行接受 返回的结果
```js

	fetch("https://www.woshishuaige.om").then((res)=>{

})

```

Promise优点：

	我们可以用链式结构将多个异步操作串联起来如下

可以不断追加类似java8 stream，这样的链式调用避免了层层嵌套，一个人东西的出现一定是解决某类问题

 ```js

	fetch("https://www.woshishuaige.om").then((res)=>{

console.log(“我”)

}).then(res=>{

console.log(“是”)

}).then(res=>{

console.log(“你")

}).then(res=>{

console.log(“爹")

}).then(res=>{

console.log(“爹")

})

```

catch:



如果这样请求过程中某个戒断遇到错误，我们会触发catch，then将不会执行  ，类似 java 语法

         · ```js

	fetch("https://www.woshishuaige.om").then((res)=>{

console.log(“我”)

}).then(res=>{

console.log(“是”)

}).then(res=>{

console.log(“你")

}).then(res=>{

console.log(“爹")

}).then(res=>{

console.log(“爹")

}).catch(error=>{

console.log("如果程序报错了会输出我"+error)

})

``

finally 

我们在请求过程中 如果有一段 必须要执行的代码可以放在 finally 中 ，学过java异常就是一样的道理

一般我们用来释放资源， 关闭声明请求，清理工作

         · ```js

	fetch("https://www.woshishuaige.om").then((res)=>{

console.log(“我”)

}).then(res=>{

console.log(“是”)

}).then(res=>{

console.log(“你")

}).then(res=>{

console.log(“爹")

}).then(res=>{

console.log(“爹")

}).catch(error=>{

console.log("如果程序报错了会输出我"+error)

}).finally(()=>{

	// 

stopLoadingAnimation()

// Up 牛批

})

``

新标准ECMA17 的 async/ await

一句话概括： 

语法糖，让异步操作变简单的，这两关键字基本同根同生， 被 async 修饰的 funcation 接受 请求必须用 await 



```js

async function F(){

	const respnse = await fetc('https:///');

   const json = await response.json

}

```

多个Promise 使用时会打破并行，

第一个执行完才执行第二个，高效做法: 

 Promise.all内可以传递多个promise对象

然后再使用await ，效率会提升一倍



const [a,b]= await Promise.all([promiseA,promiseB])



如果想使用 循环中的 所有操作都并发执行可以使用  for await





```js

async funcation f(){

	const promises  =[

	someAsyncOperator();

	someAsyncOperator();

	someAsyncOperator();

];

   for await (let result of promises){



}



}

```

不能在全局和 普通函数 中使用 await关键字，只能被应用异步，所以想要使用 await 就必须 用 async修饰函数



所以说就跟你对象一样 ，必须相互配合才能完成一些事情

 作者：0136云图 https://www.bilibili.com/read/cv13486386 出处：bilibili