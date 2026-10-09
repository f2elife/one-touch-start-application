# one-touch-start-application

用于一键启动服务的程序（Electron 桌面应用）。

## 本地开发

```bash
npm install          # 安装依赖（国内网络慢时：
                     # ELECTRON_MIRROR="https://npmmirror.com/mirrors/electron/" npm install）
npm start            # 启动应用
```

## 本地打包

```bash
npm run build:mac    # macOS universal 安装包（Intel + Apple Silicon 通用，dmg + zip）
npm run build:win    # Windows x64 安装包（NSIS）
```

产物输出到 `dist/` 目录。

## GitHub 自动打包

推送代码到 `main` 分支后，GitHub Actions 会自动在云上构建：

- **macOS**：`macos-latest` 环境构建 **universal** 安装包（`OneTouchStart-<版本>-universal.dmg` / `.zip`）
- **Windows**：`windows-latest` 环境构建 x64 安装包（`OneTouchStart Setup <版本>.exe`）

两种触发方式：

1. **普通推送**：`git push origin main` —— 构建安装包并上传为 Actions artifact（在仓库 Actions 页面下载）
2. **发布版本**：`git tag v1.0.0 && git push origin v1.0.0` —— 构建并自动创建 GitHub Release（在 Releases 页面下载）

也可以在仓库 Actions 页面手动点击 "Run workflow" 触发。

## 代码签名（可选，推荐发布前配置）

未配置签名时，安装包为未签名状态，Windows 安装时会有 SmartScreen 提示、macOS 首次打开需右键"打开"。

在仓库 **Settings → Secrets and variables → Actions** 配置：

| Secret | 用途 |
| --- | --- |
| `CSC_LINK` | Apple Developer 证书 `.p12` 的 base64 |
| `CSC_KEY_PASSWORD` | 上述证书密码 |
| `APPLE_ID` | Apple 账号（用于公证 notarization） |
| `APPLE_APP_SPECIFIC_PASSWORD` | Apple 专用密码 |
| `APPLE_TEAM_ID` | Apple 开发者团队 ID |
| `WIN_CSC_LINK` | Windows 代码签名证书 `.pfx` 的 base64 |
| `WIN_CSC_KEY_PASSWORD` | 上述证书密码 |

配置后推送即自动签名 + macOS 公证，无需修改工作流文件（`.github/workflows/build.yml`）。

## 项目结构

```
main.js                  Electron 主进程（创建窗口）
preload.js               预加载脚本（安全桥接）
index.html               页面
electron-builder.yml     打包配置（universal / NSIS）
.github/workflows/build.yml   GitHub Actions 自动打包
```
