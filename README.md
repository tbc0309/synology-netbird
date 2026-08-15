# synology-netbird

Synology [Netbird](https://github.com/netbirdio/netbird)

## Automatic updates

GitHub Actions checks the latest upstream NetBird release every day at 10:15
Asia/Shanghai. When both amd64 and arm64 archives are available, it updates
`INFO`, commits the new version, builds both DSM 7 SPK packages, and publishes
them to the matching GitHub release.

The workflow can also be started manually. Enable `force_build` to rebuild and
replace the assets for the current version without changing `INFO`.

package beta by [IMNKS.COM](https://imnks.com/9226.html)

iptables from：https://github.com/RROrg/syno-iptables


需要root权限启动：请安装[SimplePermissionManager](https://github.com/XPEnology-Community/SimplePermissionManager)（授权管理器）套件并激活它。
Need root：Please install SimplePermissionManager package and activate it.

或SSH修复权限，仅对本次安装有效(Or SSH repair permission，valid only now)：
sudo sed -i 's/package/root/g' /var/packages/Netbird/conf/privilege
