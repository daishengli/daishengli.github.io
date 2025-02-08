---
title: 内网穿透软件frp使用
date: 2024-12-10 16:37:29
description: FRP（Fast Reverse Proxy）是一款开源、高性能的内网穿透和反向代理软件，支持 TCP、UDP、HTTP、HTTPS 等多种协议，能够将内网服务安全、便捷地暴露到公网。它具备 TLS 加密、P2P 通信、动态 DNS 和 Web 界面管理等功能，适用于远程访问内网服务、开发环境共享及穿透防火墙等场景。FRP 部署简单，跨平台支持，是高效管理内网服务的理想工具。
tags:
  - 软件使用
categories:
  - 软件使用
---

# frp 是什么？

FRP（Fast Reverse Proxy）是一个开源、简洁易用、高性能的内网穿透和反向代理软件，它支持 TCP、UDP、HTTP、HTTPS 等多种协议。FRP 可以帮助用户将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网。

## FRP 的功能特点包括：

1. **多协议支持**：FRP 支持 TCP、UDP、HTTP、HTTPS 等多种协议，满足不同应用场景的需求。
2. **P2P 通信**：FRP 支持 P2P 模式，提高特殊环境下连接的灵活性。
3. **TLS 加密**：FRP 提供 TLS 加密功能，确保数据传输的安全性。
4. **动态 DNS**：FRP 支持动态 DNS，便于动态 IP 环境中的稳定访问。
5. **Web 界面**：FRP 提供 Web 界面，简化管理和监控。
6. **反向代理**：FRP 作为反向代理，使得内部服务可以通过外部服务器被访问。

## FRP 的工作原理：

FRP 主要由客户端（frpc）和服务端（frps）组成。通常情况下，服务端部署在具有公网 IP 地址的机器上，而客户端部署在需要穿透的内网服务所在的机器上。用户通过访问服务端的 frps，由 frp 负责根据请求的端口或其他信息将请求路由到相应的内网机器，从而实现通信。

## FRP 的应用场景：

1. **远程访问内网服务**：例如，通过 FRP 访问家里或公司的服务器，而不需要配置复杂的端口映射。
2. **开发环境共享**：开发者可以通过 FRP 共享自己本地的开发环境，外部团队可以直接访问内网的应用和 API。
3. **穿透防火墙/NAT**：即使内网服务器处于 NAT 后面或防火墙后面，仍然可以通过 FRP 将服务暴露到公网。

FRP 的安装和部署相对简单，采用 Golang 编写，支持跨平台，仅需下载对应平台的二进制文件即可执行，没有额外依赖。用户可以根据需要编写配置文件，启动服务端和客户端，实现内网服务的公网访问。

Github 地址：

> https://github.com/fatedier/frp

Frp 文档地址：

> https://gofrp.org/zh-cn/docs

# frp 具体使用

## 下载

可以从 GitHub 的 Release 页面中下载最新版本的客户端和服务器二进制文件。所有文件都打包在一个压缩包中，还包含了一份完整的配置参数说明。

> https://github.com/fatedier/frp/releases

## 部署

1. 解压下载的压缩包。
2. 将`frpc`复制到内网服务所在的机器上。
3. 将`frps`复制到拥有公网 IP 地址的机器上，并将它们放在任意目录。

## 开始使用！

1. 编写配置文件。
2. 使用以下命令启动服务器：`./frps -c ./frps.toml`。
3. 使用以下命令启动客户端：`./frpc -c ./frpc.toml`。

## 使用`systemd`来管理服务端

在服务器`/etc/systemd/system`创建`frps.service`文件并写入如下内容

```bash
# /etc/systemd/system/frps.service
[Unit]
# 服务名称，可自定义
Description = frp server
After = network.target syslog.target
Wants = network.target
[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /path/to/frps -c /path/to/frps.toml

[Install]
WantedBy = multi-user.target
```

使用 systemd 命令管理 frps 服务

```bash
# 启动frp
sudo systemctl start frps
# 停止frp
sudo systemctl stop frps
# 重启frp
sudo systemctl restart frps
# 查看frp状态
sudo systemctl status frps
```

设置开机自启
`sudo systemctl enable frps`

## 一个例子

