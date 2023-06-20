## Docker 现有container修改端口及配置开机自启脚本：

### 1.  查询container的Id：
```
docker inspect [CONTAINER NAME or ID] | grep Id
```
### 2.  停止container：
```
sudo docker stop [CONTAINER NAME or ID]
```
### 3.  去到container文件夹，找到`hostconfig.json`：
Windows下这个文件在：
```
\\wsl.localhost\docker-desktop-data\data\docker\containers\[CONTAINER Id]\
```
Linux下这个文件在：
```
/var/lib/docker/containers/[CONTAINER Id]/
```
### 4.  修改`"PortBindings":{}`，例如添加两个端口映射，将container中的80、22端口分别映射至主机中的8080、2222：
```
"PortBindings": {
    "80/tcp": [
        {
            "HostIp": "0.0.0.0",
            "HostPort": "8080"
        }
    ],
    "22/tcp": [
        {
            "HostIp": "0.0.0.0",
            "HostPort": "2222"
        }
    ],
}
```
### 5.  将docker中的端口暴露出来，修改`config.v2.json`，在`"Config":`中添加：
```
"ExposedPorts": {
    "80/tcp":{},
    "22/tcp":{}
}
```
### 6.  修改容器配置，添加自启动脚本，修改`config.v2.json`,在`"Args":`和`"Cmd":`中添加`/home/vscode/startup.sh`，修改后结果如下：
```
"Args": [
    "-c",
    "echo Container started\ntrap \"exit 0\" 15\n/home/vscode/startup.sh\nexec \"$@\"\nwhile sleep 1 \u0026 wait $!; do :; done",
    "-"
]
```
```
        "Cmd": [
            "-c",
            "echo Container started\ntrap \"exit 0\" 15\n/home/vscode/startup.sh\nexec \"$@\"\nwhile sleep 1 \u0026 wait $!; do :; done",
            "-"
        ]
```
全部操作完成后在container中创建脚本文件并赋予可执行属性（该文件会在container启动时被root运行）；
```
touch /home/vscode/startup.sh
chmod +x /home/vscode/startup.sh
```
在文件首行添加`#!/bin/bash`后将需要container启动时自动运行的命令写入即可。

### 7.  重启docker服务：
Windows中直接右键工具栏右下角docker desktop图标然后选restart。

Linux中输入：
```
service docker restart
```
或者：
```
systemctl restart docker
```
### 8.  启动容器：
```
sudo docker start [CONTAINER NAME or ID]
```





