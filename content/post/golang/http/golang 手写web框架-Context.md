---
title: "[手写web框架 (二) | golang context]"
date: 2020-06-24T11:19:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["golang"]
categories: ["golang/手写web框架"]
author: "geektutu"
---

 > 转载自 https://geektutu.com/post/gee-day2.html

 # 摘要

 - 将路由`router`独立出来，方便之后增强。
 - 设计上下文`context`，封装Request和Response，提供对JSON、HTML等返回类型的支持。

# 使用效果

&emsp;&emsp;为了展示第二天的成果，我们看一看在使用时的效果。

```
func main() {
    r := gee.New()
    r.GET("/", func(c *gee.Context){
        c.HTML(http.StatucOK, "<h1>Hello Gee</h1>")
    })
    r.GET("/hello", func(c *gee.Context){
        c.String(http.StatusOK, "htllo %s, you are at %s\n", c.Query("name"), c.Path)
    })

    r.POST("/login", func(c *gee.Context){
        c.JSON(http.StatusOK, gee.H{
            "username": c.PostForm("username"),
            "password": c.PostForm("password")
        })
    })

    r.Run(":9999")
}
```

- `Handler`的参数变成了`gee.Context`，提供了查询Query/PostForm参数的功能。
- `gee.COntext`封装了`HTML/String/JSON`函数，能够快速构造HTTP响应。

# 设计Context

- 必要性

&emsp;&emsp;对Web服务来说，无非是根据请求`*http.Request`，构造响应`http.ResponseWriter`。但是这两个对象提供的接口粒度太细，比如我们要构造一个完整的响应，需要考虑消息头Header和消息题Body，而Header包含了状态码StatusCode，消息类型ContentType等几乎每次请求都需要设置的信息。因此，如果不进行有效的封装，那么框架的用户将需要写大量重复繁杂的代码，而且容易出错。针对常用场景，能够高效地构造出HTTP响应是一个好框架必须考虑的点。

&emsp;&emsp;用返回JSON数据做比较，感受下封装前后的差距。

- 封装前

```
obj = map[string]interface{}{
    "name": "gee",
    "password": "1234",
}
w.Header().Set("Content-Type", "application/json")
w.WriteHeader(http.StatusOK)
encoder := json.NewEncoder(w)
if err := encoder.Encode(obj); err != nil {
    http.Error(w, err.Error(), 500)
}
```

- 封装后

```
c.JSON(http.StatucOK, gee.H{
   "name": c.PostForm("name"),
    "password": c.PostForm("password"),
})
```

&emsp;&emsp;针对使用场景，封装`*http.Request`和`http.ResponseWriter`的方法，简化相关接口的调用，只是设计Context的原因之一。对于框架来说，还需要支撑额外的功能。例如，将来解析动态路由`/hello:name`，参数`:name`的值放在哪呢？再比如，框架需要支持中间件，那中间件产生的信息放在哪？Context随着每一个请求的出现而产生，请求的结束而销毁，和当前请求强相关的信息都应该由Context承载。因此，设计Context结构，扩展性和复杂性留在了内部，而对外简化接口。路由的处理函数，以及将要实现的中间件，参数都统一使用Context实例，Context就像一次绘画的百宝箱，可以找到任何东西。

## context.go

```
// H is aslias of JSON
type H map[string]interface{}

type Context struct {
    Writer http.ResponseWriter
    Req *http.Request
    Path string
    Method string
    StatusCode int    
}

func newContext()(w http.ResponseWriter, req *http.Request) *Context {
    return &Context{
        Writer: w,
        Req: req,
        Path: req.URL.Path,
        Method: req.Method,
    }
}

func (c *Context) PostForm(key string) string {
    return c.Req.FormValue(key)
}

func (c *Context) Query(key string) string {
    return c.REq.URL.Query().Get(key)
}

func (c *Context) Status(code int) {
    c.StatusCode = code
    c.Writer.WriteHeader(code)
}

func (c *Context) SetHeader(key string, value string) {
    c.Writer.Header().Set(key, value)
}

func (c *Context) String(code int, format string, values ...interface{}) {
    c.SetHeader("Content-Type", "text/plain")
    c.Status(code)
    c.Writer.Write([]byte(fmt.Sprintf(format, values...)))
}

func (c *Context) JSON(code int, obj interface{}) {
    c.SetHeader("Content-Type", "application/json")
    c.Status(code)
    encoder := json.NewENcoder(c.Writer)
    if err := encoder.Encode(obj); err != nil {
        http.Error(c.Writer, err.Error(), 500)
    }
}

func (c *Context) Data(code int, data []byte) {
    c.Status(code)
    c.Writer.Write(data)
}

func (c *Context) HTML(code int, html string) {
    c.SetHeader("Content-Type", "text/html")
    c.Status(code)
    c.Writer.write([]byte(html))
}
```

- 代码最开头，给`map[string]interface{}`起了一个别名`gee.H`，构建JSON数据时，显的更简洁。
- `Context`目前只包含了http.ResponseWriter和*http.Request, 另外提供了对Method和Path这两个常用属性的直接访问。
- 提供了访问Query和PostForm参数的方法。
- 提供了快速构造String/Data/JSON/HTML响应的方法。

## router.go

&emsp;&emsp;我们将路由相关的方法和结构提取出来，放到一个新的文件中的router.go，方便我们下一次对router的功能进行增强，例如提供动态路由的支持。router的handle方法做了一个细微的调整，即handler的参数，变成了Context。

```
type rputer struct {
    handlers map[string]HandlerFunc
}

func newRouter() *router {
    return &couter{handlers: make(map[string]HandlerFunc)}
}

func (r *router) addRoute(method string. pattern string, handler HandlerFunc) {
    log.Printf("Route %4s - %s", method, pattern)
}
```