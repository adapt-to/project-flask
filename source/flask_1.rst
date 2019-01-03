=====================
初识Flask
=====================

Flask快速入门
=================
  ----利用虚拟环境进行Flask开发

1.Python虚拟环境的搭建
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

werkzeug源码 ::
    
    from werkzeug.wrappers import Request, Response

    @Request.application
    def hello(request):
        return Response('Hello World!')

    if __name__ == '__main__':
        from werkzeug.serving import run_simple
        run_simple('localhost', 4000, hello)
