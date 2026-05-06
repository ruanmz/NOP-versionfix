# Next.js 16.0.7+ Cloudflare Pages 兼容补丁 README
本仓库提供的兼容补丁，旨在解决 `@cloudflare/next-on-pages` 暂不支持 Next.js 16.0.7+ 版本的问题，同时确保大家能够安全部署已修复 RSC 高危漏洞的 Next.js 项目到 Cloudflare Pages。
近期，Vercel 平台已对 Next.js 版本做出强制限制，所有 ≤16.0.6 的版本因存在 RSC 远程代码执行（RCE）高危漏洞（CVE-2025-66478），均无法正常部署。而与之对应的是，`@cloudflare/next-on-pages` 官方目前尚未完成对 Next.js 16.0.7+ 版本的兼容适配，默认会拦截该版本的项目构建，这就导致很多开发者陷入“想部署安全版本却被拦截”的困境。
为此，我们开发了这款无侵入式兼容补丁，核心作用仅为修改 `@cloudflare/next-on-pages` 的 peerDependencies 版本约束，将其从原本的特定版本限制，调整为 `>=14.3.0`，从而放行 Next.js 14.3.0 及以上所有稳定版本，尤其支持已修复高危漏洞的 16.0.7+ 版本。整个补丁不改动任何核心构建逻辑，也不侵入项目源码，完全遵循原生部署流程，确保使用安全、无风险。
## 快速使用
使用方法十分简单，无需复杂操作，只需按照以下步骤执行即可。
### 步骤1：获取补丁脚本
直接下载本仓库根目录的 **fix.js** 文件，将其放置到你的项目根目录下，确保文件路径正确（项目根目录/fix.js），避免后续执行时出现“文件找不到”的错误。
### 步骤2：配置自动执行脚本
为了确保每次安装依赖时，补丁能自动应用（无需手动重复操作），请在你的项目 `package.json` 文件中，添加 `postinstall` 脚本，具体配置如下：
```json
{
  "scripts": {
    "postinstall": "node fix.js"
  }
}
```
### 步骤3：安装依赖并自动打补丁
根据你使用的包管理工具，执行对应的依赖安装命令，安装完成后，补丁会自动运行并修改版本约束：
```bash
# pnpm 用户（推荐）
pnpm install

# npm 用户
npm install

# yarn 用户
yarn install
```
### 步骤4：升级到安全版 Next.js
将你的 Next.js 版本升级到 16.0.7 及以上（已修复 RSC 高危漏洞），同时更新 react 和 react-dom 到最新版本，确保项目安全：
```bash
pnpm add next@16.0.7 react@latest react-dom@latest
```
### 步骤5：构建并部署
完成以上配置后，正常执行构建命令，随后将项目部署到 Cloudflare Pages 即可，部署流程与原生流程完全一致：
```bash
pnpm build
```
## 成功标识
当补丁执行成功时，控制台会输出以下提示，说明版本约束已修改完成：
```bash
Successfully updated Next.js version constraint in peerDependencies:
  From: x.x.x
  To: >=14.3.0

✅ Compatibility patch for @cloudflare/next-on-pages applied successfully
```
## 重要说明
- 本补丁仅修改 `@cloudflare/next-on-pages` 的 peerDependencies 版本约束，不改动任何核心构建逻辑，不侵入项目源码，完全兼容 Cloudflare Pages 部署要求。
- 若后续 `@cloudflare/next-on-pages` 官方发布更新，原生支持 Next.js 16.0.7+ 版本，可直接删除项目根目录的 `fix.js` 文件，并移除 `package.json` 中的 `postinstall` 脚本，恢复为官方原生配置。
- 补丁适配 pnpm、npm、yarn 等主流包管理工具，同时支持 Cloudflare Pages CI 部署环境，无需额外调整配置。
## 故障排查
- 报错“文件不存在”：大概率是未先执行依赖安装命令，需先执行 `pnpm install`（或对应包管理工具的安装命令），再重新运行补丁。
- CI 环境补丁不生效：检查 `fix.js` 文件是否已提交到代码仓库，确保 CI 环境能正常获取到该脚本。
## 补充说明
本补丁旨在解决开发者“无法部署安全版本 Next.js”的紧急需求，保障项目线上安全，欢迎大家使用并反馈使用过程中遇到的问题。
