### 【step by step】超简单Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1

#### 0 安装显卡驱动

（1）卸载原先所有驱动

```xml
#for case1: original driver installed by apt-get:
sudo apt-get remove --purge nvidia*
 
#for case2: original driver installed by runfile:
sudo chmod +x *.run
sudo ./NVIDIA-Linux-x86_64-384.59.run --uninstall
```

（2）禁用nouveau

`sudo gedit /etc/modprobe.d/blacklist.conf`

在最后一行添加blacklist nouveau

（3）执行命令

```xml
sudo update-initramfs -u
电脑重启之后执行
lsmod | grep nouveau  #没有输出，即说明安装成功
```

（4）进入命令行界面

Ctrl-Alt+F1,之后输入用户名和密码登录即可。

（5）给驱动run文件赋予执行权限

```xml
cd Downloads
sudo chmod a+x NVIDIA-Linux-x86_64-375.20.run
```

（6）关闭图形环境

`sudo service lightdm stop`

（7）安装

```python
### –no-opengl-files 只安装驱动文件，不安装OpenGL文件。这个参数最重要
### –no-x-check 安装驱动时不检查X服务
### –no-nouveau-check 安装驱动时不检查nouveau
### 后面两个参数可不加。
sudo ./NVIDIA-Linux-x86_64-375.20.run –no-opengl-files
### 后面就一路Accept就可以~报错The distribution-provided pre-install script failed!不必理### 会，继续安装。最重要的一步，安装程序问你是否使用nv的xconfig文件，这里一点要选yes，否则在启动x-### window时不会使用nv驱动。
```

（8）重启 X-window 服务，并重启电脑

```python
sudo service lightdm start
sudo reboot
```

（9）查看显卡是否装好

`nvidia-smi`

#### 1 准备

（1）下载CUDA9.0

从官网下载 <https://developer.nvidia.com/cuda-toolkit-archive> 

![1562722340801](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562722340801.png)

点进去之后下载下面的五个文件

![1562722381952](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562722381952.png)

（2）下载cuDNN7.4.1

![1562724702381](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562724702381.png)

（3）最终一共下载6个文件

![1562724860613](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562724860613.png)

#### 2 安装CUDA

（1）输入 `nvidia-smi`若不显示下面的驱动基本信息或者驱动版本小于384，则需要先安装驱动。

![1562724476751](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562724476751.png)



打开【软件与更新】选择【附加驱动】如下图所示勾选即可成功安装驱动，注意安装完成之后需要重启电脑。

![1562723018457](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562723018457.png)

（2）安装cuda9.0

`chmod +x ./cuda_9.0.176_384.81_linux.run`

`sudo ./cuda_9.0.176_384.81_linux.run`

![1562723129279](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562723129279.png)

`ctrl + c` 

然后按照下图内容键入accept,yes或者no

![1562723252993](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562723252993.png)

接下来把补丁都安装上去，安装过程与上面相同。

```python
sudo ./cuda_9.0.176.1_linux.run
sudo ./cuda_9.0.176.2_linux.run
sudo ./cuda_9.0.176.3_linux.run
sudo ./cuda_9.0.176.4_linux.run
```

配置环境变量

`sudo vim ~/.bashrc` 

在文件底部输入下面两句话

```python
export  PATH=/usr/local/cuda-9.0/bin:$PATH
export  LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64$LD_LIBRARY_PATH
```

`source ~/.bashrc`

（3）验证是否安装成功

```python
cd ~/NVIDIA_CUDA-9.0_Samples/1_Utilities/deviceQuery 
sudo make 
./deviceQuery
```

![1562724419479](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562724419479.png)

#### 3 安装cuDNN7.4.1

（1）解压cudnn7.4.1进入到include目录执行一下操作

`sudo cp cudnn.h /usr/local/cuda/include` 

（2）再进入lib64目录下进行以下操作

```python
sudo cp lib* /usr/local/cuda/lib64
cd /usr/local/cuda/lib64
sudo rm -rf libcudnn.so libcudnn.so.7
sudo ln -s libcudnn.so.7.4.1 libcudnn.so.7#注意这边7.4.1 要改成自己的版本 服务器改成7.6.0
sudo ln -s libcudnn.so.7 libcudnn.so
```

（3）执行以下命令验证是否安装成功

`nvcc -V`

![1562724210768](E:\notebook\自监督学习\Ubuntu16.04 安装CUDA9.0和cuDNN7.4.1.assets\1562724210768.png)



# #####

### 1 更换阿里云镜像

(1) `sudo vi /etc/apt/sources.list`

(2) 开头加上

```python
deb http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse
```

(3) `sudo apt-get update`

### 2 配置python镜像源

（1）在用户目录下(/home/cheertt)创建`.pip`文件夹，并创建`pip.conf`文件；

（2）在pip.conf下输入:

```python
[global]
trusted-host = mirrors.aliyun.com
index-url = https://mirrors.aliyun.com/pypi/simple
```

### 3 安装python虚拟环境

(1) `sudo apt-get install python3-pip`  

(2) `sudo pip3 install virtualenv`

(3) `sudo pip3 install virtualenvwrapper`

(4) `mkdir ~/.virtualenvs`

(5) `sudo vim ~/.bashrc`

(6) `在bashrc的末尾增加如下内容`

```python
export WORKON_HOME=$HOME/.virtualenvs  # 所有虚拟环境存储的目录
VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3  # 一定要加上这个代码，这个代码把我搞哭了
source /usr/local/bin/virtualenvwrapper.sh
```

(7) `source ~/.bashrc`

### 4 使用

```python
mkvirtualenv -p /usr/bin/python3 xh  # 创建虚拟环境
workon xh  # 进入虚拟环境
deactivate # 退出虚拟环境
rmvirtualenv xh # 删除虚拟环境
```



