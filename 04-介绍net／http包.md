介绍net/http包(一个小插曲)

这儿有一个简单web服务器的完整工作示例：
```go
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
```

main函数开始调用了http.HandleFunc函数，告诉http包使用handler函数处理网站根("/")目录的所有请求。

然后调用http.ListenAndServe函数，指明它应该在任何接口设备上的8080端口上监听(":8080")。（现在为止不需要担心它的第二个参数nil）这个函数直至程序终止都将是阻塞的。

handler函数是http.HandlerFunc类型。它带有一个http.ResponseWriter和一个http.Request作为它的参数。

一个http.ResponseWriter值汇集了HTTP服务器的应答；通过想它写入数据，我们把数据发送到客户端。

一个http.Request表示客户端HTTP请求的数据结构。r.URL.Path是请求URL路径的组成部分。尾部的[1:]意味着"创建一个Path从第一个字符到结尾的子切片。",这样就把路径名称开头的"/"丢弃掉了。

如果你运行这个程序并访问URL:

http://localhost:8080/monkeys

程序会呈现一个包含如下内容的页面:

Hi there, I love monkeys!
