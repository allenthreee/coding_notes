# Docker101
### Docker Basic Usage
#### 1. Run Docker Containers from Docker Image
Basicly, to run a docker container from a docker image, you can use following command:
```
mkdir <your_workdir>

**Notice** nvidia-docker **no longer exists**, use the following command with arguments **--gpus** instead. 
​```bash

sudo docker run -it --gpus all --shm-size <shared_memory_size> --name <ContainerName> -v <HostVolumn>:<ContainerVolumn> --network host <ImageName> /bin/bash
```

* -it 
    * interactive mode. without this command, the docker will just execute specific command and then stop.
* \<ContainerName\> 
    * Name of your Docker Container. You can start, stop, attach, exec your container with this name.
* -v \<HostVolumn\>:\<ContainerVolumn\>
    * Volumn Map between Host Machine and Docker Container
    * **-v /data_shared/Docker/pyun_voxelnet:/usr/app** means map the dir **/usr/app** of Docker Container to **/data_shared/Docker/pyun_voxelnet** of Host Machine.
* -p \<HostPort\>:\<DockerPort\>
    * Port Map between Host Port and Docker Port
    * **-p 8001:8888** means map the port **8888** of Docker Container to **8001** of Host Machine
* \<ImageName\>
    * Name of Docker Image you want to use to launch the Docker Container
    * You can use official Docker Images from https://hub.docker.com/ (like tensorflow/tensorflow nvidia/cuda...)
    * Or you can build you Docker Images yourself (For how to costomize docker image, please refer the next section)
    * 如使用<ImageName>报错——》can't find ImageName:latest locally，则使用ImageID可以避免这个问题
* /bin/bash
    * once it start the container, it will run /bin/bash automatically. That means it will launch the terminator for interactivation.



#### 2. Customize Docker Image
Docker images really help development and maintenance. 
Once you create the Docker image, you will never ever need to configurate the same environment. 
In addition, you can share your Docker images with others, and that guy will not get trouble in environment configuration. It does save time.

Docker image is build from DockerFile. 
DockerFile is easy to code and just like the commands we always use.

Here is the documents about DockerFile. You can learn how to use it to help your own project.

[DockerFile Document](https://docs.docker.com/engine/reference/builder/)

**Note:**
You can create your own docker file in the directory **~/DockerFiles/<image_name>**, 
and build it with following command.
```sh
sudo docker build . -t <image_name>
```

#### 3. Other Docker Commands
All Docker Commands should be executed on Host Machine.
* sudo docker ps -a
    * list all existed Docker Containers
* sudo docker rm -f \<ContainerName\>
    * remove the Docker Container
* sudo docker rmi \<ImageName\>
    * remove the Docker Image
* sudo docker attach \<ContainerName\>
    * attach the Docker Container
* sudo docker exec -it \<ContainerName\> /bin/bash
    * turn on a new terminator for this Docker Container
* sudo docker start \<ContainerName\>
    * start the Docker Container
* sudo docker stop
    * stop the Docker Container
