# NAOS Container

[English](README.md) | **简体中文**

Linux ARM64 静态链接容器运行工具，包含 `naosd`、`naos` 和 `naos-exec`。

## 下载

[最新版本](https://github.com/mixtile-rockchip/naos-container/releases/latest) · [全部版本](https://github.com/mixtile-rockchip/naos-container/releases)

- [`naos-alpine-arm64.tar.gz`](https://github.com/mixtile-rockchip/naos-container/releases/latest/download/naos-alpine-arm64.tar.gz)
- [`naos-alpine-arm64.tar.gz.sha256`](https://github.com/mixtile-rockchip/naos-container/releases/latest/download/naos-alpine-arm64.tar.gz.sha256)
- [`sbom.spdx.json`](https://github.com/mixtile-rockchip/naos-container/releases/latest/download/sbom.spdx.json)

```sh
sha256sum -c naos-alpine-arm64.tar.gz.sha256
tar -xzf naos-alpine-arm64.tar.gz
```

## 包内容

| 路径 | 内容 |
|---|---|
| `bin/naosd` | 容器守护进程 |
| `bin/naos` | 命令行客户端 |
| `bin/naos-exec` | 容器内命令执行工具 |
| `etc/naosd.yml` | 配置模板 |
| `script/` | 生命周期和网络脚本 |
| `sbom.spdx.json` | SPDX 2.3 软件物料清单 |

## 使用

在支持 namespace 和 cgroup 的 ARM64 Linux 上以 root 运行。将三个程序安装到可执行文件搜索路径中。

将 `etc/naosd.yml` 复制到 `/etc/naos/naosd.yml`，按目标系统调整容器目录、临时目录、网络命名空间目录、日志和脚本路径。默认通信 socket 为 `/var/run/naosd.sock`，客户端可通过 `-S PATH` 指定其他路径。

每个应用目录包含 ARM64 根文件系统 `rootfs/` 和 `manifest.yml`，清单中填写 `meta.id` 与 `run.arguments`。根文件系统需保留文件属主和权限。

```sh
naosd -c /etc/naos/naosd.yml
```

在另一个终端中按容器 ID 操作：

```sh
naos start myapp
naos list
naos-exec myapp /bin/sh
naos stop myapp
```

也可以通过绝对路径启动清单，清单旁需有 `rootfs/`：

```sh
naos start /userdata/myapp/manifest.yml
```

清单的 `meta.id` 作为容器 ID。外部目录在当前守护进程中登记，重启守护进程后需再次指定清单路径；应用文件由提供者管理。
