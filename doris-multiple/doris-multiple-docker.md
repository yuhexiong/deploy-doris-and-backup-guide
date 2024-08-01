# One Frontend, One Backend And One Broker In One VM - Docker

## Overview

- Virtual Machine: Ubuntu v22.04.4
- Platform: JDK 11
- Database: Doris v2.0.12

### Architecture

**10.1.1.100 doris01.domain.name**: 1 FE, 1 BE  
**10.1.1.200 doris02.domain.name**: 1 FE, 1 BE  
**10.1.1.300 doris03.domain.name**: 1 BE, 1 BROKER  
**10.1.1.400**: DNS   

mount at **/mnt/doris**  
priority_networks = 10.1.1.1/24  


## Switch To Root User
```
sudo su -
```

## Copy Config File
```
cp ./conf/* .
```

## Run

### Frontend
```
sudo swapoff -a

# depend on your vm
sudo docker compose up -d doris-fe-01
sudo docker compose up -d doris-fe-02
```

### Backend
```
sudo swapoff -a

# depend on your vm
sudo docker compose up -d doris-be-01
sudo docker compose up -d doris-be-02
sudo docker compose up -d doris-be-03
```

### Broker
```
sudo swapoff -a
sudo docker compose up -d doris-broker-01
```
