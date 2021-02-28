## 创建container

~~~
docker run -it --gpus all --name test_gpu xxx
~~~

~~~
sudo docker run -it --gpus all -v /home/ubuntu/mmseg:/home --name mmseg_tutorial --network host 6b5de13ed221 /bin/bash
~~~

~~~
sudo docker run -it --gpus all --shm-size <shared_memory_size> --name <ContainerName> -v <HostVolumn>:<ContainerVolumn> --network host <ImageName> /bin/bash
~~~

# 2021.2.26创建了一个叫 mmseg_tutorial的container在服务器上

~~~
apt-get update
~~~



~~~
apt-get install software-properties-common
~~~

由第三方维护的PPA软件源来方便的安装所需要的Python版本

~~~
add-apt-repository ppa:deadsnakes/ppa
~~~

安装上面两个之后可以安装python，否则报错

~~~
apt-get update
apt-get install python3.6
~~~



修改python3.6为优先使用 优先级为1

~~~
update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1
~~~



安装python3.6-dev

~~~
apt-get install python3.6-dev
~~~

安装pip

~~~
apt-get install python3-pip
~~~



~~~
pip3 install numpy==1.10.1 -i https://pypi.tuna.tsinghua.edu.cn/simple
~~~

下载pytorch

~~~
pip3 install torch==1.5.0 torchvision==0.6.0 -i https://pypi.tuna.tsinghua.edu.cn/simple
~~~

