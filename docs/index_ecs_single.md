# Deepseek-Harness社区版计算巢部署文档

## 服务说明

Deepseek-Harness社区版是 DeepSeek AI 开源的 Agent Harness，提供浏览器 Web UI，可在页面中配置 DeepSeek 或 OpenAI 兼容模型，并选择工作区后运行代码代理任务。本计算巢服务采用单机 ECS 部署，ECS 镜像内已预置 Node.js 24.22.2、DeepSeek Harness（0.2.1-rc.5）Web UI 和 Nginx 反向代理。

请通过计算巢控制台服务实例详情页中的安全代理入口访问 Web UI，不要直接访问输出中的私网地址或对公网开放 3080 端口。

## 版本更新

当前部署版本为 **DeepSeek Harness 0.2.1-rc.5**。相较于此前的 0.1.0-rc.6，主要功能与使用体验变化如下：

| 功能 | 更新内容 |
| --- | --- |
| 模型配置 | 自定义模型 API 支持在页面中设置各模型的推理等级；可配置输入类型、上下文窗口与最大输出 token 数，具体能力取决于所用模型。 |
| 自动化任务 | 自动化任务成为 Web 内置能力；提醒工具在标准、创造和 PTC 模式中可用，在极简模式和子代理中不可用。 |
| 插件管理 | 支持通过自定义插件索引源发现和安装插件；新增「让 Agent 创建插件」入口，发送需求后开始执行。 |
| 长对话与草稿 | 可用 `/compact` 指令手动压缩上下文；恢复草稿或切换工作区时保留文件、目录和会话引用。 |
| 消息展示 | 已发送消息中的密钥和令牌默认脱敏、按需查看；0.2.1-rc.5 优化了对话中思考内容的展示。 |
| 实验性扩展 | 提供 Git Worktrees、思考过程机器翻译和开发者工具插件，需在插件页按需安装或启用，不代表默认已开启。 |

Git Worktrees 插件可让 Agent 创建并进入独立的 Git checkout；思考过程翻译可选择 Bing、Google 或付费 DeepSeek Flash；开发者工具提供会话原始日志和调试入口。实验性功能可能随版本调整，使用前请查看对应插件说明。

