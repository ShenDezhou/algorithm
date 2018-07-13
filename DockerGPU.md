#docker使用gpu的方法
1. 确定nvidia的docker插件已启动。启动脚本：

nvidia-docker-plugin -d /usr/lib/nvidia-docker/volumes -s  /var/lib/nvidia-docker 1> /tmp/nvidia-docker-plugin.std 2>/tmp/nvidia-docker-plugin.err &

2. 使用nvidia-docker启动容器

nvidia-docker run rm nvidia/cuda nvidia-smi

升级驱动及安装nvidia-docker方法
#安装dkms
wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -ivh epel-release-latest-7.noarch.rpm
yum repolist

#检查是否安装成功
yum enablerepo=epel install dkms

#更新CUDA，驱动文件可以从官网下载

#https://developer.nvidia.com/compute/cuda/8.0/prod/local_installers/cuda_8.0.44_linux-run

sh cuda_8.0.44_linux.run

#安装cudnn，安装文件需注册nvidia账号才能获得

tar -xzvf cudnn-6.5-linux-R1.tgz
cp cuda/lib64/* /usr/local/cuda/lib64/
cp cuda/include/cudnn.h /usr/local/cuda/include/

#设置环境变量
#/root/.bashrc
export PATH=/usr/local/cuda-8.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH
#export CUDA_HOME=/usr/local/cuda

#安装nvidia-docker插件
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.0-rc.3/nvidia-docker-1.0.0.rc.3-1.x86_64.rpm
rpm -i /tmp/nvidia-docker*.rpm && rm /tmp/nvidia-docker*.rpm
#systemctl start nvidia-docker

#启动插件

mkdir /usr/lib/nvidia-docker/
mkdir /usr/lib/nvidia-docker/volumes
#volumes需要和docker运行目录一致
#NVIDIA driver installation needs to live on the same partition as the volume directory of the Docker plugin. nvidia-docker-plugin internally needs to create hard links to some driver files. Because of this, you need the NVIDIA driver (usually found under /usr) to be on the same partition as the Docker plugin volume directory (/var by default).  Possible workarounds includes installing your NVIDIA driver at a different location (see advanced-options of the installer) or changing your plugin volume directory (see nvidia-docker-plugin -d) 

nvidia-docker-plugin -d /usr/lib/nvidia-docker/volumes -s  /var/lib/nvidia-docker 1> /tmp/nvidia-docker-plugin.std 2>/tmp/nvidia-docker-plugin.err &

#启动docker
systemctl restart docker

# Test nvidia-smi
nvidia-docker run rm nvidia/cuda nvidia-smi

#如果import tensorflow时报各种错误，可能是驱动没装好，或者nvidia-docker插件有问题。
