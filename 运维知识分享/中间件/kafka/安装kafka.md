# 安装 kafka 2.4, 基于 zookeeper

## 环境配置
### 服务器列表
```shell
cat /etc/hosts
10.0.2.14 ops-lo-vm-zookeeper-1 ops-lo-vm-zookeeper-1.ops.pangfaheng.com
10.0.2.15 ops-lo-vm-zookeeper-2 ops-lo-vm-zookeeper-2.ops.pangfaheng.com
10.0.2.16 ops-lo-vm-zookeeper-3 ops-lo-vm-zookeeper-3.ops.pangfaheng.com
10.0.2.14 ops-lo-vm-kafka24-1   ops-lo-vm-kafka24-1.ops.pangfaheng.com
10.0.2.15 ops-lo-vm-kafka24-2   ops-lo-vm-kafka24-2.ops.pangfaheng.com
10.0.2.16 ops-lo-vm-kafka24-3   ops-lo-vm-kafka24-3.ops.pangfaheng.com
```
### 下载软件
```shell
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.5.7/apache-zookeeper-3.5.7-bin.tar.gz
wget https://archive.apache.org/dist/kafka/2.4.1/kafka_2.12-2.4.1.tgz

java = java 1.8
```

## 安装java

## 安装zookeeper
### 下载并解压
```shell
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.5.7/apache-zookeeper-3.5.7-bin.tar.gz

tar -zxvf /public_data/source/apache-zookeeper-3.5.7-bin.tar.gz -C /opt/module

cd /opt/module/apache-zookeeper-3.5.7-bin/
cp /opt/module/apache-zookeeper-3.5.7-bin/conf/zoo_sample.cfg /opt/module/apache-zookeeper-3.5.7-bin/conf/zoo.cfg

```
### 修改配置文件

### 启动

## 安装kafka