# class-based view 的处理流程

class-based view对外暴露的接口其实是`as_view`,现在我们要梳理一下`as_view`做了哪些事,以及在请求到达之后,它的处理流程是什么样的


## as-view 的逻辑

`as_view`其实只做了一件事,那就是返回一个闭包.这个闭包会在 Django 解析完请求之后调用,而闭包中的逻辑是这样的.

- 给 class (也就是我们定义的 View类)赋值 -- request , args , 和kwargs
- 根据 HTTP 方法分发请求.比如 HTTP GET 请求会调用 `class.get()`方法,POST 请求会调用 `class.post`方法


## 请求达到之后的完整逻辑

- 请求到达之后,首先会调用`dispatch`进行分发
- 接着会调用 get 方法
    - 在 GET 请求中, 首先会调用 `get_queryset()`方法,拿到数据源
    - 接着调用 `get_context_data`方法,拿到需要渲染到模板中的数据
        - 在 `get_context_data`中,首先会调用 `get_paginate_by`拿到每页数据
        - 接着调用 `get_context_object_name`拿到要渲染到模板中的这个 queryset 名称
        - 然后调用 `paginate_queryset`进行分页处理
        - 最后拿到的数据转为`dict`并返回
    - 调用 `render_to_response`渲染数据到页面中
        - 在`render_to_response`中调用 `get_template_name`拿到模板名
        - 然后把 `request, context, template_name` 等传递到模板中



