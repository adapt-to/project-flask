=====================
初识Flask
=====================

Flask快速入门
=================
  ----利用虚拟环境进行Flask开发

1. Python虚拟环境的搭建
----------
  
一、windows下 **创建虚拟环境**

由于Python3.4及以上自带venv，和使用virtualenv创建虚拟环境无异，好处是不需要安装额外的包，

只需在想要创建虚拟环境的目录下打开cmd命令窗口并输入执行 ``python -m venv my_venv`` 即可创建，其中my_venv是创建的自定义虚拟环境名。

二、 windows下 **启动虚拟环境**

在虚拟环境所在文件夹下打开命令行输入 ``my_venv\Scripts\activate.bat`` 进入环境，然后可以使用pip安装Python包了。

三、 windows下 **退出虚拟环境**

使用 ``deactivate`` 退出虚拟环境

2. Flask安装
--------------

激活虚拟环境，然后执行 ``pip install flask`` 安装Flask

3. WSGI
-------------
    --------- （Web Server Gateway Interface，Web服务器网关接口）
    
在Flask里面是利用 `Werkzeug <http://werkzeug.pocoo.org/>`_ 工具集实现该功能的，它也是Flask的核心，在安装Flask时会自动安装此工具集，另外一个核心工具集是 `Jinja2 <http://jinja.pocoo.org/>`_ ，它是模板引擎。

WSGI指Python Web Server Gateway Interface，它是为了让Web服务器与Python程序能够进行 **数据交流** 而定义的一套接口标准/规范。如果不统一标准，那么众多的 Python Web框架都可能仅被某些Web服务器支持；而Web服务器也没法支持所有的Python Web框架。

Flask的werkzeug示例 ::
    
    from werkzeug.wrappers import Request, Response

    @Request.application # 装饰器
    def hello(request):
        return Response('Hello World!') # 返回响应

    if __name__ == '__main__':
        from werkzeug.serving import run_simple
        run_simple('localhost', 4000, hello) # 监听4000端口，请求一进来就调用hello这个函数

Python标准库提供的独立WSGI服务器称为wsgiref

wsgiref示例 ::

    from wsgiref.simple_server import make_server
    
    def runserver(environ, start_response):
        start_response('200 OK', [('Content-Type', 'text/html')]) # 生成状态码
        return [bytes('<h1>Hello, web!</h1>', encoding='utf-8'), ] # 返回响应
    
    
    if __name__ == '__main__':
        httpd = make_server('', 8000, runserver) # 监听8000端口，请求一进来就调用runserver这个函数
        httpd.serve_forever()

可以看到不管是利用Flask的werkzeug还是Python标准库提供的独立WSGI服务器称为wsgiref，原理上都是相似的。
所以Flask本质上还是利用的一套东西实现的，如果再究其本质，实际上是利用socket来实现的

socket示例 ::
  
    import socket
    
    def handle_request(client):
        buf = client.recv(1024)
        client.send("HTTP/1.1 200 OK\r\n\r\n")
        client.send("Hello, Seven") # 发送响应
    
    def main():
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.bind(('localhost',8000)) # 监听端口
        sock.listen(5)
    
        while True:
            connection, address = sock.accept() # 获取请求
            handle_request(connection) # 调用函数
            connection.close()
    
    if __name__ == '__main__':
        main()

所以说，python_web框架本质的本质都是利用socket来实现的。

4. Flask
-------------
    ----Flask web关于启动时的源码剖析

Flaks简单示例 ::

    from flask import Flask

    # 实例化flask对象
    app = Flask(__name__)
    
    # 下面语句的含义是将 "/" 和 函数 index 的对应关系添加到路由表中
    @app.route('/') # 这里执行了两步：1. value = app.route('/') 2. value(index)
    def index():
        return 'Hello World!'
    
    if __name__ == '__main__':
        # 监听用户请求，如果有请求到来则执行app的__call__方法，这也是整个请求的入口
        # 表示在__call__方法中对请求数据进行封装并且把请求相关的东西用来做路由映射，执行视图函数，最后把视图函数返回的内容让用户看到
        app.run() # 在新版本的Flask中，建议使用flask run命令行代码来替代此写法

Flask中初始化app实例的部分源码 :: 
    
    def __init__(
            self,
            import_name, # 实例名称. 上面的例子中app名为 ``__name__`` ，即文件名
            static_url_path=None, # 静态文件路径
            static_folder='static', # 静态文件文件夹名
            static_host=None,
            host_matching=False,
            subdomain_matching=False,
            template_folder='templates', # 模板文件文件夹名
            instance_path=None, # 示例路径
            instance_relative_config=False, # 将instance_relative_config设为True，这是告诉Flask我们的配置文件路径是相对于实例文件夹的（默认是相对于程序实例根目录的）
            root_path=None # root路径
        ):
        ...省略

Flask中执行app.run()对应的源码操作 ::

    def run(self, host=None, port=None, debug=None,
            load_dotenv=True, **options):

        if os.environ.get('FLASK_RUN_FROM_CLI') == 'true':
            from .debughelpers import explain_ignored_app_run
            explain_ignored_app_run()
            return

        if get_load_dotenv(load_dotenv):
            cli.load_dotenv()

            # if set, let env vars override previous values
            if 'FLASK_ENV' in os.environ:
                self.env = get_env()
                self.debug = get_debug_flag()
            elif 'FLASK_DEBUG' in os.environ:
                self.debug = get_debug_flag()

        if debug is not None:
            self.debug = bool(debug)

        _host = '127.0.0.1'
        _port = 5000 # 默认监听5000端口
        server_name = self.config.get('SERVER_NAME')
        sn_host, sn_port = None, None

        if server_name:
            sn_host, _, sn_port = server_name.partition(':')

        host = host or sn_host or _host
        port = int(port or sn_port or _port)

        options.setdefault('use_reloader', self.debug) # 自动重启
        options.setdefault('use_debugger', self.debug) # debug模式
        options.setdefault('threaded', True)

        cli.show_server_banner(self.env, self.debug, self.name, False)

        from werkzeug.serving import run_simple  # 这一句是核心，web的本质就在这里

        try:
            run_simple(host, port, self, **options) # 这里的self就是指代前面的flask的实例对象app
            # 由于对象后面加括号，触发执行。构造方法的执行是由类创建对象触发的，即：对象 = 类名() ；而对于 __call__ 方法的执行是由对象后加括号触发的，即：对象() 或者 类()()
            # 上面的例子中的run_simple('localhost', 4000, hello)中是执行hello(),而这里变成了对象app，所以app()执行其__call__方法
        finally:
            self._got_first_request = False

__call__方法源码 ::

    def __call__(self, environ, start_response):
        return self.wsgi_app(environ, start_response) # 在这里面又执行自身的wsgi_app方法

wsgi_app方法源码（这个方法也就是所有请求的入口） ::

    def wsgi_app(self, environ, start_response):
        ctx = self.request_context(environ)
        error = None
        try:
            try:
                ctx.push()
                response = self.full_dispatch_request()
            except Exception as e:
                error = e
                response = self.handle_exception(e)
            except:
                error = sys.exc_info()[1]
                raise
            return response(environ, start_response)
        finally:
            if self.should_ignore_error(error):
                error = None
            ctx.auto_pop(error)