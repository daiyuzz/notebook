# Celery介绍

## celery基础讲解

celery官网定义：
```
Celery 是一个简单、灵活且可靠的，处理大量消息的分布式系统，并且提供维护这样一个系统的必须工具。它是一个专注于实时处理的任务队列，同时也支持任务调度。
```
下面几个关于celery的核心知识 ：
- broker：翻译过来叫做中间人。它是一个消息传输的中间件，可以理解为一个邮箱。每当应用程序celery的异步任务的时候，会向broker传输消息，而后celery的worker将会取到消息，执行相应的程序。其实这就是消费者和生产者之间的桥梁。
- backend：通常程序发送的消息，发送就完了，可能都不知道对方接受。为此，celery实现了一个backend，用于存储这些消息以及celery执行的一些消息和结果。
- worker：Celery类的实例，作用就是执行各种任务。注意在celery3.1.25后window是不支持celery worker的！
- producer：发送任务，将其传递给broker
- beat：celery实现的定时任务。可以将其理解为一个producer，因为它也是通过网络定时将任务发送给worker执行。window上celery不知此定时任务。

下面是celery的构架示意图，结合上面的文字的话应该会更好理解

![](assets/20191007190912566_2128114493.png =700x)

由于celery只是任务队列，而不是真正意义上的消息队列，它自身不具有存储数据的功能，所以broker和backend需要通过第三方工具来存储信息，celery官方推荐的是`RabbitMQ`和`Redis`，另外`mongodb`等也是可以作为broker或者backend，可能不会很稳定，这里我们选择Redis作为broker和backend。



