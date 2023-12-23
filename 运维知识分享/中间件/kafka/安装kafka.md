# 安装 kafka 2.4, 基于 zookeeper

## 环境配置
### 服务器列表
```shell
echo '10.0.2.14 ops-lo-vm-zookeeper-1 ops-lo-vm-zookeeper-1.ops.pangfaheng.com
10.0.2.15 ops-lo-vm-zookeeper-2 ops-lo-vm-zookeeper-2.ops.pangfaheng.com
10.0.2.16 ops-lo-vm-zookeeper-3 ops-lo-vm-zookeeper-3.ops.pangfaheng.com
10.0.2.14 ops-lo-vm-kafka24-1   ops-lo-vm-kafka24-1.ops.pangfaheng.com
10.0.2.15 ops-lo-vm-kafka24-2   ops-lo-vm-kafka24-2.ops.pangfaheng.com
10.0.2.16 ops-lo-vm-kafka24-3   ops-lo-vm-kafka24-3.ops.pangfaheng.com' | sudo tee -a /etc/hosts
cat /etc/hosts
```
### 下载软件
```shell
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.5.7/apache-zookeeper-3.5.7-bin.tar.gz
wget https://archive.apache.org/dist/kafka/2.4.1/kafka_2.12-2.4.1.tgz
java = java 1.8
```

## 安装java

```shell
sudo mkdir -p /opt/module
sudo tar -zxvf ~/jdk-8u111-linux-x64.tar.gz -C /opt/module

sudo ls -rlt /opt/module/jdk1.8.0_111

echo 'export JAVA_HOME=/opt/module/jdk1.8.0_111
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib' | sudo tee -a /etc/profile
source /etc/profile
java -version

```

## 安装zookeeper

### 解压
```shell
sudo tar -zxvf ~/apache-zookeeper-3.5.7-bin.tar.gz -C /opt/module
sudo mv /opt/module/apache-zookeeper-3.5.7-bin /opt/module/zookeeper
sudo cp /opt/module/zookeeper/conf/zoo_sample.cfg /opt/module/zookeeper/conf/zoo.cfg
```
### 修改配置文件

```shell
echo 'maxClientCnxns=60
tickTime=2000
initLimit=5
syncLimit=2
dataDir=/data/zookeeper/data
dataLogDir=/data/zookeeper/datalog
clientPort=2181
server.1001=ops-lo-vm-zookeeper-1:2888:3888
server.1002=ops-lo-vm-zookeeper-2:2888:3888
server.1003=ops-lo-vm-zookeeper-3:2888:3888'| sudo tee -a /opt/module/zookeeper/conf/zoo.cfg

sudo mkdir -p /data/zookeeper/{data,datalog,ssl}

# 分别在三台机器上创建myid文件
echo '1001' | sudo tee -a /data/zookeeper/data/myid
echo '1002' | sudo tee -a /data/zookeeper/data/myid
echo '1003' | sudo tee -a /data/zookeeper/data/myid

echo '[Unit]
Description=Zookeeper
Documentation=http://zookeeper.apache.org
Requires=network.target
After=network.target

[Service]
Type=forking
WorkingDirectory=/opt/module/zookeeper
User=root
Environment="JAVA_HOME=/opt/module/jdk1.8.0_111"
ExecStart=/opt/module/zookeeper/bin/zkServer.sh start /opt/module/zookeeper/conf/zoo.cfg
ExecStop=/opt/module/zookeeper/bin/zkServer.sh stop /opt/module/zookeeper/conf/zoo.cfg
ExecReload=/opt/module/zookeeper/bin/zkServer.sh restart /opt/module/zookeeper/conf/zoo.cfg
TimeoutSec=30
Restart=on-failure

[Install]
WantedBy=default.target' | sudo tee -a /usr/lib/systemd/system/zookeeper.service
```

### 启动

```shell
sudo systemctl enable zookeeper --now

sudo systemctl status zookeeper

sudo systemctl stop zookeeper

```

### 测试
```shell
# set java_home
sudo sed -i '77i JAVA_HOME=/opt/module/jdk1.8.0_111' /opt/module/zookeeper/bin/zkEnv.sh
# ops-lo-vm-zookeeper-1
sudo /opt/module/zookeeper/bin/zkCli.sh -server ops-lo-vm-zookeeper-1:2181
create /test
# ops-lo-vm-zookeeper-2, ops-lo-vm-zookeeper-3
sudo /opt/module/zookeeper/bin/zkCli.sh -server 127.0.0.1:2181
ls /
```

## 安装kafka

### 解压
```shell
sudo tar -zxvf ~/kafka_2.12-2.4.1.tgz -C /opt/module

sudo mv /opt/module/kafka_2.12-2.4.1 /opt/module/kafka

ls -rlt /opt/module

```

### 修改配置文件

```shell
echo 'broker.id=1
listeners=PLAINTEXT://ops-lo-vm-zookeeper-1:9092
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
log.dirs=/data/kafka/logs
num.partitions=3
num.recovery.threads.per.data.dir=1
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1
log.retention.hours=168
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000
zookeeper.connect=ops-lo-vm-zookeeper-1:2181,ops-lo-vm-zookeeper-2:2181,ops-lo-vm-zookeeper-3:2181
zookeeper.connection.timeout.ms=6000
group.initial.rebalance.delay.ms=0' | sudo tee -a /opt/module/kafka/config/server.properties

```

### 启动

### 测试