+++
title = '学习flask的第四天'
date = 2024-01-20T15:58:26+08:00
draft = false
+++
# jinja
## jinja的基本语法结构
### 1.块语句：
带有jinja语法关键字的各种标准性语句
#### jinja变量赋值：
```html
{% set links=[
    {'lable':'Home', 'herf':url_for('.index')},
    {'lable':'Home', 'herf':url_for('.about')},
    {'lable':'Home', 'herf':url_for('.services')},
    {'lable':'Home', 'herf':url_for('.projects')}
    ] %}
```
#### 使变量失效：
```html
{% with %}

{% set links=[
    {'lable':'Home', 'herf':url_for('.index')},
    {'lable':'Home', 'herf':url_for('.about')},
    {'lable':'Home', 'herf':url_for('.services')},
    {'lable':'Home', 'herf':url_for('.projects')},
    ] %}

{% endwith %}
```
#### 循环语句：
```html
<nav>
    {% for link in links %}
        <a href="{{ link.href }}">{{ link.lable }}</a>
    {% endfor %}
</nav>

#### 测试函数与自定义测试函数
<nav>
    {% for link in links %}
        {% if not loop.first %} | {% endif %}
        {% if link is current_link %}
        <a href="{{ link.href }}">{{ link.lable }}</a>
        {% endif %}
    {% endfor %}
</nav>
```
```python
@app.template_test('current_link')
def current_link(link):
    return link['href'] == request.path
```
### 2.表达式：
例如将变量打印到页面上的“{{}}”语句
### 3.单行语句
块语句的一种简化形式

# all code until today
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


@app.route("/services")
def services():
    return 'Services'


@app.route("/About")
def About():
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

@app.template_test('current_link')
def current_link(link):
    return link['href'] == request.path

if __name__ == "__main__":
    app.run(debug=True)

```