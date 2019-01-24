# 写在前面的话  
[这里]() 记录了在aws上gpu配置tf-faster-rcnn 及跑demo的过程，由于教研室电脑和我个人的笔记本都  
没有gpu,不能总是使用AWS跑，另外，还想调试代码加深对算法的理解，就研究了下如何在cpu下跑。  
流程与gpu下一致，只需要修改一些文件中使用gpu 的部分配置  
# 环境  
* Ubuntu 16.04  
* Anaconda3  
我的anacinda3 默认的是Python3.6,安装Anaconda 使用`conda install python==3.5`(**Note:**看一些  
博客使用`conda install python3.5`，我最初也是如此，不过总是失败，找不到源)  
* tensorflow1.2  
安装anaconda 和python3.5后，  
'conda create -n tensorflow==3.5'  
`source activate tensorflow`  
`pip install tensorflow==1.2`  
opencv ,easydict,Cython,matplotlib等，我并没有注意版本，在tensorflow环境下使用pip 安装的  
`pip install opencv-python`  
`pip install easydict` 
`pip install pillow`  
`pip install matplotlib`  
有些是在运行时提示`no module named xxxx`后安装的  
# 配置流程  
1. git clone https://github.com/endernewton/tf-faster-rcnn.git  将项目clone到本地  
2. 需改一些配置  
* /lib/setup.py 文件  
注释掉有关使用gpu的部分，如下：  
！[avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/modify1.png)  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/modify2.png)  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/modify3.png)  
* lib/model/config.py文件  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/modify4.png)  
* /lib/model/nms_wrapper.py 文件  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/modify5.png)  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/modify6.png)  
3. 链接 Cython  
在lib 目录中执行如下  
```
make clean
make
```  
回到主目录tf-faster-rcnn  
4. 安装Python COCO API  
```
cd data
git clone https://github.com/pdollar/coco.git
cd coco/PythonAPI
make
```  
### 下载预训练好的数据  
该项目中的.sh 中的url是无法下载下来的，可以改为提供的另一个，在gpu的note中，我已经提到过，  
或者，从我放到百度网盘[链接：](https://pan.baidu.com/s/1Rq0NeO_L2B3yg6O_-Vu40A) 提取码：jtmu  
下载后放到data目录中解压  
### 创建一个使用预训练数据的目录和软链接  
**Note:** 这里需要在主目录tf-faster-rcnn 中执行  
```
NET=res101
TRAIN_IMDB=voc_2007_trainval+voc_2012_trainval
mkdir -p output/${NET}/${TRAIN_IMDB}
cd output/${NET}/${TRAIN_IMDB}
ln -s ../../../data/voc_2007_trainval+voc_2012_trainval ./default
```  
### 运行demo  
主目录下执行  
```
GPU_ID=0
CUDA_VISIBLE_DEVICES=${GPU_ID} ./tools/demo.py
```  
我的结果如下，直接显示在桌面了，需要保存下来，关掉这些图片，终端才会回到命令行，当然，也可以  
像我使用gpu 训练时修改文件将结果保存下来  
  ![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/imdiRes.png)  
  其中，两张结果图片，如下  
  ![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/Figure_3.png)  
  ![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-cpu-record/Figure_4.png)  
  全部demo 结果可以看[这里](https://github.com/Emma-uestc/pictures-note/tree/master/tf-faster-rcnn-cpu-record)  
  