详细变更见 [0.2.1-rc.1 累积更新说明](https://github.com/deepseek-ai/deepseek-harness/releases/tag/dsh-v0.2.1-rc.1)与 [0.2.1-rc.5 发布说明](https://github.com/deepseek-ai/deepseek-harness/releases/tag/dsh-v0.2.1-rc.5)。

## 服务架构

本服务模板构建出的服务为单机 ECS 部署。DeepSeek Harness Web UI 在实例内部监听 `127.0.0.1:13080`，Nginx 监听 `0.0.0.0:3080` 并反向代理到本地 Web UI，计算巢安全代理基于服务实例输出地址访问 3080 端口。

<img src="architecture_ecs_single.png" width="600" height="400" align="bottom"/>

## 计费说明

通过此服务模板构建服务不产生费用。用户部署服务实例时，资源费用主要涉及：

- ECS 实例规格
- 系统盘容量

计费方式支持按量付费和包年包月，预估费用会在部署前实时展示。

## RAM账号所需权限

若使用 RAM 用户创建服务实例，需要在创建服务实例前为该 RAM 用户添加相应权限。

| 权限策略名称 | 备注 |
| --- | --- |
| AliyunECSFullAccess | 管理云服务器 ECS 的权限 |
| AliyunVPCFullAccess | 管理专有网络 VPC 的权限 |
| AliyunROSFullAccess | 管理资源编排 ROS 的权限 |
| AliyunComputeNestUserFullAccess | 管理计算巢服务用户侧资源的权限 |

## 服务实例部署流程

### 部署步骤

1. 打开计算巢服务实例创建页面，选择部署地域并填写实例规格、实例密码和网络参数。

   [部署链接](https://computenest.console.aliyun.com/service/instance/create/cn-hangzhou?type=user&ServiceId=service-252a17b1e4cc425ca5f0)

2. 确认询价明细后点击 **下一步：确认订单**，同意服务协议并点击 **立即创建**。

3. 等待服务实例部署完成。部署完成后，在服务实例详情页的 **输出** 区域查看 `WebUIAddress`。

4. 点击输出中的安全代理访问地址打开 Web UI。页面应展示 DeepSeek Harness 的新会话、设置和选择工作区入口。

![Deepseek Harness Web UI](deploy-test-webproxy-deepseek-harness.png)

## 首次使用配置

Deepseek Harness Web UI 打开后还不能直接发起会话，必须先完成模型和工作区配置。

### 1. 配置大模型

首次打开会弹出 **开始使用** 引导，点击 **添加 API Key** 可直接进入模型配置；也可以点击左下角 **更多 → 设置 → 模型**。

![配置模型](set-model.png)

1. 在 **模型** 页签中，页面会列出 DeepSeek 提供方（`deepseek-official`）与「添加模型提供商」入口。
2. 任选一种方式完成模型接入：
   - 填写 DeepSeek API Key，使用 DeepSeek 官方模型。
   - 点击 **添加模型提供商 → 第三方模型提供商**，从内置目录（OpenAI、Anthropic、Kimi、Qwen 等）中选择并填写 API Key。
   - 点击 **添加模型提供商 → 自定义模型 API**，填写 Base URL、API Key 与模型 ID。
3. 新增提供商时点击 **创建**，编辑已有提供商时点击 **保存** 完成配置。

未配置模型 API Key 时，Web UI 可以打开，但无法正常发起模型请求。

#### 示例：接入阿里云百炼 qwen-plus

1. 在 **设置 → 模型** 中点击 **添加模型提供商 → 自定义模型 API**。
2. 填写以下配置。API Key 请使用您在百炼控制台申请的密钥，不要将密钥写入对话或公开文件。

    | 字段 | 值 |
    | --- | --- |
    | Provider ID | `bailian` |
    | 显示名称 | `阿里云百炼` |
    | API 地址 | `https://dashscope.aliyuncs.com/compatible-mode/v1` |
    | API 协议 | `OpenAI Chat Completions` |
    | API 密钥 | 您自己的百炼 API Key |
    | 模型 ID | `qwen-plus` |
    | 模型显示名称 | `Qwen Plus` |

3. 添加上述模型后点击 **创建**；修改已有提供商时点击 **保存**。
4. 完成工作区选择后，在会话输入框的模型选择器中选择 **Qwen Plus**。

该示例使用百炼中国内地兼容接口，请使用对应地域的 API Key。其他地域或网关请以提供商文档中的 API 地址和协议为准。保存配置或检测到凭据不代表模型调用已成功，需通过实际对话确认。

### 2. 选择工作区

1. 返回主页面，点击 **选择工作区**。
2. 在目录选择器中进入 **workspace**，点击 **打开**。

![选择工作区](workspace.png)

3. 工作区选择完成后，新会话输入框才可正常使用。

![开始会话](chat.png)

服务实例已在 ECS 上准备 `/opt/deepseek-harness/workspace` 作为默认工作目录，并在主目录下提供同名入口供目录选择器直接选取；DeepSeek Harness 的会话、凭据、存储和附件数据保存在 `/opt/deepseek-harness/dsh-home` 下。

### 3. 开始会话

1. 确认工作区已选择，并在模型选择器中选择已配置的模型。
2. 输入任务并发送，例如：`请概述当前工作区的目录结构，不要修改文件。`
3. 等待模型回复。只有收到实际回复，才说明模型鉴权和调用成功；若请求失败，请核对 API Key、API 地址、协议、模型 ID 及实例出网连接。

长对话需要压缩上下文时，可在输入框使用 `/compact` 指令。

## 运维说明

如需登录 ECS 排查服务状态，可使用实例密码远程连接 ECS 后执行：

```bash
sudo systemctl status deepseek-harness.service
sudo systemctl status nginx.service
sudo journalctl -u deepseek-harness.service -n 100 --no-pager
```

服务启动脚本位于 `/opt/deepseek-harness/bin/start-dsh-web`，应用日志位于 `/opt/deepseek-harness/logs/service.log`。

## 注意事项

- DeepSeek Harness 当前为 developer preview，上游未来版本可能存在破坏性变更。
- 用户在 Web UI 中配置的模型 API Key 会保存在服务实例本地持久化目录中，请妥善管理实例访问权限。

## 官方链接

- [DeepSeek Harness GitHub](https://github.com/deepseek-ai/deepseek-harness)
