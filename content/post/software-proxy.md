---
title: "常用软件代理设置"
date: 2021-03-01T22:19:30+08:00
draft: false
tags: ["设置"]
categories: ["系统软件"]
---
本文记录几个常用软件的代理设置方法。
### SSH
在我们使用 Git 的时候，经常需要使用SSH来进行Clone等操作，但是常规的HTTP代理对于SSH是不生效的，这需要我们修改`~/.ssh/config`文件。
```
ProxyCommand connect -H 127.0.0.1:7890 %h %p

Host github.com
  User git
  Port 22
  Hostname github.com
  IdentityFile "Your\Path\To\.ssh\id_rsa"
  TCPKeepAlive yes
  IdentitiesOnly yes

Host ssh.github.com
  User git
  Port 443
  Hostname ssh.github.com
  IdentityFile "Your\Path\To\.ssh\id_rsa"
  TCPKeepAlive yes
  IdentitiesOnly yes
```
这样，我们通过SSH方式访问Github的时候就可以通过代理了。

<!--more-->

### Docker
在执行`docker pull`时，是由守护进程`dockerd`来执行。 因此，代理需要配在`dockerd`的环境中。 而这个环境，则是受`systemd`所管控，因此实际是`systemd`的配置。
添加文件`/etc/systemd/system/docker.service.d/proxy.conf`内容为
```
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:7890/"
Environment="HTTPS_PROXY=http://127.0.0.1:7890/"
Environment="NO_PROXY=localhost,127.0.0.1,.example.com"
```
修改完毕后，需要重载systemd并重启dockerd才能生效。
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### Maven
修改`~/.m2/settings.xml`文件中的`<proxies>`字段为
```
<proxies>
    <proxy>
        <id>http_proxy</id>
        <active>true</active>
        <protocol>http</protocol>
        <host>127.0.0.1</host>
        <port>7890</port>
    </proxy>
</proxies>
```

### gradle
修改`~/.gradle/gradle.propeties`为
```
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=7890
systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=7890
```

### Conda
修改`~/.condarc`为
```
proxy_servers:
    http: http://127.0.0.1:7890
    https: http://127.0.0.1:7890
```

### NPM
修改`~/.npmrc`为
```
proxy=http://127.0.0.1:7890/
https-proxy=http://127.0.0.1:7890
```
yarn也类似。