> 服务端使用 frps，我的 frps 安装在`/usr/local/frp`，下面有两个文件，分别是`frps`和`fros.toml`，其中`fros.toml`是配置文件。
> 客户端使用 frpc，我的 frpc 安装在`D:\development\frp`，下面有两个文件，分别是`frpc.exe`和`froc.toml`，其中`froc.toml`是配置文件。
> 这里是做 http 转发的配置，其他配置可参考官方文档。

修改服务端配置，即修改服务端`/usr/local/frp/fros.toml`文件，内容如下

```bash
# bindPort是使用的端口号
bindPort = 7000
# webServer配置是服务端 Dashboard 的配置
# 默认为 127.0.0.1，如果需要公网访问，需要修改为 0.0.0.0。
webServer.addr = "0.0.0.0"
webServer.port = 7500
# dashboard 用户名密码，可选，默认为空
webServer.user = "替换为你自己想用的账号"
webServer.password = "替换为你自己想用的密码"
# 身份认证token，客户端和服务端一致才能成功，还可以参考官方文档使用 OIDC
auth.token = "替换为你自己想用的token"
```

启动服务端
`/usr/local/frp/frps -c /usr/local/frp/frps.toml`
修改客户端配置

```bash
serverAddr = "此处填写服务端所在服务器IP"
# 此处修改为服务端使用的端口
serverPort = 7000
# 此处修改为跟服务端token一致即可
auth.token = "替换为你服务端的token"

[[proxies]]
# 名称，可随意修改
name = "http_forward"
# 类型，这里是tcp，还可以是http等，参考官方文档使用
type = "tcp"
# localIP和localPort是你本地想要代理出去的服务
localIP = "127.0.0.1"
localPort = 8080
# remotePort是指要占用服务端具体哪个端口，记得打开服务器对应端口的防火墙
remotePort = 3000
```

启动客户端
`D:\development\frp\frpc.exe -c D:\development\frp\frpc\frpc.toml`
现在可以使用了
假设服务器的 ip 是`43.159.71.116`，现在访问`http://43.159.71.116:3000`即可穿透到本地`http://127.0.0.1:8080`的服务。

# 为什么使用 frp？

使用 FRP（Fast Reverse Proxy）的原因有很多，以下是一些主要的优点和应用场景：

1. **简化内网服务的公网访问**：

   - FRP 允许用户轻松地将内网服务暴露给公网，无需复杂的网络配置或端口映射。

2. **支持多种协议**：

   - FRP 支持 TCP、UDP、HTTP、HTTPS 等多种协议，适用于不同的应用和服务。

3. **安全性**：

   - FRP 提供 TLS 加密功能，可以保护数据传输的安全，防止数据在传输过程中被窃取或篡改。

4. **高性能**：

   - FRP 设计为高性能的反向代理应用，可以处理大量的连接和数据传输。

5. **P2P 通信**：

   - FRP 支持 P2P 模式，可以在特殊网络环境下提高连接的灵活性。

6. **动态 DNS 支持**：

   - 对于动态 IP 环境，FRP 支持动态 DNS，使得服务可以稳定地被访问。

7. **Web 界面管理**：

   - FRP 提供 Web 界面，方便用户管理和监控服务状态。

8. **反向代理功能**：

   - 作为反向代理，FRP 可以将内部服务通过外部服务器暴露给公网，增加一层安全性。

9. **跨平台支持**：

   - FRP 支持多种操作系统平台，包括 Linux、Windows 和 macOS。

10. **开源**：

    - FRP 是一个开源项目，用户可以自由使用、修改和分发。

11. **易于部署和使用**：

    - FRP 的安装和配置相对简单，不需要专业的网络知识。

12. **适用于多种场景**：

    - 无论是远程办公、开发环境共享、个人项目托管还是企业内部服务的外部访问，FRP 都能提供解决方案。

13. **负载均衡和端口复用**：

    - FRP 支持代理组间的负载均衡和端口复用，可以更高效地利用公网资源。

14. **插件系统**：
    - FRP 具有高度扩展性的服务端插件系统，方便用户根据需求进行功能扩展。

使用 FRP 可以大大简化网络服务的部署和管理，提高工作效率，同时保证服务的安全性和稳定性。
