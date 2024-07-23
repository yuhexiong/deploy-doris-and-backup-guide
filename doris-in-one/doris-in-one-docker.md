# One Frontend, One Backend And One Broker In One VM - Docker

## Overview

- Virtual Machine: Ubuntu v22.04.4
- Platform: JDK 11
- Database: Doris v2.0.12

## Switch To Root User
```
sudo su -
```

## Copy Config File
```
cp ./conf/* .
```

## Run
```
docker compose up -d
```

