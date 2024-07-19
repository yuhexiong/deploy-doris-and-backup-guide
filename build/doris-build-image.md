# Build Image

Document refer to [Apache Doris v2.1](https://doris.apache.org/docs/install/cluster-deployment/run-docker-cluster) and [Apache Doris v1.2](https://doris.apache.org/docs/1.2/install/construct-docker/construct-docker-image/)  

## Overview

- Virtual Machine: Ubuntu v22.04.4
- Platform: JDK 11
- Database: Doris v2.0.12


## Switch To Root User
```
sudo su -
```

## Install

### Doris
```
wget https://apache-doris-releases.oss-accelerate.aliyuncs.com/apache-doris-2.0.12-bin-x64.tar.gz
```

## Frontend

### Architecture
dockerfile refer to [fe/dockerfile](./fe/dockerfile)  
init_fe.sh refer to [fe/init_fe.sh](./fe/init_fe.sh)  

```
└── docker-build                                     // Root directory 
    └── fe                                           // FE directory
        ├── dockerfile                               // Dockerfile script
        └── resource                                 // Resource directory
            ├── init_fe.sh                           // Startup and registration script
            └── apache-doris-2.0.12-bin-x64.tar.gz   // Binary package
```

## Build
```
cd ./docker-build/fe
docker build . -t doris-fe
```

## Backend

### Architecture
dockerfile refer to [be/dockerfile](./be/dockerfile)  
init_be.sh refer to [be/init_be.sh](./be/init_be.sh)  

```
└── docker-build                                     // Root directory 
    └── be                                           // BE directory
        ├── dockerfile                               // Dockerfile script
        └── resource                                 // Resource directory
            ├── init_be.sh                           // Startup and registration script
            └── apache-doris-2.0.12-bin-x64.tar.gz   // Binary package
```

## Build
```
cd ./docker-build/be
docker build . -t doris-be
```

## Broker

### Build HDFS Broker
```
docker pull apache/doris:build-env-ldb-toolchain-latest
sudo docker run -it --name build-env-ldb-toolchain-latest -v /root/.m2:/root/.m2 -v /root/apache-doris-2.0.12-bin-x64:/root/doris/ apache/doris:build-env-ldb-toolchain-latest
```

in container  
```
./root/doris/build.sh --broker
```

exit container  
```
cd /root/apache-doris-2.0.12-bin-x64/output
tar czvf apache_hdfs_broker.tar.gz apache_hdfs_broker
```


### Architecture
dockerfile refer to [broker/dockerfile](./broker/dockerfile)  
init_be.sh refer to [broker/init_be.sh](./broker/init_broker.sh)  

```
└── docker-build                                     // Root directory 
    └── broker                                       // BROKER directory
        ├── dockerfile                               // Dockerfile script
        └── resource                                 // Resource directory
            ├── init_be.sh                           // Startup and registration script
            └── apache_hdfs_broker.tar.gz   // Binary package
```

## Build
```
cd ./docker-build/broker
docker build . -t doris-broker
```