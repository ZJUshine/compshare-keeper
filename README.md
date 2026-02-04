# CompShare Keeper

自动化管理 CompShare 机器的 GitHub Action 项目。

## 功能

- ⏰ 每天自动对所有 CompShare 机器进行一次重启（关机再开机）
- 🔒 使用 GitHub Secrets 安全存储 API 密钥
- 📊 详细的执行日志
- 🎯 支持手动触发

## 快速开始

### 1. 获取 CompShare API 密钥

1. 登录 [CompShare 控制台](https://console.compshare.cn)
2. 进入 [API 管理](https://console.compshare.cn/uaccount/api_manage)
3. 获取你的 `public_key` 和 `private_key`

### 2. 配置 GitHub Secrets

在你的 GitHub 仓库中配置以下 Secrets：

1. 进入仓库的 `Settings` → `Secrets and variables` → `Actions`
2. 点击 `New repository secret` 添加以下密钥：

   - **Name**: `COMPSHARE_PUBLIC_KEY`
     **Value**: 你的 CompShare Public Key

   - **Name**: `COMPSHARE_PRIVATE_KEY`
     **Value**: 你的 CompShare Private Key

### 3. 启用 GitHub Actions

1. 进入仓库的 `Actions` 标签
2. 如果 Actions 未启用，点击启用
3. 找到 `Daily Machine Restart` 工作流

### 4. 配置执行时间

默认配置为每天 UTC 时间 02:00（北京时间 10:00）执行。

如需修改时间，编辑 `.github/workflows/daily-restart-machines.yml`:

```yaml
on:
  schedule:
    # cron 格式: 分 时 日 月 周
    # 示例：'0 14 * * *' = 每天 UTC 14:00 (北京时间 22:00)
    - cron: '0 2 * * *'
```

**常用时间示例**：
- `'0 2 * * *'` - 每天 UTC 02:00 (北京时间 10:00)
- `'0 6 * * *'` - 每天 UTC 06:00 (北京时间 14:00)
- `'0 14 * * *'` - 每天 UTC 14:00 (北京时间 22:00)

## 手动触发

如需立即执行重启任务：

1. 进入仓库的 `Actions` 标签
2. 选择 `Daily Machine Restart` 工作流
3. 点击 `Run workflow` 按钮
4. 选择分支后点击 `Run workflow`

## 工作原理

脚本执行流程：

1. 📋 获取所有 CompShare 机器列表
2. 🛑 关闭所有机器
3. ⏳ 等待机器完全关闭（最多 5 分钟）
4. ⏸️  等待 30 秒
5. 🚀 启动所有机器
6. ⏳ 等待机器完全启动（最多 5 分钟）
7. ✅ 完成

## 本地测试

你也可以在本地运行脚本进行测试：

### 1. 安装依赖

```bash
pip install --upgrade ucloud-sdk-python3
```

### 2. 设置环境变量

```bash
export COMPSHARE_PUBLIC_KEY="your_public_key"
export COMPSHARE_PRIVATE_KEY="your_private_key"
```

### 3. 运行脚本

```bash
python scripts/restart_machines.py
```

## 文件说明

```
.
├── .github/
│   └── workflows/
│       └── daily-restart-machines.yml  # GitHub Action 工作流配置
├── scripts/
│   └── restart_machines.py             # 重启机器的 Python 脚本
└── README.md                            # 项目说明文档
```

## 常见问题

### Q: 如何查看执行日志？

进入仓库的 `Actions` 标签，选择相应的运行记录查看详细日志。

### Q: 任务失败了怎么办？

1. 检查 GitHub Secrets 中的 API 密钥是否正确
2. 查看 Actions 日志中的错误信息
3. 确认 CompShare 账户状态正常
4. 尝试手动触发工作流进行调试

### Q: 可以只重启特定机器吗？

当前版本会重启所有机器。如需重启特定机器，可以修改 `scripts/restart_machines.py` 中的逻辑，根据机器名称或 ID 进行过滤。

### Q: 重启过程中机器会丢失数据吗？

CompShare 机器重启时：
- 系统盘数据会保留
- 内存中的数据会丢失
- 建议在低峰期执行重启任务

## 技术支持

- [CompShare 文档](https://www.compshare.cn/docs)
- [CompShare API 管理](https://console.compshare.cn/uaccount/api_manage)
- [UCloud Python SDK](https://github.com/ucloud/ucloud-sdk-python3)

## 许可证

MIT License
