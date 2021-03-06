---
name: 常见问题
sort: 3
---

# 常见问题

### 部署应用

#### 如果我已经有其它应用使用端口 3000 了怎么办？

您可以在您第一次启动 Gogs 的时候通过以下方式修改默认的监听端口：

    ./gogs web -port 3001

该方法同样会在您填写安装页面时生效，因此请选择一个您希望以后一直给 Gogs 使用的端口号。

#### 如何使用 Nginx 的反向代理？

在 `nginx.conf` 文件中，将下面的 `server` 部分增加至 `http` 分区内并重载配置：

```
server {
    listen 80;
    server_name git.crystalnetwork.us;

    location / {
        proxy_pass http://localhost:3000;
    }
}
```

#### 如何使用 HTTPS？

在 `custom/conf/app.ini` 文件中修改下列配置选项（以下仅为示例）：

```
[server]
PROTOCOL = https
ROOT_URL = https://try.gogs.io/
CERT_FILE = custom/https/cert.pem
KEY_FILE = custom/https/key.pem
```

如果您想要使用自签名的 HTTPS，则可以使用下列命令来生成所需文件：

	$ ./gogs cert -ca=true -duration=8760h0m0s -host=myhost.example.com

#### 如何使用离线模式？

如果您需要将 Gogs 运行于内网环境下，只需将 `custom/conf/app.ini` 文件中的配置选项 `server -> OFFLINE_MODE` 修改为 `true` 即可。

#### 如何使用自定义 robots.txt？

在 `custom` 目录下创建 `robots.txt` 文件即可。

#### 如何以守护进程形式运行？

Gogs 拥有一些由第三方提供的脚本来支持以守护进程形式运行：

- [init.d/centos](https://github.com/gogits/gogs/blob/master/scripts/init/centos/gogs)
- [init.d/debian](https://github.com/gogits/gogs/blob/master/scripts/init/debian/gogs)
- 下小节中的 Systemd 服务

#### Systemd 服务

在 GitHub 上的 Gogs 仓库有一个 [systemd 服务模版文件](https://github.com/gogits/gogs/blob/master/scripts/systemd/gogs.service)，您需要做出一定的修改才能够使用它：

1. 将 `ExecStart` 属性替换默认的 `start.sh` 路径为您的 Gogs 实际安装路径下的位置。
2. 将 `WorkingDirectory` 属性替换为您的 Gogs 实际安装路径根目录。
3. [可选] 如果您 Gogs 安装示例使用 `MySQL/MariaDB`、`PostgreSQL`、`Redis` 或 `memcached`，请去掉相应 `After` 属性的注释。

当您完成修改后，请将文件保存至 `/etc/systemd` 然后执行 `sudo systemd restart gogs`。

您可以通过 `sudo systemd status gogs -l` 或 `sudo journalctl -b -u gogs.service`  命令检查 Gogs 的运行状态。

### 管理权限

#### 如果成为管理员？

1. 当用户注册时 `ID = 1` 则会自动成为管理员，无需邮箱验证。
2. 默认管理员登录 `Admin -> Users` 面板并设置其它人员为管理员。
3. 通过安装页面注册的用户会自动成为管理员。

### 仓库管理

#### 如何给予用户 Git 钩子权限？

由于这是一个 **可能损坏您系统的高级权限**，您必须在用户管理面板（`/admin/users/:userid`）为您完全信任的用户单独开启。

### 其它

#### 如何获取 Gogs 当前版本？

纯文本形式的 Gogs 版本存储在文件 `templates/VERSION` 中。

#### `gogs serv` 命令是做什么用的？

用户完全没有理由和必要去手动执行该命令，当新的 SSH 推送发生时，它会被 Git 的 Update Hook 自动调用。
