# 物理架构云上部署 dock-compose.yml 文件编写与使用

## docker-compose
Compose 项目是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排。  
Compose 定位是 「定义和运行多个 Docker 容器的应用（Defining and running multi-container Docker applications）」，其前身是开源项目 Fig。

## Compose 中有两个重要的概念：
1. 服务 (service)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
2. 项目 (project)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

### 服务
在实践中，通常把组成整个系统的各个独立服务安排在不同的docker容器中，以实现独立的安全沙箱环境和快速部署能力。比如对于本项目来说，后台，前段，数据库系统，redis储存系统以及对应的测试环境系统，均由docker实现部署。  
实际上，最后的组织便是将各个独立子系统放入docker环境中，以一个个独立的docker实例运行在物理设备上，而在docker-compose的组织概念中，我们称之为服务\(Service\)。映射到项目上来说，后台\(Node.js服务器\)可以是一个服务，数据库系统\(Mysql Server\)可以是一个服务，除此之外还有文件系统\(SeaweedFS\)、缓存系统\(Redis\)等等

### 项目
项目的概念其实应该映射到实际整个项目上，也就是服务的组织。docker-compose通过docker-compose.yml作为一个项目来定义服务组织。

## docker-compose.yml文件的编写

docker-compose.yml的组织是层级化的，在services下定义各个命名的服务，compose会据此生成对应名称的docker实例。对于每个服务，其实可以看成是compose启动容器的配置，所以这里的配置项其实与docker的run命令基本上是成映射关系的，但是compose也提供了从Dockerfile.txt构建镜像的能力。  
本项目的docker-compose.yml如下:
```
version: "3"
services:
  qr-meals-se:
    image: qr-meals-se
    ports:
      - "443:443"
    networks:
      - qr-meals-net
    configs:
      - prod
  qr-meals-se-dev:
    image: qr-meals-se
    ports:
      - "444:443"
    networks:
      - qr-meals-net-dev
    configs:
      - dev

  qr-meals-db:
    image: mysql
    ports:
      - "3306:3306"
    volumes:
      - db-data:/var/lib/mysql/data
    networks:
      - qr-meals-net
  
  qr-meals-db-dev:
    image: mysql
    ports:
      - "6033:3306"
    networks:
      - qr-meals-net-dev

  qr-meals-redis:
    image: redis:alpine
    networks:
      - qr-meals-net
  qr-meals-redis-dev:
    image: redis:alpine
    networks:
      - qr-meals-net-dev

networks:
  qr-meals-net:

volumes:
  db-data:

configs:
  dev:
    file: ./dev.conf
  prod:
    file: ./prod.conf

```
