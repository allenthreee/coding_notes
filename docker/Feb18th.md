### image文件

**docker把应用程序及其依赖打包在image文件里面**，只有通过这个文件才能生成docker容器

下面二者关系类似

image——》container

class   ——》object

| image     | class  |
| --------- | ------ |
| container | object |

- doker启动，重启，关闭

| 操作           | 命令                         |
| :------------- | ---------------------------- |
| 启动           | systemctl start docker       |
| 守护进程启动   | sudo systemctl daemon-reload |
| 重启docker     | systemctl restart docker     |
| 重启docker服务 | sudo service docker restart  |
| 关闭docker     | service docker stop          |
| 关闭docker     | systemctl stop docker        |



### Dockerfile文件

dockerfile文件用于配置image，docker根据该文件生成二进制的image文件



docker status

1. created（已创建）
2. restarting（重启中）
3. running / Up（运行中）
4. removing（迁移中）
5. paused（暂停）
6. exited（停止）
7. dead（死亡）



~~~c++
std::cout<<"this is a string"<<std::endl;
~~~

通过docker image创建 container，创建好之后，container就像一台独立的机器一样，可以在container内安装各种包，以满足代码的运行需求。

docker cp可以实现本机与container之间的文件拷贝

docker run xx——》从docker image xxx创建一个 container并运行

~~~
docker run -it ubuntu /bin/bash
~~~

- 参数说明
  - -i：交互式操作
  - -t：终端
  - ubuntu：ubuntu镜像（image）
  - /bin/bash：放在镜像名后的是命令，这里我们希望有一个交互式shell，因此用的是 /bin/bash



#### 查看所有容器：

~~~
docker ps -a
~~~

#### 查看当前活动的容器：

~~~
docker ps
~~~



python中：

~~~python
print(f"t: {t}")
~~~

f表示格式化字符串，加f后可以在字符串里面使用用花括号括起来的变量和表达式，如果字符串里面没有表达式，则前面加不加 f 都一样



![TheGodFather](https://puui.qpic.cn/vcover_hz_pic/0/c2f26xjegzuyi16t1444742226.jpg/0)











