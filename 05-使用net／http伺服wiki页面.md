使用net/http包伺服wiki页面

使用net/http包，它必须被导入：
```go
import (
"fmt"
"io/ioutil"
"net/http"
)
```

让我们创建一个处理器函数，viewHandler将允许用户查看一个wiki页面。它将处理前缀带有"/view/"的URL。
```go
func viewHandler(w http.ResponseWriter, r *http.Request) {
    title := r.URL.Path[len("/view/"):]
    p, _ := loadPage(title)
    fmt.Fprintf(w, "<h1>%s</h1><div>%s</div>", p.Title, p.Body)
}
```

首先，这个函数从r.URL.Path中截取出页面的标题，请求URL的路径组成部分。Path被使用[len("/view/"):]丢弃掉请求路径的前导部分"/view/"然后重新切片。

然后函数加载页面数据，使用简单的HTML字符串格式化页面，并将其http.ResponseWriter变量w中。

再次提醒，注意使用_忽略来自loadPage的错误返回值。这里这样做我是为了简明明了，通常这样认为是坏的实践。我们会在后面注意这点。

为了使用这个处理函数，我们重写我们的main函数以便初始化http使用viewHandler来处理/view/路径下的任何请求。
```go
func main() {
    http.HandleFunc("/view/", viewHandler)
    http.ListenAndServe(":8080", nil)
}
```
点击此处查看到现在为止我们已经写的代码。

让我们创建一些页面数据(如test.txt)，编译我们的代码，并尝试伺服一个wiki页面。

在你的编辑器里打开test.txt文件，并且将字符串"Hello world"（没有引号）保存到该文件。
 ```shell
$ go build wiki.go
$ ./wiki
```

(如果你正在使用Windows你不必输入"./"即可运行程序。)

运行web服务器时，访问 http://localhost:8080/view/test 应该会显示一个标题为“test“，包含"Hello world"文字的页面。