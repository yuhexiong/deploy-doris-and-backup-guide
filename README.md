# Deploy Doris Guide

Guide for deploying Apache Doris in one/three vitrual machines by script or docker.  

## Overview

- Platform: JDK 11
- Database: Doris v2.0.12


## Run

### Script
- one frontend, one backend and one broker in one vm (refer to [doris-in-one-script.md](./doris-in-one/doris-in-one-script.md))



## Docker

### Build Image
- build doris image fe/be/broker (refer to [doris-build-image.md](./build/doris-build-image.md))

### Docker Compose
- one frontend, one backend and one broker in one vm (refer to [doris-in-one-docker.md](./doris-in-one/doris-in-one-docker.md))
- two frontends, three backends and one broker in three vms (refer to [doris-multiple-docker.md](./doris-multiple/doris-multiple-docker.md))  


## Backup
- backup from 1 doris and restore to another (refer to [doris-backup.md](./doris-backup/doris-backup.md))  