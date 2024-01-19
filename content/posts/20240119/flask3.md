+++
title = '学习flask的第三天'
date = 2024-01-19T16:36:00+08:00
draft = false
+++
# 禁用自动转义
![](/image/jinjablock.png)
使用jinja的block（块）写法禁用转义  
当文件绝对安全时  
可以使用|safe过滤器
![](/image/safe.png)
在jinja中，“|safe”称为过滤器
直接用于禁用自动转义
过滤器本质是在将变量输出到页面前的预操作
一个模板可以使用多个不同的过滤器
例如：{{ title|safe|tojson }}
更多过滤器说明可以看jinja官网
jinja.pocoo.org/docs/dev/tempaltes#builtin-filters
# 自己做一个Markdown过滤器
需要引入Markdown模块和reduce模块
```python
from functools import reduce
```
开始定义：
```python
@app.template_filter('md')
def markdown_to_html(txt):
    from markdown import markdown
    return markdown(txt)
```
## 写一个上下文处理器
![](/image/textclq)
```python
# 上下文处理器

def read_md(filename):
    with open(filename,encoding='utf-8') as md_file:
        content = reduce(lambda x, y: x + y, md_file.readlines())
    return content

@app.context_processor
def inject_methods():
    return dict(read_md=read_md)

```
# 尝试调用这个过滤器吧
先创建一个md文件吧
![](/image/html1.png)
在返回的页面里使用我们的过滤器
![](/image/useglq)
运行结果长这样
![](/image/glqresult)

## 编码问题
windows能不能放弃你那gbk！！！！
开头加一个“# coding: utf-8”
```python
# coding: utf-8
```
在open中加入“encoding=utf-8”
```python
def read_md(filename):
    with open(filename,encoding='utf-8') as md_file:
        content = reduce(lambda x, y: x + y, md_file.readlines())
    return content
```
# 截至目前的代码
```python
# coding: utf-8
from flask import Flask, render_template,request,redirect,url_for,make_response,abort
from werkzeug.routing import BaseConverter
from werkzeug.utils import secure_filename
from os import path
from functools import reduce

class RegexConverter(BaseConverter):
    def __init__(self,url_map,*items):
        super(RegexConverter,self).__init__(url_map)
        self.regex=items[0]


app = Flask(__name__)
app.url_map.converters['regex'] = RegexConverter

@app.route("/")
def index():
    response = make_response(render_template('index.html', 
                                             title='<h1>hello user</h1>',
                                             body = "## header"
                                             ))
    response.set_cookie('user_name','')

    return response


@app.route("/service")
def services():
    return 'Services'


@app.route("/About")
def about():
    return 'About'

# 路由转换器：int， float， path路径
@app.route('/user/<int:user_id>')
def user(user_id):
    return 'User %d' % user_id

# 在解释http请求时实际上是指向projects这个虚目录下的一个文件夹
# 通常是index，真正地址(/project/index.html)
# 对于上面的about会被作为文件名解释，加一个/会404
@app.route('/projects/')
@app.route('/ourroute/')
def projects():
    return 'the project page'
# 可以提供多个url，只需加上一个新的route定义
# 在匹配url规则时是从上往下的，匹配到对应规则就进入函数

# 只能是a-z之间的三个字母
@app.route('/user/<regex("[a-z]{3}"):user_ifm>')
def regex(user_ifm):
    return 'User %s' % user_ifm

@app.route('/login',methods=['GET','POST'])
def login():
    if request.method=='POST':
        username=request.form['user_name']
        passward=request.form['passward']
        print(username)
        print(passward)
    else:
        username=request.args['user_name']
        print(username)
    return render_template('login.html',method=request.method)

@app.route('/upload',methods=['GET','POST'])
def upload():
    if request.method=='POST':
        f = request.files['file']
        basepath = path.abspath(path.dirname(__file__))
        upload_path=path.join(basepath,'static/uploads',secure_filename(f.filename))
        f.save(upload_path)
        return redirect(url_for('upload'))
    return render_template('upload.html')

@app.errorhandler(404)
def page_not_found(error):
    return render_template('404.html'), 404
    

@app.template_filter('md')
def markdown_to_html(txt):
    from markdown import markdown
    return markdown(txt)

# 上下文处理器

def read_md(filename):
    with open(filename,encoding='utf-8') as md_file:
        content = reduce(lambda x, y: x + y, md_file.readlines())
    return content

@app.context_processor
def inject_methods():
    return dict(read_md=read_md)

if __name__ == "__main__":
    app.run(debug=True)

```