# NAOS Container

**English** | [简体中文](README.zh-CN.md)

## Downloads

[Latest release](https://github.com/mixtile-rockchip/naos-container/releases/latest) · [All releases](https://github.com/mixtile-rockchip/naos-container/releases)

- [`naos-alpine-arm64.tar.gz`](https://github.com/mixtile-rockchip/naos-container/releases/latest/download/naos-alpine-arm64.tar.gz)
- [`naos-alpine-arm64.tar.gz.sha256`](https://github.com/mixtile-rockchip/naos-container/releases/latest/download/naos-alpine-arm64.tar.gz.sha256)
- [`sbom.spdx.json`](https://github.com/mixtile-rockchip/naos-container/releases/latest/download/sbom.spdx.json)

```sh
sha256sum -c naos-alpine-arm64.tar.gz.sha256
tar -xzf naos-alpine-arm64.tar.gz
```

## Usage

A statically linked container toolkit for Linux ARM64:

- `naosd` manages containers and serves a local Unix socket.
- `naos` controls the daemon and container lifecycle.
- `naos-exec` runs a command in an existing container.

The package contains:

```text
bin/naos                 Command-line client
bin/naosd                Container daemon
bin/naos-exec            Container command runner
etc/naosd.yml            Configuration template
script/                  Optional lifecycle and network scripts
sbom.spdx.json           SPDX 2.3 software bill of materials
```

Run the daemon as root on an ARM64 Linux system with namespace and cgroup
support. Install the three binaries in your executable search path.

Copy `etc/naosd.yml` to `/etc/naos/naosd.yml` and adapt it to your system.
Set `naosd.containersDir`, `naosd.tempDir`, `naosd.netnsDir` and the log path to
suitable locations. Review script paths, device access, volume mappings and
automatic startup settings. The default socket is `/var/run/naosd.sock`;
`naos` can select another socket with `-S PATH`.

Each installed application needs a directory under `containersDir` containing its ARM64
`rootfs/` and a `manifest.yml` with its `meta.id` and `run.arguments`.
Preserve the root filesystem's ownership and permissions.

Start the daemon:

```sh
naosd -c /etc/naos/naosd.yml
```

In another terminal, control an application using its container ID:

```sh
naos start myapp
naos list
naos-exec myapp /bin/sh
naos stop myapp
```

You can also start an application from an absolute manifest path, with its
ARM64 `rootfs/` directory beside the manifest:

```sh
naos start /userdata/myapp/manifest.yml
```

The manifest's `meta.id` becomes the container ID. The application directory is
registered for the current daemon process without copying its files or changing
automatic startup settings. After restarting the daemon, submit the manifest
path again. Use the ID to list, stop, or restart the container. External
directories cannot be replaced by `install` or removed by `uninstall`.

`sbom.spdx.json` records packaged file checksums and the reported static
dependencies. Unavailable license or version information is retained as
`NOASSERTION`.
