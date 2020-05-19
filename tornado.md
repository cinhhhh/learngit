# Tips of TORNADO
## TCP协议
三次握手建立TCP协议<br>
等待服务器响应时间
## urllib -> socket  通过socket直接获取html
## select poll epoll
* select：writefds readfds exceptfds  监视三种文件描述
最大监视事件数 1024linux
* epoll 没有限制最大数 只拷贝一次
## 事件循环
**selector**：<br>*unregister* *register*两个方法，监听事件（可读、可写）<br>
需要额外写函数驱动不断循环去监听事件<br>
**事件循环存在的问题**：<br>1、回调过深，维护麻烦  2、异常无法向上抛出
## 协程
* 可以**被暂停**并切换到**其他协程**运行的函数
* 定义协程的方法：<br> 1.`from tornado.gen import coroutine`&nbsp;使用装饰器`@coroutine`将函数装饰成协程，这种方法实际是利用了生成器的特点，使函数变成协程 <br>2. 直接使用`async def xxx`, 原生协程，**推荐**使用这种, 使用`await`获取协程的值
* run_sync 方法可以在运行某个协程之后停止事件循环
* `tornado.queue` 是非阻塞的队列