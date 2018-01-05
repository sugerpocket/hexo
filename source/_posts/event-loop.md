---
title: 浅析 event loop 事件队列 microTask 和 macroTask 的区别
date: 2018-01-05 13:18:16
tags: 
  - web前端
  - nodejs
  - javascript
thumbnail: /2018/01/05/event-loop/thum.jpeg
banner: /2018/01/05/event-loop/thum.jpeg
---

javascript 是单线程的，但是如果遇到了不得不等待的任务，js 引擎并不会阻塞进程，而是异步地将任务添加到队列，不阻塞主进程，使得主进程可以先执行其他任务，等到进程空闲，再检查任务队列，本篇就 js 中的事件循环机制与任务队列调度的细节进行探讨。

<!--more-->

实际上，js 引擎并不只维护一个任务队列，总共有三种任务

1. Task(macroTask): `setTimeout`, `setInterval`, `setImmediate`, `I/O`, `UI rendering`
2. microTask: `Promise`, `process.nextTick`, `Object.observe`, `MutationObserver`, `MutaionObserver`

按照一般（之前的理解），这些函数都只是添加任务到队列里面，似乎并无差别

看这段经典代码，体会一下

```javascript
setTimeout(function() {
  console.log(4);
}, 0);

var promise = new Promise(function executor(resolve) {
  console.log(1);
  for (var i = 0; i < 10000; i++) {
    i == 9999 && resolve();
  }
  console.log(2);
}).then(function() {
  console.log(5);
});

console.log(3);
```

简单的 event-loop 理解，答案是

1
2
3
4
5

好吧，错了，答案是

1
2
3
5
4

这说明 `Promise.then` 注册的任务先执行了，why？

我们再来看一下之前说的 `Promise` 注册的任务属于 `microTask`，`setTimeout` 属于 `Task`，两者有何差别？

好吧，实际上，microTasks 和 Tasks 并不在同一个队列里面，他们的调度机制也不相同。比较具体的是这样：

> 个人总结，参考资料在文章末尾，有错误欢迎指正

1. event-loop start
2. microTasks 队列开始清空（执行）
3. 检查 Tasks 是否清空，有则跳到 4，无则跳到 6
4. 从 Tasks 队列抽取一个任务，执行
5. 检查 microTasks 是否清空，若有则跳到 2，无则跳到 3
6. 结束 event-loop

好，也就是说，microTasks 队列在一次事件循环里面不止检查一次，我们做个实验

```javascript
// 添加三个 Task
// Task 1
setTimeout(function() {
  console.log(4);
}, 0);

// Task 2
setTimeout(function() {
  console.log(6);
  // 添加 microTask
  promise.then(function() {
    console.log(8);
  });
}, 0);

// Task 3
setTimeout(function() {
  console.log(7);
}, 0);

var promise = new Promise(function executor(resolve) {
  console.log(1);
  for (var i = 0; i < 10000; i++) {
    i == 9999 && resolve();
  }
  console.log(2);
}).then(function() {
  console.log(5);
});

console.log(3);
```

以上这段代码在开始添加三个任务到 Tasks 里面，并且第二个 task 会添加一个 microTask

预测一下结果：

若每次执行一个 task 都会检查 microTask 队列，那么 microTask 会先于 task 3 执行，8 将会先于 7 输出，结果应该为

1
2
3
5
4
6
8
7

如果只检查一遍 microTasks，那么 8 将于下一次 event-loop 输出，也就是最后输出，结果应该为

1
2
3
5
4
6
7
8

毫无疑问，结果是前者（仔细阅读那段简单的测试代码，理解一下，复制粘贴试试结果，没有图片难以形象地描述）

结论就是，microTasks 会在每个 Task 执行完毕之后检查清空，而这次 event-loop 的新 task 会在下次 event-loop 检测。


对于大部分开发者来说，异步队列就好似不存在一般，我们只管用就可以了，有一定经验的人会了解事件循环，任务队列，但是很少人知道他们的实现细节，虽然这对开发来说无关紧要，但理解基础的东西会帮助开发者更好地解决问题（或许哪一天就踩到了坑呢）。

### 参考

https://juejin.im/entry/58332d560ce46300610e4bad
https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/
https://flyyang.github.io/2017/03/07/javascript%E4%B8%AD%E7%9A%84microtask%E4%B8%8Etask/
https://hao5743.github.io/2017/02/27/%E5%AF%B9node%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF%E6%9C%BA%E5%88%B6%E4%B8%ADMacrotask%E5%92%8CMicrotask%E7%9A%84%E7%90%86%E8%A7%A3/
https://github.com/ccforward/cc/issues/48
https://github.com/creeperyang/blog/issues/21
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop
