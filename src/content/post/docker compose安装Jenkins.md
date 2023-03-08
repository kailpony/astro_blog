## **通过docker-compoes来部署容器**

1. 创建 `docker_jenkins_compose`文件夹

2. docker_jenkins_compose 目录下创建 `docker-compose.yml` 文件

3. 编写`nano docker-compose.yml`

   ```shell
   # docker-compose.yml
   
   version: '3'
   services:                                      # 集合
     docker_jenkins:
       user: root                                 # 为了避免一些权限问题 在这我使用了root
       restart: always                            # 重启方式
       image: jenkins/jenkins:lts                 # 指定服务所使用的镜像 在这里我选择了 LTS (长期支持)
       container_name: jenkins                    # 容器名称
       ports:                                     # 对外暴露的端口定义
         - 8082:8080                              # 访问Jenkins服务端口
         - 50000:50000
       volumes:                                   # 卷挂载路径
         - /var/vol_dockers/jenkins_home/:/var/jenkins_home  # 这是我们一开始创建的目录挂载到容器内的jenkins_home目录
         - /var/run/docker.sock:/var/run/docker.sock
         - /usr/bin/docker:/usr/bin/docker                # 这是为了我们可以在容器内使用docker命令
         - /usr/local/bin/docker-compose:/usr/local/bin/docker-compose
   ```

4.创建启停脚本文件： restart , start, stop, 并修改文件权限

- 创建文件

  ```shell
  # restart
  docker compose restart
  ```

  ```shell
  # start
  docker compose up -d
  ```

  ```shell
  # stop
  docker compose stop
  ```

- 修改文件权限

  ```shell
  chmod 777 restart start stop // 可读可写可执行
  ```

### 启动容器

```shell
./start
```

### 查看容器日志

1. `docker logs 'containerId'`

   jietu

   生成Jenkins登录初始密码，一会登录的时候要用

2. `docker logs -f jenkins 查看实时日志`

   ```shell
   查看Jenkins状态
   ps -ef | grep jenkins
   ```

### 宿主机访问Jenkins主目录

```shell
# 容器内/var/jenkins_home挂载目录

/var/vol_dockers/jenkins_home
```

### 登录Jenkins

浏览器打开 `http://主机ip:8082`, 端口就是yaml文件种配置的映射端口。