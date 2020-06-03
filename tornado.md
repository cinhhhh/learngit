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

## web of tornado


### tornado不能用同步IO
### url配置
`urls[()]` tornado会自动转成这个对象 `tornado.web.URLSpec`
### define options
`define`定义在命令行传递的参数以及类型
`options` 全局只有一个options,不需要实例化
### requestHandler（是一个长连接）

* `web.RequestHandler` 处理每个url的类要继承这个类<br>
客户端发起不同的请求时，只需重写这个类的方法即可，例如：post、delete、put、patch

* 入口方法`_initialize`<br>
* `prepare`真正调用请求之前的初始化方法，要在_initialize方法之前走这个接口
* `on_finish`方法，类似于unittest中的setupclass
####输入方法：
* `get_argument()` 方法返回一个string类型<br>`get_arguments()`返回的是一个list类型，这个方法可以获取url中的餐参数和body中的参数<br>
* `get_body_argument()`和`get_body_arguments()`可以获取json格式数据
####输出
* `set_status()`方法
* 使用`write()`方法每调用一次，就会放到缓存区，不会断开连接，当所有的`write()`方法执行完，才会一并发送。如果需要断开连接，需要使用`finish()`方法，也可以返回数据，（如果发送的dict，会返回json格式数据）
* `write_error`可以返回报错页面
#### requestHandler的子类
* 301永久重定向，http->https;&nbsp;302临时重定向
* 重定向的方法：`RedirectHandler`和`self.redirect()`的区别：`RedirectHandler`是写在url中的，可以设置`permanent`参数（True永久重定向，Flase临时重定向）；`self.redirect()`是写在业务逻辑里的
* `StaticFileHandler`，代理静态文件<br>`settings = {
    "static_path":"C:/py_work/tornadoTest/web/tatic",
    "static_url_prefix":"/static"
}`
### template
## peewee（orm）
[peewee官方文档地址](http://docs.peewee-orm.com/en/latest/index.html )  

* 优点：隔离数据库差异  便于维护 防sql注入  变量传递方便
### 使用peewee生成表
* 要先，根据所需要的数据库类型，初始化数据库。例如：  `db = MySQLDatabase('message', host='127.0.0.1', port=3306, user='root', password='wait')`，
* 建立model，`from peewee import Model` 自己创建的model要继承这个Model，`CharField()`的`index=Ture`参数可以对该字段建立索引（查询快速）
* 生成表可以直接调用`db.create_tables([model_name])`
### 使用peewee保存数据
实例化model后，直接调用`save()`方法
### 使用peewee获取数据
**获取单条数据**<br> 
`get_by_id()`方法会立即执行sql语句，并返回model的实例对象  
`get(model.property = xxx)`  
**获取所有数据**  
直接调用`model.select()`方法,返回的是modelselect对象，不会立即执行sql语句，而是当进入for循环才会执行，（迭代协议）  
**根据条件获取**  
| peewee语法 | 对应sql语法 | 
| ------ | ------ | 
| `model.select(model.property1, model.property2)` | select * from table |
| `model.select().where(model.property1>100 )` | select * from table where property>100 |
| `model.select().where(model.property1>100)&where(model.property2>200)`|where xxx and xxx|
|`model.select().where(model.property1>100)|where(model.property2>200)`|where xxx or xxx|
|`model.select().where(model.property.contains())` | where xxx like xxx |
|`model.select().order_by(model.property.desc())`|order by|

