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