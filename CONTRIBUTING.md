# 贡献指南

感谢你考虑为本项目做出贡献！

## 如何贡献

### 报告问题

- 使用 GitHub Issues 报告 bug 或提出功能请求
- 提供清晰的标题和详细的描述
- 包含复现步骤、环境信息和预期行为

### 提交代码

1. Fork 本仓库
2. 创建你的功能分支 (`git checkout -b feature/amazing-feature`)
3. 提交你的修改 (`git commit -m 'Add some amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建 Pull Request

### 代码规范

- Python 代码遵循 PEP 8
- 提交信息使用清晰的中文或英文描述
- 添加必要的注释和文档

### Pull Request 要求

- 确保代码通过现有测试
- 对于新功能，添加相应的测试用例
- 更新相关文档
- 保持提交历史清晰

## 开发环境

项目为 Python 客户端，包元数据要求 Python 3.10+。在仓库根目录安装并运行现有离线测试：

```bash
python -m pip install -e .
python -m pytest
```

也可使用 uv：

```bash
uv sync
uv run python -m pytest
```

测试使用合成数据，不应发起真实学校登录。修改 HTTP 流程时应模拟响应，不加入真实账号、密码、Cookie 或验证码；不要上传 CLI 输出。当前离线测试范围见 [README](README.md#离线测试)。

`pyproject.toml` 中保留了 Ruff 规则配置，但当前依赖未包含 Ruff；上述命令不代表已执行 Ruff 检查。

## 行为准则

- 尊重所有贡献者
- 保持友好和专业的交流
- 接受建设性的批评
- 关注项目目标和用户需求

## 许可证

提交代码即表示你同意你的贡献使用项目的 MIT 许可证。
