# xui-pox

x-ui 面板精简修改版一键安装与管理脚本。

- 支持纯 IPv4、纯 IPv6,以及 AMD64 / ARM64 架构的 VPS
- 支持 Alpine / Ubuntu / Debian / CentOS 系统,推荐最新版 Ubuntu
- 订阅节点本地化生成,不依赖第三方节点转换外链
- 无后台定时任务、无开机自启、无看门狗、无在线更新——所有行为完全手动可控
- 仓库内不含任何二进制文件,全部组件由 GitHub Actions 工作流自动打包发布

## 安装

以 root 身份在 VPS 上执行:

```
bash <(wget -qO- https://raw.githubusercontent.com/suanx/xui-pox/main/install.sh)
```

或

```
bash <(curl -Ls https://raw.githubusercontent.com/suanx/xui-pox/main/install.sh)
```

## 安装过程

脚本会依次引导你完成:

1. 是否开放端口并关闭防火墙(回车默认执行,已自行处理防火墙可选 2 跳过)
2. 设置面板登录用户名(回车随机生成 6 位,不允许包含 `admin` 字样)
3. 设置面板登录密码(回车随机生成 6 位)
4. 设置面板登录端口(回车随机取 10000-65535)
5. 设置面板登录根路径(回车随机 3 位,务必保存好)
6. 是否启用 https 登录(输入 `y` 会调用内置 Acme 申请证书;回车跳过用 http)
7. 安装完成后自动显示登录地址、账号密码,并进入管理菜单

⚠️ 安装结束时会打印全部登录信息,请务必记录。

## 使用

安装完成后,终端输入 `x-ui` 打开管理菜单:

| 选项 | 功能 |
|---|---|
| 1 | 一键安装 xui-pox |
| 2 | 删除卸载(清除面板全部数据) |
| 3 | 其他设置:Argo 固定/临时隧道、订阅优选 IP、Gitlab 订阅、warp-wireguard 账号、hy2 多端口跳跃、本地 IP 订阅 |
| 4 | 变更面板设置:用户名密码、登录端口、根路径、还原面板 |
| 5 | 关闭 / 重启 xui-pox |
| 6 | 生成并查看聚合通用节点、Clash-Meta 与 Sing-box 客户端配置及订阅链接 |
| 7 | 查看运行日志 |
| 8 | 一键原版 BBR+FQ 加速 |
| 9 | 管理 Acme 申请域名/IP 证书 |
| 10 | 管理 Warp(本地 Netflix、ChatGPT 解锁检测) |
| 11 | 添加 WARP-plus-Socks5 代理模式(本地 Warp / 多地区 Psiphon 出口) |
| 12 | 刷新 IP 配置及参数显示 |
| 0 | 退出 |

### 支持协议

hy2(Hysteria2,含端口跳跃)、vless(reality-vision / tcp-vision / ws / tcp)、vmess(ws / tcp)、trojan(tcp-tls / ws-tls)、shadowsocks;Argo 临时 / 固定隧道(vmess/vless/trojan + ws)。

### 订阅链接

菜单 6 可生成三种客户端配置,并可通过三种方式分享:

- **本地 IP 订阅**:菜单 3 → 6,用内置 busybox httpd 起一个订阅服务,生成 `clmi.yaml`(Clash-Meta)、`sbox.json`(Sing-box)、`jhsub.txt`(通用聚合)三个链接
- **Gitlab 订阅**:菜单 3 → 3,把配置推送到你自己的 Gitlab 仓库
- **Argo 隧道**:菜单 3 → 1,给 ws 节点套 Cloudflare Argo 隧道

### WARP-plus-Socks5

菜单 11 可启动本地 Socks5 代理(默认端口 40000),支持 31 个国家/地区的 Psiphon 出口,并可与 xray 出站(菜单内 `socks5-warp` 分流)联动。

## 升级 / 重装

本版本已移除在线更新功能,升级方式为重新执行安装命令:

```
bash <(curl -Ls https://raw.githubusercontent.com/suanx/xui-pox/main/install.sh)
```

脚本检测到已安装会提示先卸载(菜单 2)再装。卸载前建议在面板"备份与恢复"下载 `x-ui-yg.db` 备份数据库。

## 二进制打包(GitHub Actions)

仓库内不含二进制。推送代码到 `main` 分支(或手动触发)时,工作流 [.github/workflows/release.yml](.github/workflows/release.yml) 会自动:

1. 获取 x-ui 面板主程序包(amd64 / arm64)
2. 获取 warp-plus 二进制(amd64 / arm64)并重命名为 `xuiwpph_*`
3. 发布到本仓库 Release(tag `latest`),脚本通过 `releases/latest/download/...` 下载

如 Release 资产缺失,在 GitHub 仓库 Actions 页面手动 Run workflow 即可重建。

## 仓库文件

| 文件 | 说明 |
|---|---|
| `install.sh` | 一键安装与管理脚本(主程序) |
| `config.json` | xray 默认出站配置模板(含 WARP WireGuard 出站) |
| `acme.sh` | Acme 证书申请脚本(菜单 9 / 安装时 https 选项调用) |
| `CFwarp.sh` | Warp 管理脚本(菜单 10 调用) |
| `version` | 版本记录 |
| `ssh.yml` | 可选:服务器 SSH 保活工作流(仅手动触发,需自行填写账户信息) |
| `.github/workflows/release.yml` | 二进制自动打包发布工作流 |

## 注意事项

- 安装路径为 `/usr/local/x-ui`,快捷命令 `x-ui`,面板数据库在 `/etc/x-ui-yg/`
- 面板首次登录后建议:更换随机生成的账号密码、启用 https、确认根路径非空
- 已知保留的外部依赖(仅功能调用,无推广内容):面板主程序包与 warp-plus 上游、Acme 官方客户端、Cloudflare cloudflared / WARP API、DNS 与分流规则集
