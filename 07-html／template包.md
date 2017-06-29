html/template包是Go标准库的一部分。我们可以使用html/template将HTML保存到单独的文件中，允许我们不修改底层Go代码的情况下改变编辑页面的布局。

首先，我们必须将html/template添加到导入列表中。我们再也不需要使用fmt包了，所以必须移除它。
```go
import (
"html/template"
"io/ioutil"
"net/http"
)
```

让我们创建一个包含HTML表单的模板文件。打开一个命名为edit.html的新文件，并添加下面几行内容：
```html
<h1>Editing {{.Title}}</h1>

<form action="/save/{{.Title}}" method="POST">
<div><textarea name="body" rows="20" cols="80">{{printf "%s" .Body}}</textarea></div>
<div><input type="submit" value="Save"></div>
</form>
```

修改editHandler函数以便使用这个末班，替代硬编码的HTML：
```go
func editHandler(w http.ResponseWriter, r *http.Request) {
    title := r.URL.Path[len("/edit/"):]
    p, err := loadPage(title)
    if err != nil {
        p = &Page{Title: title}
    }
    t, _ := template.ParseFiles("edit.html")
    t.Execute(w, p)
}
```

template.ParseFiles函数将读取edit.html的内容并返回一个*template.Template类型的值。

t.Execute方法执行模板，把生成的HTML写入到http.ResponseWriter。这个.Title和.Body点标识符引用自p.Title和p.Body。

模板指令都被双大括号包括着。printf "%s" .Body 指令是一个函数调用，将.Body作为字符串输出而不是字节流，如同调用fmt.Printf。通过模板动作，html/template包帮助确保只有安全和看起来正确的HTML被生成。例如，它自动转义大于号(>)，使用&gt;替换，以确保用户数据不打断表单的HTML。

现在因为我们正在使用模板工作，让我们为viewHandler创建一个叫做view.html的模板：

```html
<h1>{{.Title}}</h1>

<p>[<a href="/edit/{{.Title}}">edit</a>]</p>

<div>{{printf "%s" .Body}}</div>
```

Modify viewHandler accordingly:

据此修改viewHandler：

```go
func viewHandler(w http.ResponseWriter, r *http.Request) {
    title := r.URL.Path[len("/view/"):]
    p, _ := loadPage(title)
    t, _ := template.ParseFiles("view.html")
    t.Execute(w, p)
}
```

注意我们在两个处理函数中用了几乎完全一样的模板代码。让我们通过将模板代码移动到它自己的函数里移除这部分重复内容：

```go
func renderTemplate(w http.ResponseWriter, tmpl string, p *Page) {
    t, _ := template.ParseFiles(tmpl + ".html")
    t.Execute(w, p)
}
```

And modify the handlers to use that function:

并使用这个函数修改处理函数：

```go
func viewHandler(w http.ResponseWriter, r *http.Request) {
    title := r.URL.Path[len("/view/"):]
    p, _ := loadPage(title)
    renderTemplate(w, "view", p)
}

func editHandler(w http.ResponseWriter, r *http.Request) {
    title := r.URL.Path[len("/edit/"):]
    p, err := loadPage(title)
    if err != nil {
        p = &Page{Title: title}
    }
    renderTemplate(w, "edit", p)
}
```

如果我们注释掉主函数中为实现的保存处理函数，我们可以再次构建被那个测试我们的程序。点击此处查看迄今为止已写的代码。