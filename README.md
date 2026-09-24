# Xboard 订阅管理面板

一个基于 **[cedar2025/xboard](https://github.com/cedar2025/xboard)** (MIT License) 二次开发的 PHP 代理订阅管理面板，用于统一管理节点、套餐、支付与用户订阅。

> 🎬 **在线体验**：<https://xboard.88531.cn>
>
> 管理后台入口为后台地址（安装时自动生成），前台为用户中心（注册 / 登录 / 购买套餐 / 订阅节点）。

---

## 功能特性

- 基于 V2board 二次开发，Laravel 10 内核
- 适配 Laravels / Webman，并发达 10 倍以上提升
- 配置全部存数据库，改完即生效，无需重启
- 支持 Docker 部署 & 分布式部署（Web / DB / 节点分离）
- 订阅分发支持 Hy2、sing-box、自动识别客户端版本下发新协议
- 支持按用户 IP 地区自动分配节点
- Cloudflare 真实访客 IP 支持
- 内置多支付插件：易支付(EPay)、MGate、支付宝当面付(AlipayF2F)、Stripe、BTCPay、CoinPayments、Coinbase
- 支持 SQLite（个人部署首选）与 MySQL 双数据库
- 路由过滤：订阅地址追加 `&filter=HongKong|USA` 过滤节点

## 面板使用指南（用户）

📖 **完整图文使用文档（注册登录 / 订阅导入 / 各平台客户端教程）**：<https://doc.88531.cn/docs/vpncentid/vpncentid-1ghjce2lu8tpn>

1. **注册/登录** —— 打开站点首页，注册自己的邮箱账号
2. **购买套餐** —— 在「商店」选择套餐 → 创建订单 → 选择支付方式（支付宝/微信等）→ 完成支付
3. **订阅节点** —— 支付成功后订单自动开通，在「我的订阅」复制订阅地址，粘贴到 v2rayN / Shadowrocket / Clash 等客户端即可导入全部节点
4. **流量/到期** —— 个人中心可查看剩余流量、到期时间、在线设备数

> 更多各平台客户端图文教程（Windows / 安卓 / iOS / NAS 软路由 / Shadowsocks 等）见：[科学上网客户端使用教程目录](https://doc.88531.cn/docs/vpncentid)

## 部署教程

### 一、Docker Compose（推荐）

```bash
# 1. 拉取仓库
git clone https://github.com/personal82555/xboard.git
cd xboard

# 2. 使用自带示例配置
cp docker-compose.sample.yaml docker-compose.yaml

# 3. 启动（内置安装向导）
docker compose run -it --rm xboard php artisan xboard:install
# 按提示选择 SQLite / MySQL、设置管理员邮箱密码，记下后台地址

# 4. 启动全部服务
docker compose up -d
```

已有旧版本升级：

```bash
docker compose run -it --rm xboard php artisan xboard:update
docker compose up -d
```

> SQLite 数据、`.env`、配置等都在 `.docker/.data` 目录，直接挂载即可持久化，重建容器不丢数据。

### 二、Dockerfile / 宝塔

- 镜像：`ghcr.io/cedar2025/xboard:latest`（或 `legacy` 稳定版）
- 宝塔面板：新建网站 → 反向代理容器端口即可

### 三、后台基础配置

| 配置项 | 说明 |
|---|---|
| 系统配置 → 站点地址 | 必填 `app_url`，影响支付回调与邮件链接 |
| 支付配置 | 添加支付方式 → 网关选 `EPay`/`AlipayF2F`/`Stripe` → 填商户参数 → 启用 |
| 节点管理 | 添加节点 → 选择协议(sing-box/Hy2 等) → 复制定阅分发地址到服务器 |
| 套餐管理 | 创建套餐 → 设置周期/价格/节点分组 → 关联到商店 |

## 开发

```bash
# 依赖安装（需要 PHP >= 8.2 / composer）
composer install
cp .env.example .env && php artisan key:generate

# 本地跑起
php artisan serve
```

源码核心目录：

```
app/
├── Payments/        # 支付网关插件（EPay/Alipay/Stripe/BTC等）
├── Protocols/       # 订阅协议生成（sing-box/clash/v2ray…）
├── Services/        # 业务逻辑（订单/支付/计划任务/订阅…）
└── Http/Controllers # API 控制器（v1/v2, admin/user/guest）
```

文案模板、页面在 `resources/` 与 `public/theme/`。

---

## 免责声明 / Disclaimer

本项目仅用于学习交流，不得用于商业/非法用途。
This project is released for learning purposes only, use at your own risk. Original work © Tokumeikoi & cedar2025, under MIT License — see [LICENSE](LICENSE)。
