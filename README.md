# 西南大学统一身份认证登录脚本

> ⚠️ **实验性质**：本项目依据仓库保留的统一身份认证前端材料实现登录客户端。本次文档整理仅核对仓库代码，未验证学校当前部署、真实账号登录或协议兼容性；使用前请自行确认适用范围并遵守学校规则。

当前实现提供交互式命令行和 `LoginClient` API：初始化会话、下载图形验证码、由用户手动输入验证码、使用代码中记录的 RSA 公钥加密密码，再提交登录请求。验证码与登录请求共用同一 Session。

---

## 快速开始

### 安装

```bash
# 使用 pip
pip install -e .

# 或使用 uv（推荐）
uv sync
```

### 交互式命令行

安装后在仓库根目录运行：

```bash
python src/login.py
# 或使用 uv
uv run python src/login.py
```

按提示输入账号和密码，打开程序下载到当前工作目录的 `captcha.png`，再手动输入图形验证码。密码通过 `getpass` 读取。默认生成的仓库根目录 `captcha.png` 已加入 Git 忽略规则；自定义输出路径仍需自行保护。

**注意：当前 CLI 会输出完整登录响应，成功时还会输出 Cookie。** 这些输出可能包含敏感信息，请勿上传、转发或保存到公开日志；不要把真实账号登录作为自动测试。

### Python API

公开入口为 `LoginClient`，未提供顶层 `login(...)` 函数：

```python
from getpass import getpass
from swu_auth import LoginClient

username = input("账号: ").strip()
password = getpass("密码: ")
client = LoginClient()
client.initialize()
captcha_path = client.download_captcha()
print(f"请打开验证码图片: {captcha_path.resolve()}")
verify_code = input("验证码: ").strip()
result = client.login(username, password, verify_code)
print(f"响应码: {result.get('code')}")
```

`LoginClient.login()` 返回服务器响应字典，不是 `(success, message)` 元组。当前 CLI 以响应 `code == 200` 判断成功；验证码识别和重试由调用方处理，仓库没有内置 OCR、`captcha_solver` 或 `max_retries` 参数。修改连接配置时可向 `LoginClient(settings=Settings(...))` 传入 [Settings](src/swu_auth/config.py)。

## 实现范围

1. **会话初始化**：访问配置中的登录页并保留 Session Cookie。
2. **验证码下载**：从配置服务器的 `/getVerifyCode` 下载 PNG，由用户读取并输入。
3. **密码加密**：使用 [crypto.py](src/swu_auth/crypto.py) 中记录的 RSA 公钥和前端兼容编码方式；不会在运行时重新获取公钥。
4. **登录请求**：向配置服务器的 `/sso/doLogin` 提交账号、加密密码、验证码和协议字段。

协议、公钥或学校页面变化都可能影响兼容性。静态材料与离线测试不能证明真实登录仍可用。

## 项目结构

```
.
├── src/
│   ├── login.py           # 交互式命令行
│   └── swu_auth/
│       ├── __init__.py
│       ├── client.py      # 登录客户端
│       ├── crypto.py      # RSA 加密
│       └── config.py      # 配置管理
├── tests/                 # 单元测试
├── pyproject.toml         # 项目配置和依赖
└── README.md
```

## 环境要求

- 包元数据声明 Python 3.10+，以 [pyproject.toml](pyproject.toml) 为准；此要求不表示本次已验证所有 Python 版本。
- 当前依赖声明为 `requests>=2.31`、`pycryptodome>=3.20` 和 `pytest>=8.0`；`pytest` 用于测试。
- 未声明或使用 Pillow / ddddocr；验证码由用户手动输入。

## 离线测试

```bash
python -m pytest
# 或使用 uv
uv run python -m pytest
```

现有测试覆盖登录页 URL、请求字段与密码编码，使用合成数据且不发起真实登录。通过离线测试不能替代学校当前环境中的兼容性验收。

## 注意事项

- ⚠️ 本项目用于技术研究和学习，请遵守学校相关规定
- ⚠️ 登录协议可能随时变化，需要持续维护
- ⚠️ 切勿将账号密码硬编码到代码中
- ⚠️ 建议通过环境变量或安全配置管理凭证

## 相关项目

- **[swu-checkin](https://github.com/Sorynthia/swu-checkin)** - 钉钉查寝自动打卡脚本
- **[swudk-dingtalk](https://github.com/Sorynthia/swudk-dingtalk)** - 钉钉扫码打卡前端工具

## 贡献指南

欢迎提交 Issue 和 Pull Request！请查看 [CONTRIBUTING.md](CONTRIBUTING.md) 了解详细信息。

## 引用与归属

如果你在项目中使用或参考了本代码，建议按以下方式标注：

```
基于 Sorynthia/swu-login 开发
GitHub: https://github.com/Sorynthia/swu-login
```

本项目采用 MIT 许可证，欢迎使用和修改，但请保留原作者信息。

## 许可证

MIT License
