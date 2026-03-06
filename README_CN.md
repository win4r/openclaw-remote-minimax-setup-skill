# OpenClaw 远程 MiniMax 部署 Skill

这是一个可复用的 OpenClaw Skill，用于通过 **SSH 在远程 Linux 服务器上部署并验证 OpenClaw**，目标场景包括：

- **MiniMax M2.1**
- **MiniMax 国内版 API 域名**
- **Telegram Bot 接入**
- **首次部署常见故障排查**

这个仓库沉淀的是一次真实成功部署后的方法、流程和踩坑经验，但已经去除了所有敏感信息。

## 这个 Skill 能做什么

它主要覆盖以下流程：

1. 先阅读 OpenClaw 官方文档，再动手部署
2. 通过 SSH 在远程服务器上安全安装 OpenClaw
3. 配置 `minimax/MiniMax-M2.1`
4. 接入 Telegram Bot
5. 显式处理 `gateway.mode=local`
6. 修复 `systemctl --user` 在远程 VPS 上常见的常驻问题（`loginctl enable-linger`）
7. 同时验证模型链路和 Telegram 链路

## 仓库结构

```text
.
├── README.md
├── README_CN.md
├── dist/
│   └── openclaw-remote-minimax-setup.skill
└── skill/
    └── openclaw-remote-minimax-setup/
        ├── SKILL.md
        └── references/
            ├── config-template.md
            ├── failure-modes.md
            └── workflow.md
```

## 这个 Skill 固化了哪些关键经验

这个 Skill 明确记录了几个非常容易踩的坑：

- `openclaw config validate` 通过，不代表网关一定能成功启动
- 如果缺少 `gateway.mode=local`，首次启动可能直接失败
- Telegram 如果返回 `chat not found`，通常是用户还没有先在 Bot 私聊里点 **Start**
- 如果远程部署使用的是 `systemctl --user`，而没有启用 **linger**，那么 SSH 断开后服务可能就没了

## 如何使用

### 方式一：直接使用源码 Skill 目录

把下面这个目录复制到你的 OpenClaw skills 目录，或者你自己的 skill 工作区：

```text
skill/openclaw-remote-minimax-setup/
```

### 方式二：使用打包后的 `.skill` 文件

如果你希望分发单文件产物，可以直接使用 `dist/` 目录中的 `.skill` 包。

## 公开仓库的安全说明

这个仓库是**脱敏后的公开版本**，不会包含：

- 真实服务器 IP
- 真实用户名或密码
- Telegram Bot Token
- MiniMax API Key
- Gateway Token

示例配置里全部使用占位符。

## 为什么要做这个 Skill

OpenClaw 的远程部署，难点往往不在“安装”，而在最后那几个特别容易忽略的细节：

- 运行时配置门槛
- provider / model 对不上
- Telegram 首次私聊行为
- VPS 上 user service 的持久化问题

这个 Skill 的目的，就是把这些真实经验变成一条稳定、可复用、可交付的流程。

## License

本仓库按现状提供，可自由参考、改写和复用。
