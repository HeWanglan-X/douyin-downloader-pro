# 抖音视频下载 Pro（Douyin Downloader Pro）

> ## ⚠️ 改版声明
> 本仓库为 [官方版《抖音视频下载（Douyin Downloader）》](https://greasyfork.org/zh-CN/scripts/574900)（原作者 [ArcherEmiya](https://github.com/W-ArcherEmiya)，MIT 协议）的**改版（Fork）**。
> 在官方版基础上修复了 1 个已知 Bug，并改造为可与之共存安装的独立脚本。

下载当前抖音网页视频，并支持在个人主页批量选择视频下载。

## 修复的 Bug

| 项 | 内容 |
|---|---|
| **现象** | 官方版悬浮下载按钮**左侧约 168px 宽的透明区域会屏蔽鼠标点击**。该区域恰好覆盖抖音视频右侧的点赞、展开评论等操作栏，导致不上下拖动按钮就无法点击页面原有功能。 |
| **原因** | 悬浮按钮的外层面板是一个 `position: fixed` 的矩形容器，其中预留了一块隐藏的"状态气泡"区域（`min-width: 168px`）。状态气泡自身虽然设置了 `pointer-events: none`，但**外层面板没有**，默认 `pointer-events: auto` —— 点击落在透明预留区时事件被面板截获，而面板没有任何点击处理逻辑，形成点击死区。 |
| **修复** | 给整个面板设置 `pointer-events: none`（点击穿透），仅保留下载按钮本身 `pointer-events: auto`。按钮外观、右侧悬浮、上下拖动、位置记忆等设计全部保持不变。 |

## 与原版的其他区别

1. **可与官方版共存**：使用独立的 DOM id / CSS 类 / 动画名 / 本地存储键（前缀 `douyin-downloader-pro-`），两个脚本可同时安装、互不干扰。
2. **快捷键不同**：本版为 `D`（Download），官方版为 `Q`，共存时不会同时触发。
3. **按钮自动避让**：检测到官方版悬浮按钮存在时，本版按钮自动向左偏移，避免两个按钮重叠；未检测到时保持贴边（right 16px）。
4. **不再被官方版覆盖**：移除了官方 GreasyFork 更新地址，官方发布新版本不会覆盖本分叉的修复。

## 安装

1. 安装 [Tampermonkey](https://www.tampermonkey.net/)（或其他支持 `GM_*` API 的油猴扩展）。
2. 打开油猴管理面板 → 新建脚本，将 `douyin-downloader-pro.user.js` 的内容粘贴进去，保存。
3. 打开抖音网页刷新即可。

## 使用

- 在视频页点击右侧悬浮下载按钮，或按 `D` 键下载当前视频。
- 按钮可上下拖动，位置会被记住。
- 在个人主页点击按钮，可批量扫描并选择下载视频（支持选择保存文件夹、搜索筛选、全选/全不选）。

## 开源协议

[MIT](LICENSE)

- 原作者：[ArcherEmiya](https://github.com/W-ArcherEmiya)（官方版，MIT）
- 分叉维护：[HeWanglan-X](https://github.com/HeWanglan-X)
