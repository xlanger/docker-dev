### 创建 `nodejs` 镜像

基于 `node:8.1.0-alpine` 镜像创建（推荐）：
```bash
$ cd /path/to/node
$ docker build --rm -t my-node:8.1.0-alpine .
```

基于 `CentOS` 镜像创建：
```bash
$ cd /path/to/node/centos
$ docker build --rm -t node:6.9.5 .
```
