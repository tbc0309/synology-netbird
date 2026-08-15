# Synology NetBird

适用于 Synology DSM 7 的 [NetBird](https://github.com/netbirdio/netbird) 客户端套件。

## 功能

- 支持 x86_64 和 ARM64 群晖设备
- 使用 DSM 原生 TUN，不捆绑 iptables 扩展或额外 Netfilter 内核模块
- 自动识别 Routing Peer / Exit Node 角色并切换运行模式
- 安装向导支持 Setup Key 和自托管管理服务器地址
- GitHub Actions 自动检查 NetBird 上游版本、构建并发布 SPK

## 自动路由模式

DSM 缺少 `libxt_mark.so` 时，NetBird 的原生 iptables 初始化会失败并回退到 userspace firewall。普通 Peer 连接不受影响，但仅靠该回退不足以保证路由节点功能。

套件的 `netbird-service` 会每 15 秒读取一次 `netbird status -d`：

- `Networks: -`：以标准 TUN 模式运行，适合普通 Peer
- `Networks:` 包含网段：判定本机已被控制端指定为 Routing Peer 或 Exit Node，自动以 `NB_USE_NETSTACK_MODE=true` 重启
- 路由角色被移除：自动恢复标准 TUN 模式

模式状态保存在 `/var/packages/Netbird/var/router-mode`，重启 DSM 或套件后仍会优先使用上次确认的模式，并在连接控制端后重新判断。

出于安全考虑，套件不会自动设置 `NB_ENABLE_LOCAL_FORWARDING=true`。访问路由节点自身局域网地址上的服务时才需要该选项；访问路由节点后方网段、Masquerade 和 Exit Node 不需要默认开启它。

## 实机验证

- ARM64：DS218（rtd1296），DSM 7.2.2，Linux 4.4.302
- x86_64：SA6400（epyc7002），DSM 7.2.2，Linux 5.10.55
- NetBird 0.77.0 普通 Peer 双向 Ping、DSM HTTPS 和 SSH 正常
- DS218 作为 userspace Routing Peer，SA6400 经 NetBird 访问 `192.168.31.111/32`：Ping 4/4 成功
- DS218 开启 Masquerade 后返回流量正常
- DS218 作为 Exit Node，SA6400 的 `0.0.0.0/0` 明确通过 `wt0`，公网访问和 ICMP 正常

实测两种 DSM 内核均已有 MARK、conntrack 和 NAT/MASQUERADE 能力。缺失的是部分 iptables 用户态扩展，因此不需要随套件补充 `.ko`。

## 系统要求

- DSM 7.1-42661 或更高版本
- x86_64 或 ARM64
- root 权限
- 可用的 `/dev/net/tun`

## 安装

1. 从 [Releases](https://github.com/tbc0309/synology-netbird/releases) 下载对应架构的 SPK。
2. 在 DSM 套件中心选择“手动安装”。
3. 按安装向导填写 Setup Key；自托管用户同时填写管理服务器地址。

Setup Key 属于敏感凭据，建议使用一次性或短有效期 Key，安装完成后及时撤销不再使用的 Key。

## Root 权限

套件需要 root 权限创建和管理 TUN 接口。请安装并启用 [SimplePermissionManager](https://github.com/XPEnology-Community/SimplePermissionManager)，或在每次重新安装后通过 SSH 执行：

```sh
sudo sed -i 's/package/root/g' /var/packages/Netbird/conf/privilege
```

## 自动更新

GitHub Actions 每天北京时间 10:15 检查 NetBird 上游版本。确认 amd64、arm64 文件均已发布后，自动更新版本号、构建两个 SPK 并发布到 Releases。

也可以在 Actions 页面手动运行 `Check upstream and release`，启用 `force_build` 重新构建当前版本。

## 日志

```sh
cat /var/packages/Netbird/var/Netbird.log
```

## 致谢

- [NetBird](https://github.com/netbirdio/netbird)
- [SimplePermissionManager](https://github.com/XPEnology-Community/SimplePermissionManager)
