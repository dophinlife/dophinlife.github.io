---
title: "docker engine 无法正常启动"
layout: post
category: note
tags: [docker]
excerpt: "docker engine 无法正常启动问题"
---

# 错误情况

执行 `systemctl start docker`，报以下错误：

```
Job for docker.service failed because the control process exited with error code. See "systemctl status docker.service" and "journalctl -xe" for details.
```

接着执行 `systemctl status docker.service`，详细信息如下：

```
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: activating (auto-restart) (Result: exit-code) since 四 2020-05-07 12:37:54 CST; 1s ago
     Docs: https://docs.docker.com
  Process: 24510 ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock (code=exited, status=1/FAILURE)
 Main PID: 24510 (code=exited, status=1/FAILURE)

5月 07 12:37:54 host-10-0-197-127 systemd[1]: Failed to start Docker Application Container Engine.
5月 07 12:37:54 host-10-0-197-127 systemd[1]: Unit docker.service entered failed state.
5月 07 12:37:54 host-10-0-197-127 systemd[1]: docker.service failed.
```

输出中没有有用的信息，最后发现是因为配置了国内的镜像，镜像文件为/etc/docker/daemon.json，将 daemon.json 改为 daemon.conf 后，docker即可正常启动。