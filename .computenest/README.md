# Deepseek-Harness社区版计算巢服务

## 目录说明

```text
.
├── .computenest
│   ├── README.md                        - 当前说明文件
│   ├── config.yaml                      - 计算巢服务配置（computenest-cli 据此构建服务）
│   ├── resources
│   │   ├── architecture                 - 服务架构图（config.yaml 引用）
│   │   └── icons
│   │       └── service_logo.png         - 服务 logo
│   └── ros_templates
│       └── template.yaml                - 单 ECS 版 ROS 模板
├── docs
│   ├── index_ecs_single.md              - 中文交付文档（MkDocs 首页）
│   ├── index_ecs_single_en.md           - 英文交付文档
│   └── *.png                            - 文档截图
├── mkdocs.yml                           - MkDocs 站点配置
└── README.md                            - 仓库说明
```

## 服务概要

Deepseek-Harness社区版基于 DeepSeek AI 开源项目 deepseek-harness 构建，提供浏览器 Web UI。服务部署后，用户需在 Web UI 中先配置模型提供方的 API Key，再选择工作区才能开始使用。

本服务无内置登录界面，已按计算巢安全代理方案发布：Web UI 内部监听 `127.0.0.1:13080`，Nginx 监听 `0.0.0.0:3080` 反代到本地 Web UI，输出 `WebUIAddress` 开启 `EnableWebProxy` 并使用私网 IP，安全组 3080 仅放行 `100.104.0.0/16`。

镜像内为 dsh 0.2.1-rc.5（Node 24.22.2）。0.2.x 启动时会生成一次性访问 token，Nginx 在首次访问时自动用它换取签名 Cookie 并跳转，用户通过安全代理打开即可，无需手动输入 token。

Nginx 对上游请求头做回环改写（`Host 127.0.0.1:13080`、`Origin http://127.0.0.1:13080`、`Sec-Fetch-Site same-origin`），以通过上游 `/api/*` 的浏览器信任栅栏。

## 为什么 Nginx 必须 `absolute_redirect off`

安全代理只对外暴露 HTTPS 8443 网关，反代下来的 Host 是网关域名（如 `aliyun-workbench.com`）。Nginx 默认 `absolute_redirect on`，会把首次访问的 `return 302 /?token=...` 拼成 `http://aliyun-workbench.com:3080/?token=...` 的绝对地址，浏览器被带到不可达的 3080 端口，表现为「安全代理打开页面一直卡住」。

因此在 `config.yaml` 的 EcsImage 构建脚本里，`start-dsh-web` 生成的 server 块固定写入 `absolute_redirect off;`，302 保持相对路径 `/?token=...`，token 换 Cookie 的整个跳转都留在代理网关内完成。

构建期内置三条断言，任一不满足即镜像构建失败，避免交付出「能打开但用不了」的镜像：

1. 使用 Cookie 容器访问 `http://127.0.0.1:3080/` 必须拿到 200；
2. 携带该 Cookie 对 `/api/remote.mux` 发起 WebSocket 升级必须拿到 101；
3. 另有一条定向断言：以网关 Host（`aliyun-workbench.com`）且无 Cookie 请求首页，`Location` 必须是相对路径，否则打印 nginx 配置并失败。

## 发布信息

- 服务名称：Deepseek-Harness社区版
- 服务 ID：`service-252a17b1e4cc425ca5f0`
- 部署物 ID：`artifact-349fae3ffa414d47a0e3`
- 正式版：版本 `4`，状态 `Online`（`absolute_redirect off` 修复尚未随正式版发布）
- 测试版（beta）：版本 `7`，部署物版本 `7`，镜像 `m-j6cd3yzglnel5rap11ar`
- 已下线验证版本：部署物版本 `6`（`m-j6c997kvvwy8j1k4n46t`，同样包含上述修复）
- 主访问端口：`3080`（安全代理访问）
- 内部应用端口：`13080`

## 已知事项

- dsh 0.2.x 的接口不再是 `/api/<method>` 形式的 HTTP JSON-RPC，统一改走 `/api/remote.mux` 长连接；模板与文档的健康检查均已改用该端点的 WebSocket 握手。
- 工作区选择器以 `HOME`（`/opt/deepseek-harness/home`）为根且只能逐级浏览，`template.yaml` 在部署时把 `/opt/deepseek-harness/workspace` 软链到 `home/workspace`，镜像构建脚本也做了同样的软链，供独立使用镜像时生效。
- 已有 dsh 签名 Cookie 的浏览器会跳过首次 302 引导，回归验证首次访问时必须定向清除应用 Cookie，不要清空整个浏览器登录态。

## 文档入口

用户交付文档请参见 [docs/index_ecs_single.md](../docs/index_ecs_single.md)。
