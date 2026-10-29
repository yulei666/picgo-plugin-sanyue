# PicGo Plugin - Sanyue CloudFlare ImgBed

一个用于 PicGo / PicList 的 [Sanyue CloudFlare ImgBed](https://github.com/MarSeventh/CloudFlare-ImgBed) 图床上传插件，支持多种上传渠道。

> 本插件基于 [gunanbei/picgo-plugin-sanyue](https://github.com/gunanbei/picgo-plugin-sanyue)（npm: `picgo-plugin-uploader-sanyue-imgbed`）修改，增加了上传文件夹占位符、输入校验等功能，以独立的包名发布。
>
> 与原插件使用不同的图床 ID（`sanyue-cloudflare-imgbed`），两者可以同时安装互不影响；从原插件迁移过来时需要重新填写一次配置。

## 功能特性

- 🚀 支持多种上传渠道（Telegram、CF-R2、S3）
- 🔐 基于 Token 的身份验证
- 📁 支持自定义上传文件夹，支持 `{Y}/{m}/{d}` 等时间占位符
- 🏷️ 支持选择服务端文件命名策略
- 🗜️ 支持开关服务端压缩（Telegram 渠道）
- 📱 简单易用的配置界面
- 🎯 自动处理图片上传和URL生成
- 🔄 支持完整URL返回格式

## 安装

### 方法一：通过 PicGo 命令行安装（推荐）

```bash
picgo install sanyue-cloudflare-imgbed
```

### 方法二：通过 PicGo / PicList 插件市场安装

1. 打开 PicGo / PicList 应用
2. 进入 `插件设置`
3. 搜索 `sanyue-cloudflare-imgbed`
4. 找到 `sanyue-cloudflare-imgbed` 插件并点击安装

> **注意**：搜索时不要包含 `picgo-plugin-` 前缀。

### 方法三：使用 npm 全局安装

```bash
npm install -g picgo-plugin-sanyue-cloudflare-imgbed
```

### 方法四：安装到 PicGo GUI 目录

```bash
cd ~/Library/Application\ Support/picgo  # macOS
# cd ~/.config/picgo  # Linux
# cd %APPDATA%\picgo  # Windows

# cd ~/Library/Application\ Support/piclist  # PicList (macOS)
npm install picgo-plugin-sanyue-cloudflare-imgbed --save
```

## 配置说明

### 1. 获取 SanYue 图床配置

在使用本插件之前，您需要：

1. 注册 SanYue 图床账号
2. 获取 API 地址和 Token
3. 选择合适的上传渠道

### 2. 插件配置

在 PicGo 的图床设置中找到 `Sanyue CloudFlare ImgBed`，点击配置：

#### 必需配置项

| 配置项 | 说明 | 示例 |
|--------|------|------|
| **API地址** | SanYue 图床的 API 地址（不包含 /upload） | `https://api.sanyue.com` |
| **Token** | 拥有上传权限的认证 Token | `your_token_here` |

#### 可选配置项

| 配置项 | 说明 | 默认值 | 选项 |
|--------|------|--------|------|
| **上传渠道** | 选择图片存储的渠道 | `telegram` | TG、CF-R2、S3 |
| **上传目标文件夹** | 指定图片上传到的文件夹路径 | 空字符串（根目录） | 任意文件夹路径 |
| **命名策略** | 服务端保存文件时的命名方式 | 默认（前缀_原名） | 默认、仅前缀、仅原名、短链接 |
| **服务端压缩** | 是否由服务端压缩图片，仅对 Telegram 渠道生效 | 开启 | 开启、关闭 |

### 3. 上传渠道说明

#### Telegram (TG)
- **特点**: 免费、稳定、速度快
- **适用**: 个人博客、文档站点

#### CF-R2
- **特点**: 基于 Cloudflare R2 存储
- **适用**: 需要 CDN 加速的场景
- **优势**: 全球访问速度快

#### S3
- **特点**: 兼容 Amazon S3 协议
- **适用**: 企业级应用
- **优势**: 高可用性和可扩展性

### 4. 文件夹管理

#### 上传目标文件夹配置
- **根目录**: 留空或输入空字符串，图片将上传到根目录
- **自定义路径**: 输入文件夹路径，如 `images/blog` 或 `photos/2024`
- **路径格式**: 支持多级目录，使用 `/` 分隔（`\` 会自动转换为 `/`，首尾多余的 `/` 和空格会被去掉）
- **特殊字符**: 中文、空格、`&`、`#` 等字符会自动进行 URL 编码，可以直接使用
- **示例**: 
  - `images` - 上传到 images 文件夹
  - `photos/2024/january` - 上传到多级目录
  - `blog/{Y}/{m}` - 按年月自动归档，如 `blog/2026/10`

#### 占位符

与 PicList 高级重命名的占位符保持一致，按上传时的本地时间替换：

| 占位符 | 含义 | 示例 |
|--------|------|------|
| `{Y}` | 4 位年份 | `2026` |
| `{y}` | 2 位年份 | `26` |
| `{m}` | 月（补零） | `10` |
| `{d}` | 日（补零） | `27` |
| `{h}` | 时（24 小时制，补零） | `09` |
| `{i}` | 分（补零） | `05` |
| `{s}` | 秒（补零） | `30` |
| `{ms}` | 毫秒（3 位） | `042` |
| `{timestamp}` | 秒级时间戳 | `1793073533` |

花括号必须成对出现，且只能包含上表中的占位符。花括号不匹配（如 `blog/{Y`）或使用了不支持的占位符（如 `{YY}`）时，上传会直接报错并给出提示，不会上传到错误的目录。

### 5. 命名策略

| 选项 | 说明 | 示例 |
|------|------|------|
| 默认（前缀_原名） | 时间戳前缀 + 原文件名 | `1793281376123_photo.png` |
| 仅前缀 | 只用时间戳前缀 | `1793281376123.png` |
| 仅原名 | 保留原文件名 | `photo.png` |
| 短链接 | 8 位随机字符 | `aB3dE9xQ.png` |

同名文件已存在时，服务端会自动在文件名后追加 `(1)`、`(2)` 等编号。

### 6. 服务端压缩

仅对 Telegram 渠道的图片生效：

- **开启**（默认）：图片以照片形式发送到 Telegram，会被压缩
- **关闭**：图片以文件形式发送到 Telegram，保留原图画质

其他渠道不受此选项影响。

## 使用说明

### 1. 基本使用

1. 完成上述配置后，在 PicGo 主界面选择 `Sanyue CloudFlare ImgBed` 作为默认图床
2. 拖拽图片到 PicGo 或使用快捷键上传
3. 上传成功后会自动复制图片链接到剪贴板

### 2. 批量上传

1. 选择多张图片
2. 拖拽到 PicGo 界面
3. 等待所有图片上传完成
4. 所有链接会自动复制到剪贴板

### 3. 快捷键上传

- **Windows/Linux**: `Ctrl + Shift + P`
- **macOS**: `Cmd + Shift + P`

### 4. 文件夹管理功能

#### 配置上传文件夹
1. 在插件配置中找到"上传目标文件夹"选项
2. 输入您想要的文件夹路径
3. 支持多级目录，使用 `/` 分隔
4. 留空表示上传到根目录

#### 文件夹路径示例
```
# 上传到根目录
（留空）

# 上传到单级文件夹
images

# 上传到多级文件夹
photos/2024/january
blog/images
```

#### 注意事项
- 文件夹路径区分大小写
- 建议使用英文和数字，避免特殊字符
- 如果文件夹不存在，系统会自动创建

## 故障排除

### 常见问题

#### 1. 上传失败
- **检查网络连接**: 确保网络正常
- **验证 Token**: 确认 Token 有效且有上传权限
- **检查 API 地址**: 确保地址正确且可访问

#### 2. 返回解析失败
- **检查 JsonPath 设置**: 默认为 `src`
- **联系管理员**: 如果问题持续存在

#### 3. 图片无法显示
- **检查图片链接**: 确认返回的 URL 格式正确
- **验证上传渠道**: 确认选择的渠道可用

#### 4. 文件夹相关问题
- **文件夹不存在**: 系统会自动创建不存在的文件夹
- **路径错误**: 检查文件夹路径格式，使用 `/` 分隔多级目录
- **提示"上传目标文件夹配置错误"**: 检查花括号是否成对、占位符是否在支持列表中
- **权限问题**: 确认 Token 有对应文件夹的写入权限

#### 5. 插件市场搜索不到
- **搜索关键词**: 使用 `sanyue-cloudflare-imgbed`，不要包含 `picgo-plugin-` 前缀
- **npm 索引延迟**: 新发布的版本可能需要 10-60 分钟才能在搜索中显示
- **命令行安装**: 如果搜索不到，可以直接使用命令 `picgo install sanyue-cloudflare-imgbed` 安装
- **清除缓存**: 尝试重启 PicGo 应用或清除应用缓存

### 错误代码说明

| 错误信息 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `Can't find uploader config` | 配置未完成 | 检查并完成所有必需配置 |
| `上传失败` | 网络或服务器问题 | 检查网络连接和服务器状态 |
| `返回解析失败` | API 返回格式变化 | 联系插件开发者 |

## 开发信息

- **包名**: `picgo-plugin-sanyue-cloudflare-imgbed`
- **图床 ID**: `sanyue-cloudflare-imgbed`
- **版本**: 1.1.0
- **许可证**: MIT
- **GitHub**: [https://github.com/yulei666/picgo-plugin-sanyue](https://github.com/yulei666/picgo-plugin-sanyue)
- **NPM**: [https://www.npmjs.com/package/picgo-plugin-sanyue-cloudflare-imgbed](https://www.npmjs.com/package/picgo-plugin-sanyue-cloudflare-imgbed)
- **原项目**: [gunanbei/picgo-plugin-sanyue](https://github.com/gunanbei/picgo-plugin-sanyue)

## 更新日志

### picgo-plugin-sanyue-cloudflare-imgbed v1.1.0
- ✨ 新增「命名策略」选项，可选默认、仅前缀、仅原名、短链接
- ✨ 新增「服务端压缩」选项，可关闭 Telegram 渠道的图片压缩

### picgo-plugin-sanyue-cloudflare-imgbed v1.0.1
- 🐛 修复 PicList / PicGo 图床设置页提示"加载配置失败"的问题

### picgo-plugin-sanyue-cloudflare-imgbed v1.0.0
- 🎉 以新包名 `picgo-plugin-sanyue-cloudflare-imgbed` 独立发布，图床 ID 改为 `sanyue-cloudflare-imgbed`
- ✨ 上传文件夹支持 `{Y}` `{y}` `{m}` `{d}` `{h}` `{i}` `{s}` `{ms}` `{timestamp}` 占位符
- ✅ 校验花括号匹配和占位符合法性，非法配置在上传前报错
- 💡 设置页提供上传文件夹和占位符说明
- 🐛 上传文件夹做 URL 编码并规范化路径，修复含 `&`、`#`、中文等字符时上传异常
- 🐛 修复 API 地址末尾带 `/` 时拼出 `//upload` 的问题
- 🐛 上传失败时显示真实错误信息，不再被当作上传成功

### 以下为原插件 picgo-plugin-uploader-sanyue-imgbed 的更新日志

### v1.1.9 (2025-10-16)
- 🐛 **重要修复**：移除错误的依赖配置，修复插件安装失败的问题
- 📦 更新包名为 `picgo-plugin-uploader-sanyue-imgbed`
- 🔍 优化 npm 搜索关键词，提升插件可发现性
- 📝 完善 README 文档和安装说明

### v1.1.8 (2025-10-16)
- 🔧 清理依赖项，移除不存在的包引用
- ⚡ 优化包体积

### v1.1.6 (2025-10-16)
- 🐛 修复依赖问题

### v1.1.5 (2025-10-16)
- 🔄 首次发布到 npm registry

### v1.1.1
- ✨ 新增上传目标文件夹配置功能
- 🔄 支持完整URL返回格式（returnFormat=full）
- 🐛 修复上传渠道选择问题
- ⚡ 优化错误处理机制
- 🎨 改进配置界面

### v1.0.0
- 🎉 初始版本发布
- 📤 支持基本图片上传功能
- 🔀 支持多种上传渠道（Telegram、CF-R2、S3）
- 🔐 基于Token的身份验证

## 发布流程

本仓库通过 GitHub Actions（`.github/workflows/publish.yml`）自动发布到 npm，使用 npm Trusted Publishing，无需配置 npm token：

1. 修改 `package.json` 中的 `version` 并更新本 README 的更新日志，提交并推送
2. 在 GitHub 上以 `v<版本号>` 为 tag 创建 Release（如 `v1.0.1`）
3. Release 发布后 workflow 自动校验 tag 与 `package.json` 版本一致，然后执行 `npm publish`

也可以在 Actions 页面手动运行该 workflow，发布所选分支上的当前版本。

## 贡献

欢迎提交 Issue 和 Pull Request 来帮助改进这个插件！

## 许可证

MIT License - 详见 [LICENSE](License) 文件
