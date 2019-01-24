* 最近在阅读 faster r-cnn源码，发现对tensorflow某些API还是不是很熟悉，记录下方便复习  
* 解析主要参考知乎上一篇文章，[传送门](https://zhuanlan.zhihu.com/p/32230004)  
* [resnet_utils.conv2d_same](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/slim/python/slim/nets/resnet_utils.py)  
* [slim.arg_scope()](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/framework/python/ops/arg_scope.py)  
这个函数的作用是给list_ops中的内容设置默认值。但是每个list_ops中的每个成员需要用@add_arg_scope修饰才行,需要如下两个步骤  
```
@slim.add_arg_scope() # 修饰目标函数
slim.arg_scope() # 为目标函数设置默认参数
```  
示例：首先用`@slim.add_arg_scope`修饰目标函数`fun1()`，然后利用`slim.arg_scope()` 为它设置  
默认参数  
```
import tensorflow as tf
slim =tf.contrib.slim
 
@slim.add_arg_scope
def fun1(a=1,b=2):
    return (a+b)
 
with slim.arg_scope([fun1],a=10):
    x=fun1(b=30)
    print(x)

```  
* @ 修饰符  
在python 中经常用到。Python 中使用`@`修饰符是放在函数定义上方，它将被修饰的函数作为参数，返回  
修饰后的同名函数。  
```
@func_1 #等价于fun_1(func_b)
def func_2():
```  
本质上跟直接调用被修饰的函数`func_2()`相同，但是，有时这样做还是用用的，例如在调用被修饰的函数前  
需要输出时间信息，我们可以在@后方函数中添加输出时间信息的语句，这样每次就只需要调用@后方函数  
```
def funs(fun,factor=20):
    x=fun()
    print(factor*x)
    
    
@funs     #等价funs(add(),fator=20)
def add(a=10,b=20):
    return(a+b)
```  
* `tf.py_func()` 拓展tensorflow 灵活性  
[tf.py_func()](https://blog.csdn.net/tiankongtiankong01/article/details/80568311)  
['another blog'](https://blog.csdn.net/tiankongtiankong01/article/details/80568311)  
* `tf.tile()`:张量扩展  
[tf.tile()](https://www.tensorflow.org/api_docs/python/tf/tile)  
* tf.squeeze()`:删除输入张量中维度为1的轴    
[tf.squeeze()](https://www.tensorflow.org/api_docs/python/tf/squeeze)  
* [tf.slice()](https://www.tensorflow.org/api_docs/python/tf/slice)  
['here are details blog'](https://blog.csdn.net/legend_hua/article/details/80329811)  
* ·arg_scope·：第一次看到的时候不理解，为什么要这么做，list很多可能用到的，就google了下，  
[这里](https://stackoverflow.com/questions/45226950/what-does-arg-scope-actually-do)示例  
* tf.meshgrid(): 从数组产生网格生成矩阵  
[tf.meshgrid()](https://www.tensorflow.org/api_docs/python/tf/meshgrid)  
* 在查`numpy.ascontiguousarray()`，看了官网这个函数的使用还是不知为什么要这样，接着找了找吗，在  
[Stack Overflow](https://stackoverflow.com/questions/26998223/what-is-the-difference-between-contiguous-and-non-contiguous-arrays)上看到一个回答解释了我的疑问  

