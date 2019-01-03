=====================
Python主流Web框架介绍
=====================

主流框架的介绍
=====================

1. Django : 重武器，内部包含了非常多的组件（例如：ORM、Form、ModelForm、缓存、Session、中间件、信号等等...）
#. Flask : 短小精悍，内部没有太多组件，第三方组件十分丰富，可定制性强（了解更多 `Flask扩展 <http://flask.pocoo.org/extensions/>`_）
#. Bottle : 比Flask还精简的一个web框架，但第三方组件库不及Flask。Flask名字的由来是调侃和致敬Bottle（两个语言的含义都是‘瓶子’），适合小型开发，大型企业不太适用
#. Tornado : 异步非阻塞框架（node.js），通过一个线程执行多任务

.. note::Django和Flask

   1.在开发小型程序时，Flask占据优势，因为Flask轻，在一些小型程序中可能只需要某一些组件即可，这时如果选用Django会显得很臃肿。

   2.在开发大型项目时，Flask和Django皆可，但Flask往往由于能够自主添加需要的扩展甚至是自己编写适合自己的扩展而显得比Django更加人性化，但
   也是由于这样的原因使得Flask在使用上比Django要难一些，Django由于自身就具备很多组件而不需要手动添加，所以在使用上会比Flask容易

