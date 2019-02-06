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
  ## Train your own model  
  * 这里主要记录使用res101模型训练，由于训练过程出现很多error,基本都是`FileNotFoundError`:  
  'file or directory does not exist',每次根据error修改文件，所以为了减少每次训练时间修改以下文件  
  1.`tf-faster-rcnn/experiments/scripts/train_faster_rcnn.sh`中迭代次数  
```
 case ${DATASET} in
  pascal_voc)
    TRAIN_IMDB="voc_2007_trainval"
    TEST_IMDB="voc_2007_test"
    STEPSIZE="[50000]"
    ITERS=70000
    ANCHORS="[8,16,32]"
    RATIOS="[0.5,1,2]"
    ;;
```  
 将`ITERS = 70000`改为300，不过，后来，我直接改成了60(我使用的CPU，训练太慢)  
 2. `tf-faster-rcnn/experiments/scripts/test_faster_rcnn.sh`中相同的改变  
 **Note:** 我使用的是VOC 数据集，所以改动的是这里，使用哪个数据集更改哪个数据集中设置  
 3.下载数据集`VOCdevkit`放到./data下 使用 `mv VOCdevkit VOCdevkit2007`并只保留文件  
 `data/VOCdevkit2007/VOC2007/ImageSets/Main/test.txt`文件的前200行(这个随意，我也不是精准的200行  
 就是为了减少时间)  
 * 训练  
 删除之前主目录中产生的`output`和./data中`cache`，重新执行以下命令  
```
 NET=res101
TRAIN_IMDB=voc_2007_trainval+voc_2012_trainval
mkdir -p output/${NET}/${TRAIN_IMDB}
cd output/${NET}/${TRAIN_IMDB}
ln -s ../../../data/voc_2007_trainval+voc_2012_trainval ./default
cd ../../..
```  
```
mkdir -p data/imagenet_weights
cd data/imagenet_weights
wget -v http://download.tensorflow.org/models/resnet_v1_101_2016_08_28.tar.gz
tar -xzvf resnet_v1_101_2016_08_28.tar.gz
mv resnet_v1_101.ckpt res101.ckpt
cd ../..
```  
`./experiments/scripts/train_faster_rcnn.sh 0 pascal_voc res101`  
我使用的是res101模型和`pascal_voc`数据集，这里GPU_ID设为0，因为是使用的CPU  
出现过以下典型的错误，出现什么添加什么  
1.比如：FileNotFoundError 的/home/user/tf-faster-rcnn/data/VOCdevkit2007/VOC2007.../main..xxx.txt  
不存在，/home/user/tf-faster-rcnn/data/VOCdevkit2007/results/VOC2007.../main..xxx不存在，由于下载下来  
数据集的目录结构是这样的，VOCdevkit2007/results/VOC.../...所以，我只好在VOCdevkit2007目录下手动  
创建一个results目录，并将目录VOC2007及其子目录复制到results中  
2.如图  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/error_examples/error_ex1.png)  
这个错误需要修改`tf-faster-rcnn/lib/datasets/voc_eval.py`文件的121行  
```
    with open(cachefile, 'w') as f:
      pickle.dump(recs, f)
```  
修改为  
```
    with open(cachefile, 'wb') as f:
      pickle.dump(recs, f)
```  
这样修改完，又出现了如下错误  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/error_examples/error_ex2.png)  
google的结果是将该文件的105行`cachefile = os.path.join(cachedir, '%s_annots.pkl' % imagesetfile)`  
修改为`cachefile = os.path.join(cachedir, ('%s_annots.pkl' % imagesetfile))`，该错误仍然存在，  
又google到在项目tf-faster-rcnn 项目的issue中的上述错误的问题下的回答中的修改是将105行改为  
`cachefile = os.path.join(cachedir, '%s_annots.pkl' % imagesetfile.split("/")[-1].split(".")[0])`  
改过后训练成功，结果如下，由于迭代次数设置的非常少，所以准确率是很低的  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/error_examples/res1.png)  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/error_examples/res2.png)  
**关于错误的记录我只截图了两个，这部分日记也是我回忆的，所以，关于解决方案的博客链接我没有再找，  
也没有放进来。
