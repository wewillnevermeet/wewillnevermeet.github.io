+++
title = '学习Flask的第一天'
date = 2024-01-16T00:46:47+08:00
draft = false
+++

# 1.用到的插件
![](/image/day1_pluginunit.png)
```python
from flask import Flask, render_template,request
from werkzeug.routing import BaseConverter
```
# 2.路由转换器
url转换器有三种：int,float,path
![](/image/day1_urlturner.png)
```python
# 路由转换器：int， float， path路径
@app.route('/user/<int:user_id>')
def user(user_id):
    return 'User %d' % user_id
```
运行结果如下图：
![](/image/day1_ending1.png)

# 3."/"和多个url
首先来看一个简单的about
![](/image/day1_about.png)
```python
@app.route("/About")
def about():
    return 'About'

```
![](/image/day1_moerurl.png)
```python
# 在解释http请求时实际上是指向projects这个虚目录下的一个文件夹
# 通常是index，真正地址(/project/index.html)
# 对于上面的about会被作为文件名解释，加一个/会404
@app.route('/projects/')
@app.route('/ourroute/')
def projects():
    return 'the project page'
# 可以提供多个url，只需加上一个新的route定义
# 在匹配url规则时是从上往下的，匹配到对应规则就进入函数
```

# 4.正则表达式
需要创建一个类
![](/image/day1_regex.png)
```python
class RegexConverter(BaseConverter):
    def __init__(self,url_map,*items):
        super(RegexConverter,self).__init__(url_map)
        self.regex=items[0]


app = Flask(__name__)
app.url_map.converters['regex'] = RegexConverter
```
然后就可以制定url规则了
![](/image/day1_urlrule.png)

# 5.传meythod（涉及HTML）
![](/image/day1_method.png)
![](/image/day1_html.png)
```python
@app.route('/login',methods=['GET','POST'])
def login():
    return render_template('login.html',method=request.method)
```
运行结果如图：
![](/image/day1_methodget.png)
输入一些内容并提交后
![](/image/day1_methodpost.png)

# 6.至今为止的全代码
```python
from flask import Flask, render_template,request
from werkzeug.routing import BaseConverter

class RegexConverter(BaseConverter):
    def __init__(self,url_map,*items):
        super(RegexConverter,self).__init__(url_map)
        self.regex=items[0]


app = Flask(__name__)
app.url_map.converters['regex'] = RegexConverter

@app.route("/")
def hello():
    return render_template('index.html', title='hello world')


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

@app.route('/user/<regex("[a-z]{3}"):user_ifm>')
# 只能是a-z之间的三个字母
def regex(user_ifm):
    return 'User %s' % user_ifm

@app.route('/login',methods=['GET','POST'])
def login():
    return render_template('login.html',method=request.method)

if __name__ == "__main__":
    app.run(debug=True)

```