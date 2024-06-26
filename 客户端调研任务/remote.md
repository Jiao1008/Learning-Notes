# Remote 模块

介绍：为渲染进程和主进程通信封装了一种简单方法，可直接获取主进程对象或者调用主进程函数或对象的方法，不比显示地发送进程间消息。

```
const { remote } = require('electron')
const myModel = remote.require('myModel')
myModel.doSomething()
```

### 缺陷
- 当渲染进程调用远程对象的方法/函数时，是进行同步IPC通信的，会阻塞用户代码执行，直到远程对象方法/函数执行完毕。（频繁调用，会影响主进程和渲染进程的性能）
- 主进程会保持引用每一个渲染进程访问的对象，包括函数的返回值。（频繁调用，会对内存的占用和垃圾回收造成不小压力）
- 数组属于“基本对象”而非“引用对象”，通过值拷贝传递，渲染进程中修改时，无法修改到原始数组。
- 对象在首次引用时，会创建一个新对象，并把引用对象中的属性拷贝到新对象中，之后对引用对象进行修改，不会影响新对象。
- 渲染进程传递的回调会被异步调用，而主进程会忽略该回调的返回值。
- 对象泄露。
- 在给主进程传递回调时，主进程会保持回调的引用，直到主进程被销毁。所以在使用remote模块时进行事件订阅时，要接触事件订阅。