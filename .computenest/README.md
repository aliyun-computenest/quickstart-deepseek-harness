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

镜像内为 dsh 0.2.1-rc.5（Node.js 24.22.2）。Web UI 内部监听 `127.0.0.1:13080`，Nginx 监听 `0.0.0.0:3080`，用户通过计算巢控制台服务实例详情页的安全代理入口访问。

## 当前版本功能

- 自定义模型 API 可在页面中设置推理等级、输入类型及模型容量参数。
- 自动化任务为 Web 内置能力；提醒工具支持标准、创造和 PTC 模式，不支持极简模式和子代理。
- 插件支持自定义索引源与「让 Agent 创建插件」入口。
- 支持 `/compact` 手动压缩上下文，恢复草稿或切换工作区时保留引用。
- 已发送消息中的密钥和令牌默认脱敏；0.2.1-rc.5 优化了思考内容展示。
- Git Worktrees、思考过程翻译和开发者工具属于实验性插件，需按需安装或启用。

版本变更说明、百炼 `qwen-plus` 配置示例和首次会话步骤见[中文部署文档](../docs/index_ecs_single.md)。

## 发布信息

- 服务名称：Deepseek-Harness社区版
- 服务 ID：`service-252a17b1e4cc425ca5f0`
- 部署物 ID：`artifact-349fae3ffa414d47a0e3`
- 正式版：服务版本 `6`，状态 `Online`
- 上游应用版本：`0.2.1-rc.5`
- 部署物版本：`7`
- 主访问端口：`3080`（安全代理访问）
- 内部应用端口：`13080`

## 已知事项

- dsh 0.2.x 的接口统一使用 `/api/remote.mux` 长连接。
- 工作区选择器以 `HOME`（`/opt/deepseek-harness/home`）为根且只能逐级浏览，`template.yaml` 在部署时把 `/opt/deepseek-harness/workspace` 软链到 `home/workspace`，镜像构建脚本也做了同样的软链，供独立使用镜像时生效。

## 文档入口

用户交付文档请参见 [docs/index_ecs_single.md](../docs/index_ecs_single.md)。
