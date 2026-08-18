# EIDE cpptools 兼容构建

此分支基于上游 `github0null/eide` 的 `v3.27.2`，用于兼容旧版 EIDE 工作区结构和新版 Microsoft C/C++ 扩展。

## 修改内容

1. `provideFolderBrowseConfiguration` 不再要求工作区目录与 EIDE 项目目录完全相等。工作区目录包含 EIDE 项目目录时，同样向 cpptools 返回 browsePath。
2. `provideConfigurations` 在文件与项目映射缺失时主动调用项目匹配逻辑，兼容新版 cpptools 直接请求文件配置的行为。
3. 活动项目不在匹配列表中时，回退到第一个可用项目，避免返回空配置。
4. 子模块地址改为 HTTPS，便于本机构建和 GitHub Actions 拉取。

## 构建要求

- Windows、Linux 或 macOS
- Git（需要拉取子模块）
- Node.js 18
- npm（随 Node.js 18 提供）
- `@vscode/vsce` 2.15.0（由项目开发依赖提供，无需全局安装）

## 本地构建

```powershell
git clone --recurse-submodules https://github.com/hebbio/eide.git
Set-Location eide
npm ci --no-audit --no-fund
npm run vscode:prepublish
npx --no-install vsce package
```

打包时不要使用 `--no-dependencies`，否则 VSIX 会缺少 `x2js` 等运行时依赖，导致扩展激活失败。

## 自动发布

推送匹配 `v*-hebbio.*` 的标签后，`.github/workflows/custom-release.yml` 会自动：

1. 拉取源码和子模块。
2. 使用 Node.js 18 安装依赖。
3. 通过 webpack 构建扩展。
4. 打包包含运行时依赖的 VSIX。
5. 上传 Actions Artifact 并创建 GitHub Release。
