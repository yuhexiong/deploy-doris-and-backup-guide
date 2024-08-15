# One Frontend, One Backend And One Broker In One VM - Script

Document refer to [BoldBI](https://support.boldbi.com/kb/article/14891/install-and-configure-apache-doris-in-ubuntu-environment)  

## Overview

- Virtual Machine: Ubuntu v22.04.4
- Platform: JDK 11
- Database: Doris v2.0.12

## Switch To Root User
```
sudo su -
```

## Security Limits
```
vim /etc/security/limits.conf
```
add below
```
soft nofile 65536
hard nofile 65536
```

## Install
```
sudo apt -y update 
sudo swapoff -a

sudo apt install build-essential maven cmake byacc flex automake libtool-bin bison binutils-dev libiberty-dev zip unzip libncurses5-dev curl git ninja-build
```


### Java
```
sudo apt install default-jdk
sudo update-alternatives --config java
```

environment
```
vim /etc/environment
```
add below
```
JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
```

make environment variables effective
```
source /etc/environment
```

### Python
```
sudo apt-get install --only-upgrade python3
sudo apt update && sudo apt upgrade -y
```

### GCC
```
sudo apt install gcc-11 g++-11
sudo ln -s /usr/bin/g++-11 /usr/bin/g++
sudo ln -s /usr/bin/gcc-11 /usr/bin/gcc
```

### MySQL
```
sudo apt install mysql-client-core-8.0
```

### Doris
```
wget https://apache-doris-releases.oss-accelerate.aliyuncs.com/apache-doris-2.0.12-bin-x64.tar.gz
tar -xf apache-doris-2.0.12-bin-x64.tar.gz
```

## Configure

### Frontend
```
cd  apache-doris-2.0.12-bin-x64/fe
sudo vim conf/fe.conf
```
add below
```
meta_dir = ${DORIS_HOME}/doris-meta
priority_networks = 127.0.0.1/24 
```

port
```
sudo ufw allow 8030/tcp
sudo ufw allow 9020/tcp
sudo ufw allow 9030/tcp
sudo ufw allow 9010/tcp
```

start
```
./bin/start_fe.sh --daemon
```

### Backend
```
mkdir /mnt/doris
cd  apache-doris-2.0.12-bin-x64/be
sudo vim conf/be.conf
```
add below
```
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
storage_root_path=/mnt/doris
priority_networks = 127.0.0.1/24 
```

port
```
sudo ufw allow 9060/tcp
sudo ufw allow 8040/tcp
sudo ufw allow 9050/tcp
sudo ufw allow 8060/tcp
```

start
```
sudo sysctl -w vm.max_map_count=2000000
ulimit -n 65536

./bin/start_be.sh --daemon
```

### Broker

start
```
cd apache-doris-2.0.12-bin-x64/extensions
./apache_hdfs_broker/bin/start_broker.sh --daemon
```

### Check Listening Ports

```
apt install net-tools
sudo netstat -tuln
```

expected results are as follows
```
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:9060            0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:9050            0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:8040            0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:8060            0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN
tcp6       0      0 :::8000                 :::*                    LISTEN
tcp6       0      0 :::9020                 :::*                    LISTEN
tcp6       0      0 :::9030                 :::*                    LISTEN
tcp6       0      0 :::22                   :::*                    LISTEN
tcp6       0      0 127.0.0.1:9010          :::*                    LISTEN
tcp6       0      0 :::8030                 :::*                    LISTEN
udp        0      0 127.0.0.53:53           0.0.0.0:*
udp6       0      0 fe80::be24:11ff:fee:546 :::*
```

## Connect

### MySQL Command Line Interface
```
mysql -h 127.0.0.1 -P 9030 -u root
```

### Add Backend
```
ALTER SYSTEM ADD BACKEND "127.0.0.1:9050";
SHOW BACKENDS;
```

expected results are as follows  
```
+-----------+-----------+---------------+--------+----------+----------+---------------------+---------------------+-------+----------------------+-----------+------------------+--------------------+---------------+---------------+---------+----------------+--------------------+--------------------------+--------+-------------------------+---------------------------------------------------------------------------------------------------------------+-------------------------+----------+
| BackendId | Host      | HeartbeatPort | BePort | HttpPort | BrpcPort | LastStartTime       | LastHeartbeat       | Alive | SystemDecommissioned | TabletNum | DataUsedCapacity | TrashUsedCapcacity | AvailCapacity | TotalCapacity | UsedPct | MaxDiskUsedPct | RemoteUsedCapacity | Tag                      | ErrMsg | Version                 | Status                                                                                                        | HeartbeatFailureCounter | NodeRole |
+-----------+-----------+---------------+--------+----------+----------+---------------------+---------------------+-------+----------------------+-----------+------------------+--------------------+---------------+---------------+---------+----------------+--------------------+--------------------------+--------+-------------------------+---------------------------------------------------------------------------------------------------------------+-------------------------+----------+
| 10039     | 127.0.0.1 | 9050          | 9060   | 8040     | 8060     | 2024-07-12 03:38:01 | 2024-07-12 03:40:05 | true  | false                | 0         | 0.000            | 0.000              | 1.000 B       | 0.000         | 0.00 %  | 0.00 %         | 0.000              | {"location" : "default"} |        | doris-2.0.12-2971efd194 | {"lastSuccessReportTabletsTime":"N/A","lastStreamLoadTime":-1,"isQueryDisabled":false,"isLoadDisabled":false} | 0                       | mix      |
+-----------+-----------+---------------+--------+----------+----------+---------------------+---------------------+-------+----------------------+-----------+------------------+--------------------+---------------+---------------+---------+----------------+--------------------+--------------------------+--------+-------------------------+---------------------------------------------------------------------------------------------------------------+-------------------------+----------+
```

### Add Broker
```
ALTER SYSTEM ADD BROKER {BROKER_NAME} "127.0.0.1:8000";
SHOW BROKER;
```

expected results are as follows  
```
Name            |Host         |Port|Alive|LastStartTime      |LastUpdateTime     |ErrMsg                                                              |
----------------+-------------+----+-----+-------------------+-------------------+--------------------------------------------------------------------+
{BROKER_NAME}   |127.0.0.1    |8000|true |2024-07-10 08:18:12|2024-07-16 03:27:39|                                                                    |
```