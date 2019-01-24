# 使用AWS配置环境  

## lanuch instance  
[referce to](https://zhuanlan.zhihu.com/p/25066187)  
# 从本地连接到服务器  
我本地虽然装的双系统，但平时科研的软件是在windows下，切换来切换去太麻烦，最后，也在windows下  
安装了常用环境，现在也是在windows下连接服务器，所以需要下载git,使用Git bash 连接服务器，我把  
秘钥统一放到`C:\Users\xxx\.ssh`下，为了方便，打开git bash，首先cd 到该目录下，但是直接使用  
命令`cd C:\Users\Holly\.ssh`会提示 'no such file or directory`,google 找到[here](https://askubuntu.com/questions/466549/bash-home-user-ssh-authorized-keys-no-such-file-or-directory)  
我并没有完全按照这个操作，我只是首先更改了.ssh权限，然后使用命令`cd ~/.ssh`就进入了.ssh目录  
# 安装cuda  
* update env  
`sudo apt-get update && sudo apt-get install -y build-essential git libgfortran3`  
[get the address of last version of cuda](https://developer.nvidia.com/cuda-90-download-archive)  
选择如下图  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/cuda.png)  
使用`wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run` 下载下来，然后使用`sudo sh cuda_9.0.176_384.81_linux-run`安装  
如下图  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/cudaInstall.png)  
安装时会进入文档，点击`Ctrl + C`就可以跳出文档，但是需要回答一下几个问题，问题及回答如下图  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/q.png)  
```
Do you accept the previously read EULA?
accept/decline/quit: accept

You are attempting to install on an unsupported configuration. Do you wish to continue?
(y)es/(n)o [ default is no ]: y

Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81?
(y)es/(n)o/(q)uit: y

Do you want to install the OpenGL libraries?
(y)es/(n)o/(q)uit [ default is yes ]: y

Do you want to run nvidia-xconfig?
This will update the system X configuration file so that the NVIDIA X driver
is used. The pre-existing X configuration file will be backed up.
This option should not be used on systems that require a custom
X configuration, such as systems with multiple GPU vendors.
(y)es/(n)o/(q)uit [ default is no ]: n

Install the CUDA 9.0 Toolkit?
(y)es/(n)o/(q)uit: y

Enter Toolkit Location
 [ default is /usr/local/cuda-9.0 ]:

Do you want to install a symbolic link at /usr/local/cuda?
(y)es/(n)o/(q)uit: y

Install the CUDA 9.0 Samples?
(y)es/(n)o/(q)uit: n

Not enough space on parition mounted at /.
Need 5878290432 bytes.

Disk space check has failed. Installation cannot continue.

Signal caught, cleaning up
```  
但是当我使用`nvidai-smi`查看GPU时，却给出如下提示  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/check_cudaInfo.png)  
在安装过程中有错误信息，还是没有成功，毕竟我的money在流淌，就不再尝试了，按照我的经验，多  
尝试几次总是能成功的，我以前配置很多环境，很少超过三次的，我直接终止了该实例，使用社区版的  
实例，一般社区版的都配置了好了。  
我又重新安装了一遍，成功了，如下图  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/gpuInfo.png)  
使用`echo "export LD_LIBRARY_PATH=\${LD_LIBRARY_PATH}:/usr/local/cuda-9.0/lib64" >> ~/.bashrc`  
命令将 CUDA加入到库的路径中，以方便其他库找到它。如果你使用其他版本或其他路径，需要修改以下命令中的字符串“/usr/local/cuda-9.0”   
## 选择社区版的instance  
 ![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/communityAMI.png)  
 这个基本不需要配置了，我参考了[这里](https://www.pyimagesearch.com/2017/09/20/pre-configured-amazon-aws-deep-learning-ami-with-python/)  
 只是，我首先使用`nvidia-smi`查看了GPU信息，显示了GPU信息，是有的，我还是reboot了一下服务器,  
 再查看没有GPU信息了，也没有installers文件夹，我不想再安装一遍，就又reboot一次，这次有GPU信息  
 了，也有一些常用包了，接着就运行我的faster-rcnn 了  
# 运行 faster-rcnn  
我使用的是tensorflow版本的[tf-faster-rcnn](https://github.com/endernewton/tf-faster-rcnn)  
我在上述两种配置的环境中都运行了该faster-rcnn，只是，第一个没有训练完就停了，也没截图记录  
过程，相对而言，使用社区版的也顺利很多，按照readme文档中的installation 即可，只是，注意两点  
1. update setup.py中的gpu 信息，根据自己GPU型号,更改architecture   
2. 使用它的demo时，将fetch_faster_rcnn_models.sh 文件中的url为下方提供的 Google drive的链接  
原链接下载不下来，一直在给我retrying,一开始，我以为网速原因下载失败，重新下载，后来，我注意到  
下方的note，才改的。。。我燃烧的money啊  
3.  改了链接后，又出现了解压错误，我又打开该文件，发现了解压命令部不对，Ubuntu下解压 .tgz文件  
的命令是`tar -xvf filename.tgz`，也可以进入data下手动解压  
过程还是有些小问题的，虽然是社区版，显示安装了tensorflow，还是提示我no module named tf，  
只好再装，**Note:** 提示no module named cv2时要使用`pip install opencv-python`  
4. `demo.py` 运行成功后如下图所示
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/res_demo.png)  
但是，当我将demo文件夹传输到本地查看时，却没有显示识别框，查看了`demo.py`文件，并结合  
[这篇博客](https://blog.csdn.net/zzyincsdn/article/details/84780511)明白了，`demo.py`文件只是将结果显示，而我用服务器跑的，terminal 是无法show图片的，  
所以采用将结果图片保存下来，修改`demo.py`文件如下：  
1. 在文件头部分导入如下 
```
# modify 1
import matplotlib
matplotlib.use("Agg")
```  
2.在`vis_detections()`函数前添加全局变量`fig_count_i = 0`，在该函数的`plt.draw()`后增加如下代码  
```
# modify 2
global fig_count_i
plt.savefig("%s.png"%fig_count_i)
fig_count_i = fig_count_i + 1
```   
最后使用fileziller将结果图片下载到本地  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/res.png)  、
其中两张结果如下  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/demo_res/0.png)  
![avatar](https://github.com/Emma-uestc/pictures-note/blob/master/tf-faster-rcnn-gpu/demo_res/1.png)  
接下来，该研究如何使用这个训练自己的数据了  
Lucy ！！！