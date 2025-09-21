# synology-netbird

Synology [Netbird](https://github.com/netbirdio/netbird)

package beta by [IMNKS.COM](https://imnks.com/9226.html)

iptables from：https://github.com/RROrg/syno-iptables


需要root权限启动：请安装SimplePermissionManager（授权管理器）套件并激活它。
Need root：Please install SimplePermissionManager package and activate it.

或SSH修复权限，仅对本次安装有效(Or SSH repair permission，valid only now)：
sudo sed -i 's/package/root/g' /var/packages/Netbird/conf/privilege
