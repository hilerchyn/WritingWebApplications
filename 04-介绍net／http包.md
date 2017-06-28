Introducing the net/http package (an interlude)
介绍net/http包(一个小插曲)

Here's a full working example of a simple web server:

这儿有一个简单web服务器的完整工作示例：

package main

import (
    "fmt"
    "net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hi there, I love %s!", r.URL.Path[1:])
}

func main() {
    http.HandleFunc("/", handler)
    http.ListenAndServe(":8080", nil)
}

The main function begins with a call to http.HandleFunc, which tells the http package to handle all requests to the web root ("/") with handler.

main函数开始调用了http.HandleFunc函数，告诉http包使用handler函数处理网站根("/")目录的所有请求。

It then calls http.ListenAndServe, specifying that it should listen on port 8080 on any interface (":8080"). (Don't worry about its second parameter, nil, for now.) This function will block until the program is terminated.

然后调用http.ListenAndServe函数，指明它应该在任何接口设备上的8080端口上监听(":8080")。（现在为止不需要担心它的第二个参数nil）这个函数直至程序终止都将是阻塞的。

The function handler is of the type http.HandlerFunc. It takes an http.ResponseWriter and an http.Requestas its arguments.

handler函数是http.HandlerFunc类型。它带有一个http.ResponseWriter和一个http.Request作为它的参数。

An http.ResponseWriter value assembles the HTTP server's response; by writing to it, we send data to the HTTP client.

一个http.ResponseWriter值汇集了HTTP服务器的应答；通过想它写入数据，我们把数据发送到客户端。

An http.Request is a data structure that represents the client HTTP request. r.URL.Path is the path component of the request URL. The trailing [1:] means "create a sub-slice of Path from the 1st character to the end." This drops the leading "/" from the path name.

一个http.Request表示客户端HTTP请求的数据结构。r.URL.Path是请求URL路径的组成部分。尾部的[1:]意味着"创建一个Path从第一个字符到结尾的子切片。",这样就把路径名称开头的"/"丢弃掉了。

If you run this program and access the URL:

如果你运行这个程序并访问URL:

http://localhost:8080/monkeys

the program would present a page containing:

程序会呈现一个包含如下内容的页面:

Hi there, I love monkeys!
