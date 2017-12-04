---
title: Tornado异步接口正确编写方法
date: 2017-12-04 20:52:17
tags: python tornado ioloop
---

### Tornado简介:

[官方介绍](http://tornado-zh.readthedocs.io/zh/latest/guide/intro.html)如下:
> Tornado 是一个Python web框架和异步网络库，起初由 FriendFeed 开发。
> 通过使用非阻塞网络I/O, Tornado 可以支持上万级的连接，处理 长连接, WebSockets, 和其他
> 需要与每个用户保持长久连接的应用。

原理:

* Tornado使用了linux系统底层的`epoll`来实现它的高并发（在BSD和Mac OS X系统调用的`kqueue`）;

* 一个tornado的实例(instance)其实是一个`IOLoop`类的实例；

* `IOLoop`实例会不断轮询并处理当前活跃的连接。


### 一个简单的“Hello world"示例

```
import tornado.ioloop
import tornado.web

class MainHandler(tornado.web.RequestHandler):
    def get(self):
        self.write("Hello, world")

if __name__ == "__main__":
    application = tornado.web.Application([
        (r"/", MainHandler),
    ])
    application.listen(8888)
    tornado.ioloop.IOLoop.current().start()
```

### 一个异步的"Hello world"示例:

```
import tornado.ioloop
import tornado.web
import tornado.gen

class MainHandler(tornado.web.RequestHandler):

    @tornado.gen.corountine
    def get(self):
        res = yield foo()
        raise gen.Return(self.finish(res))

    @tornado.gen.corountine
    def foo():
        raise gen.Return("Hello, world")

if __name__ == "__main__":
    application = tornado.web.Application([
        (r"/", MainHandler),
    ])
    application.listen(8888)
    tornado.ioloop.IOLoop.current().start()
```

### 总结

*笔者工作中对Tornado, Django, Flask这三个web框架均有使用经验, 在此作一下个人经验总结:*

* Tornado的优势是长连接。若项目开发中不设计长连接，推荐选择Flask 或 Django等同步网络框架 + `gevent`的方案。

* 相较于pyhton的另外两个流行的web框架 Flask和Django，Tornado所支持的第三方库较少，且开发难度较大。


*申明：该网站所有文章均为原创，转载请著名出处:`http://blog.lican.site`，谢谢！*

<div id="SOHUCS" sid="tornado_ioloop"></div>
