+++
title = '学习flask的第二天'
date = 2024-01-18T22:48:03+08:00
draft = false
+++
# click
看了半天click用法，还让Mario帮忙，最后还是出错了
```python
from flask import Flask, render_template,request,redirect,url_for,make_response,abort
from werkzeug.routing import BaseConverter
from werkzeug.utils import secure_filename
import click
from flask.cli import FlaskGroup
from os import path

class RegexConverter(BaseConverter):
    def __init__(self,url_map,*items):
        super(RegexConverter,self).__init__(url_map)
        self.regex=items[0]


def create_app():
    app = Flask(__name__)
    app.url_map.converters['regex'] = RegexConverter

    @app.route("/")
    def index():
        response = make_response(render_template('index.html', title='hello user'))
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
    return app

manager = FlaskGroup(create_app=create_app)

@click.command("rundev")
def dev():
    from livereload import Server
    app = create_app()
    live_server = Server(app.wsgi_app)
    live_server.watch('**/*.*')
    live_server.serve(open_url=True)

@click.command("hello")
@click.argument('user_name')
def run():
    app = create_app()
    with app.app_context():
        click.echo(app.config)

manager.add_command(dev)

manager.add_command(run)

if __name__ == "__main__":
    # app.run(debug=True)
    manager()
```
报错长这样：
![](/image/click.png)
还是改回去吧。。。