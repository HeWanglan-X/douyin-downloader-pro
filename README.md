# 抖音视频下载 Pro（Douyin Downloader Pro）

> ## ⚠️ 改版声明
> 本仓库为 [官方版《抖音视频下载（Douyin Downloader）》](https://greasyfork.org/zh-CN/scripts/574900)（原作者 [ArcherEmiya](https://github.com/W-ArcherEmiya)，MIT 协议）的**改版（Fork）**。
> 在官方版基础上修复了 1 个已知 Bug，作为**替代版本独立使用**；版本更新由本仓库自行维护，不受官方发布影响。

下载当前抖音网页视频，并支持在个人主页批量选择视频下载。

## 修复的 Bug

| 项 | 内容 |
|---|---|
| **现象** | 官方版悬浮下载按钮**左侧约 168px 宽的透明区域会屏蔽鼠标点击**。该区域恰好覆盖抖音视频右侧的点赞、展开评论等操作栏，导致不上下拖动按钮就无法点击页面原有功能。 |
| **原因** | 悬浮按钮的外层面板是一个 `position: fixed` 的矩形容器，其中预留了一块隐藏的"状态气泡"区域（`min-width: 168px`）。状态气泡自身虽然设置了 `pointer-events: none`，但**外层面板没有**，默认 `pointer-events: auto` —— 点击落在透明预留区时事件被面板截获，而面板没有任何点击处理逻辑，形成点击死区。 |
| **修复** | 给整个面板设置 `pointer-events: none`（点击穿透），仅保留下载按钮本身 `pointer-events: auto`。按钮外观、右侧悬浮、上下拖动、位置记忆等设计全部保持不变。 |

## 与原版的其他区别

1. **独立安装使用**：使用独立的 DOM id / CSS 类 / 动画名 / 本地存储键（前缀 `douyin-downloader-pro-`），与官方版互不干扰，可放心替换官方版。
2. **快捷键与官方一致**：本版为 `Q`（与官方版相同）。本版定位为官方版的替代，请勿与官方版同时启用，避免按 `Q` 时两个脚本重复触发。
3. **按钮自动避让**：检测到官方版悬浮按钮存在时，本版按钮自动向左偏移，避免两个按钮重叠；未检测到时保持贴边（right 16px）。
4. **不再被官方版覆盖**：移除了官方 GreasyFork 更新地址，官方发布新版本不会覆盖本分叉的修复；更新由本仓库自行维护。

## 兼容性（Manifest V3）

- Chrome / Edge 138+ 上的篡改猴 5.x 是 Manifest V3 扩展，用户脚本跑在隔离世界（`USER_SCRIPT`）里：对 `window.fetch`、`XMLHttpRequest` 做的补丁**只作用于那个副本，看不到页面自己的请求**，于是出现「悬浮按钮还在，但点下载没反应 / 解析不到地址」。
- 自 v0.2.4 起，脚本会向页面注入一小段桥接代码（页面世界执行，自动复用页面 `nonce`）：由它接管 `fetch` / XHR 抓取抖音自己的 aweme / feed 响应，再用 `postMessage` 回传给脚本；同时回传 `_ROUTER_DATA`、`RENDER_DATA`、`player` 等页面全局数据的 JSON 快照。
- 若站点 CSP 拒绝内联注入，桥接会静默失败并回落到同源 `aweme/detail` 接口解析，不影响手动下载。
- 需要保持扩展详情页里的「允许用户脚本」（Allow User Scripts）开关为开启，否则篡改猴不会注入任何脚本。

## 安装

**一键安装（推荐）**：<https://raw.githubusercontent.com/HeWanglan-X/douyin-downloader-pro/main/douyin-downloader-pro.user.js> —— 用已装脚本管理器的浏览器打开，篡改猴会弹出安装页。

其他渠道：

| 渠道 | 地址 | 说明 |
|---|---|---|
| GitHub 主仓库 | 上面的 raw 链接 | 更新最快，`@updateURL` 指向这里 |
| GitHub Release | <https://github.com/HeWanglan-X/douyin-downloader-pro/releases/latest> | 附带 `douyin-downloader-pro.user.js` 与 `douyin-downloader-pro-<版本>.zip` 发布包 |
| Greasy Fork / 脚本猫 / OpenUserJS | 上架中，见仓库 Issues 公告 | 同时维护，版本与 GitHub 对齐 |

手动安装：

1. 安装 [Tampermonkey](https://www.tampermonkey.net/)（或其他支持 `GM_*` API 的油猴扩展）。
2. 打开油猴管理面板 → 新建脚本，将 `douyin-downloader-pro.user.js` 的内容粘贴进去，保存。
3. **停用或删除官方版脚本**（避免两个脚本同时响应 `Q` 键）。
4. 打开抖音网页刷新即可。

## 自动更新

- 脚本头部包含 `@updateURL` / `@downloadURL`，指向本仓库 `main` 分支（GitHub raw）。
- Tampermonkey 默认约每天检查一次自动更新（可在「设置 → 通用 → 更新」中调整检查周期）。
- 也可以随时在油猴面板手动点击「检查更新」立即拉取最新版。
- 注意：`raw.githubusercontent.com` 在部分地区访问可能不稳定；如遇更新失败，可手动从本仓库重新导入。

## 使用

- 在视频页点击右侧悬浮下载按钮，或按 `Q` 键下载当前视频。
- 按钮可上下拖动，位置会被记住。
- 在个人主页点击按钮，可批量扫描并选择下载视频（支持选择保存文件夹、搜索筛选、全选/全不选）。

## 更新记录

| 版本 | 内容 |
|---|---|
| **v0.2.8** | 文件名不跟随当前视频、整批同名；长视频偶发下载失败需重试；分片失败自动重试并回落单流 |
| **v0.2.7** | 下载提速：自动选更快源 + 多线程分片并发；进度显示实时速度；批量间隔缩短 |
| **v0.2.6** | 视频页文件名取成推荐视频标题；连点下载重复保存同一视频 |
| **v0.2.5** | 部分视频（MSE/`blob:` 播放）点下载没反应、解析不到地址 |
| **v0.2.4** | 适配 Manifest V3（Chrome/Edge + 篡改猴 5.x）；一并发布无水印选源 |
| **v0.2.3** | 无水印选源：优先干净播放地址，水印流排后 |
| **v0.2.2** | 界面汉化：悬浮按钮、状态提示、批量下载弹窗 |
| v0.2.1 | 恢复 `@updateURL` / `@downloadURL`，支持通过本仓库自动更新。 |
| v0.2.0 | 转正为替代版本：快捷键恢复为 `Q`（与官方一致），独立 DOM/存储键，README 改版声明。 |
| v0.1.0 | 分叉版本：修复悬浮按钮透明区域拦截鼠标点击的 Bug。 |

## 开源协议

[MIT](LICENSE)

- 原作者：[ArcherEmiya](https://github.com/W-ArcherEmiya)（官方版，MIT）
- 分叉维护：[HeWanglan-X](https://github.com/HeWanglan-X)
