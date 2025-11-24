# Code Switch

集中管理 Claude Code、Codex 和 Gemini CLI 的 AI 供应商配置

## 核心功能

- **平滑切换供应商** - 无需重启 Claude Code/Codex/Gemini CLI，实时切换不同供应商
- **智能降级机制** - 支持多供应商分级优先级调度（Level 1-10），自动故障转移
- **用量统计追踪** - 请求级别的 Token 用量统计和成本核算
- **MCP 服务器管理** - Claude Code 和 Codex 双平台 MCP Server 集中配置
- **技能市场** - Claude Skill 自动下载与安装，内置热门技能仓库
- **Gemini CLI 管理** - 支持 Google OAuth、API Key、PackyCode 等多种认证方式
- **深度链接导入** - 通过 ccswitch:// 协议一键导入供应商配置
- **速度测试** - 并发测试供应商端点延迟，优化选择
- **自定义提示词** - 管理 Claude/Codex/Gemini 的系统提示词
- **环境变量检测** - 自动检测并提示环境变量冲突

## 下载安装

[最新版本下载](https://github.com/Rogers-F/code-switch-R/releases)

- **Windows**: 下载 `CodeSwitch-amd64-installer.exe` 运行安装，或下载 `CodeSwitch.exe` 便携版
- **macOS (Apple Silicon)**: 下载 `codeswitch-macos-arm64.zip`，解压后拖入 Applications
- **macOS (Intel)**: 下载 `codeswitch-macos-amd64.zip`，解压后拖入 Applications

## 工作原理

应用启动时在本地 `:18100` 端口创建 HTTP 代理服务器，并自动配置 Claude Code 和 Codex 指向该代理。

代理暴露两个关键端点：
- `/v1/messages` → 转发到 Claude 供应商
- `/responses` → 转发到 Codex 供应商

请求由 `proxyHandler` 基于优先级分组动态选择 Provider：
1. 优先尝试 Level 1（最高优先级）的所有供应商
2. 失败后依次尝试 Level 2、Level 3 等
3. 同一 Level 内按用户排序依次尝试

这让 CLI 看到的是固定的本地地址，而请求被透明路由到你配置的供应商列表。

## 界面预览

![亮色主界面](resources/images/code-switch.png)
![暗色主界面](resources/images/code-swtich-dark.png)
![日志亮色](resources/images/code-switch-logs.png)
![日志暗色](resources/images/code-switch-logs-dark.png)

## 开发指南

### 环境要求
- Go 1.24+
- Node.js 18+
- Wails 3 CLI: `go install github.com/wailsapp/wails/v3/cmd/wails3@latest`

### 开发运行
```bash
wails3 task dev
```

### 构建

```bash
# 更新构建元数据
wails3 task common:update:build-assets

# 打包当前平台
wails3 task package
```

### 交叉编译 Windows (macOS)

```bash
brew install mingw-w64
env ARCH=amd64 wails3 task windows:build
env ARCH=amd64 wails3 task windows:package
```

## 发布

推送 tag 即可触发 GitHub Actions 自动构建：

```bash
git tag v1.0.0
git push origin v1.0.0
```

自动构建产物：
- `codeswitch-macos-arm64.zip`
- `codeswitch-macos-amd64.zip`
- `CodeSwitch-amd64-installer.exe`
- `CodeSwitch.exe`

## 常见问题

- **macOS 无法打开 .app**: 先执行 `wails3 task common:update:build-assets` 再构建
- **交叉编译权限问题**: macOS 终端需要完全磁盘访问权限

## 技术栈

- **后端**: Go 1.24 + Gin + SQLite
- **前端**: Vue 3 + TypeScript + Tailwind CSS
- **框架**: [Wails 3](https://v3.wails.io)

## License

MIT
