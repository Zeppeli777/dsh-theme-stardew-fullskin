# dsh-theme-stardew-fullskin · 星露谷物语整皮主题

把 DeepSeek Harness 的 Web UI 换成一整片星露谷——祝尼魔陪你写代码。

灵感与骨架来自 [dsh_theme_terraria](https://github.com/10086ggqq/dsh_theme_terraria)（同一套 dsh 主题插件机制），视觉与素材全面换成星露谷物语。

> 🔀 本仓库是**完全替换版**（整皮）：整个前端换成星露谷界面。想要只换配色、保留官方 React UI 的**组件替换版**，请用 [`dsh-theme-stardew-valley`](https://github.com/Zeppeli777/dsh-stardew-skin)（原仓库 `main` 分支）。

![标题屏](screenshots/title.png)

![主界面（0.2.1 实测）](screenshots/main-021.png)

## 功能一览

- **标准 dsh 插件包**：入口 `index.js` 导出 `apply(ctx)`，`cordis.patch.yml` 声明 `dsh.bundle` 补丁层，一条 `dsh plugin --profile web add dsh-theme-stardew-fullskin` 即可安装/卸载，不改动 dsh 安装中的任何文件。
- **不是纯皮肤**：主题直连 dsh 宿主的 Typert 网关协议（一元调用 `POST /api/<ns>/<method>`，事件走 WebSocket `/api/remote.mux` 多路复用流——`$events` 广播/审批瀑布 + `session/follow` 会话日志），会话创建、消息发送、流式输出、工具审批、模型切换、工作空间管理全部真实可用。
- **农场日历系统**：会话即游戏日——每开一个新存档，星露谷历法（四季 × 每季 28 天）就推进一天；顶栏日历木牌随季节换色，点击打开 28 日月历与「今日运势」（TV 占卜风）；换季时全屏横幅 + 号角音效 + 壁纸季节色调，并有花瓣（春）/萤火（夏）/落叶（秋）/雪花（冬）四季天气粒子，均可在设置里关闭。
- **四块农场 = 四个 Agent 预设**：标准农场（standard，全功能编码代理）、河边农场（code，Code Mode SDK）、森林农场（minimal，极简双工具）、山顶农场（cordis，自定义 preset 模板）。模式选择弹窗 1:1 复刻游戏内「建人物 + 选农场」界面，配官方农场地图图标。
- **手账风消息渲染**：祝尼魔的回复按 markdown 渲染——标题/列表/表格/引用/链接/行内码，代码块进深褐木匣（语言牌 + 复制按钮 + js/py/sh/json/html 着色），流式输出即渲即见、上翻阅读不拽底；工具调用做成「布告栏委托」卡：农活叫法（终端命令/读取文件/编辑文件…）+ 参数与输出分区着色 + 复制按钮，长输出自动折叠；思考过程流式实时上屏，出正文后自动折起。
- **上下文能量条**：HUD 里的游戏能量条实时显示上下文余量（按最近一轮 prompt tokens / 标称上下文估算，默认 128K，可用 `localStorage stardew.ctxCap` 调整）。
- **五只农场伙伴桌宠**：小鸡（蹦跳）、虚空鸡（漂浮）、史莱姆（挤压跳）、猫咪（游走+坐下）、蝴蝶（飞舞）。**默认关闭、可选开启**：顶栏「桌宠」按钮一键全开/全关，单只开关在标题屏「农场伙伴」弹窗里，偏好刷新后保持。
- **8-bit 音效引擎**：纯 WebAudio 振荡器合成，零音频文件；助手回复、审批请求、提问、失败、换季各有提示音，顶栏一键开关。
- **壁纸 / BGM**：默认 AI 生成的星露谷风像素山谷壁纸；支持自定义图片/GIF/视频壁纸（IndexedDB），自定义 BGM 上传循环播放，另有「示例 BGM ♪」内嵌 SoundCloud 播放器（ConcernedApe - Spring (The Valley Comes Alive)，需能访问 soundcloud.com）。

![农场日历弹窗](screenshots/calendar.png)

![消息渲染：markdown 表格/代码匣与布告栏委托卡](screenshots/chat-render.png)

![换季横幅·夏天来了](screenshots/season-banner.png)

### 概念映射

| 官方 UI 概念 | 星露谷主题映射 |
| --- | --- |
| Agent Preset（预设） | 农场选择：标准 / 河边 / 森林 / 山顶 |
| 会话（Session） | 存档 / 新的一天（每开新存档推进星露谷日历一天） |
| 用户消息 | 农夫 |
| 助手 | 祝尼魔 |
| 工具审批请求 | 布告栏委托 |
| 任务清单（todo） | 任务日志 |
| token 用量 | 上下文能量条 |
| API 密钥状态 | 指示灯（绿=已配置） |

## 安装

```
# 环境：dsh CLI 0.1.2-rc.1 ~ 0.2.1-rc.3 实测兼容（沙箱端到端验证：
# 会话/流式输出/工具调用/预设/模型切换/存档列表；0.2.x 起 trust fence
# 移到 connection 行、web-runtime 行不再携带 trustedHosts，本插件已同步；
# 旧版 dsh 请用历史提交），Node.js ^22.19 || >=24 与 pnpm
# 1. 把主题作为插件安装进 web profile
dsh plugin --profile web add dsh-theme-stardew-fullskin

# 或从本地目录安装（本仓库根就是插件包）
dsh plugin --profile web add /path/to/dsh-theme-stardew-fullskin

# 或直接从 GitHub 安装
dsh plugin --profile web add github:Zeppeli777/dsh-theme-stardew-fullskin

# 2. 启动 Web 服务（默认 http://127.0.0.1:3080/）
dsh web
```

打开页面 → 「新存档」→ 在弹出的设置里填入 DeepSeek API 密钥 → 开始对话。

### 避坑

- 农场按钮按宿主实际存在的预设动态生成：rc.1 起官方预设调整为 standard / minimal / cordis（另可能有 PTC 等新增预设，以原名追加显示），旧版的 code（河边农场）预设已不存在。
- 更新插件后页面没变：强制刷新 `Ctrl+F5` 或地址加 `?v=2` 绕过缓存。
- 浏览器缓存旧 UI / 密钥保存失败 / 文件夹选择器拿不到完整路径等问题，与 Terraria 版一致，见[参考仓库 README](https://github.com/10086ggqq/dsh_theme_terraria#避坑指南实测踩过的坑)。

## 本地预览（不装 dsh 也能看界面）

```
npm run dev          # 默认 http://127.0.0.1:7100/
npm run dev -- --port 8080
```

纯静态伺服 `web/`，无宿主时 RPC 会失败（界面可浏览，对话不可用）。

## 修改主题

直接编辑 `web/index.html`（HTML+CSS+JS 单文件，无构建步骤），重启 `dsh web` 生效。
素材加工脚本在 `scripts/`（`transform.py` 为一次性改皮脚本，`gen_pets.py` 生成桌宠页，`dev-server.mjs` 为预览服务器）。

## 卸载

```
dsh plugin --profile web remove dsh-theme-stardew-fullskin
dsh web   # 重启后恢复官方 React 前端
```

浏览器端个性化数据（壁纸、音乐、音效开关、审批偏好、伙伴设定）存在 localStorage / IndexedDB，清除站点数据即可完全抹掉。

## 目录结构

```
├── package.json            <- 插件清单（包名 dsh-theme-stardew-fullskin、dsh.bundle 补丁声明）
├── index.js                <- 插件入口：导出 apply(ctx)，替换官方前端解析器
├── cordis.patch.yml        <- bundle 补丁层：停用官方 web-runtime，插入 stardew-theme 行
├── web/                    <- 插件伺服的主题前端
│   ├── index.html              主题全部源码（HTML + CSS + JS 单文件）
│   ├── logo.png                星露谷物语中文 Logo（标题屏）
│   ├── valley.jpg              默认山谷壁纸（AI 生成）
│   ├── junimo.png              祝尼魔头像
│   ├── heart.png               红心印记（对话气泡装饰）
│   ├── energybar.png           能量条参考图
│   ├── farm-*.png              农场地图图标（模式选择弹窗）
│   ├── chicken_*.png / slime.gif / cat.gif / butterfly.gif   桌宠素材
│   ├── parsnip.png             防风草 favicon
│   ├── fusion-pixel-12px.woff2 缝合像素字体（开源）
│   ├── chicken_pet.html / voidchicken_pet.html / slime_pet.html / cat_pet.html / butterfly_pet.html   桌宠页
│   └── manifest.webmanifest    PWA 清单
├── scripts/                <- 素材加工与构建脚本（不参与运行）
├── assets/                 <- 维基下载的原始素材 + SOURCES.md 来源清单
├── screenshots/            <- 界面截图
└── reference-terraria/     <- Terraria 主题参考仓库（仅开发参考，不参与打包）
```

## 素材来源与许可

- 本主题代码：**MIT License**
- 游戏素材（Logo、祝尼魔、鸡、史莱姆、猫、蝴蝶、农场图标等）：取自 [Stardew Valley Wiki](https://zh.stardewvalleywiki.com)，版权归 ConcernedApe 所有。本主题为粉丝向非官方皮肤，与 ConcernedApe 无关；**素材请勿商用**。逐文件来源见 `assets/SOURCES.md`。
- 像素字体 Fusion Pixel：其自身的开源许可（见 Fusion Pixel Font 项目）。
- 示例 BGM「Spring (The Valley Comes Alive)」：通过 SoundCloud 官方嵌入式播放器在线播放，不分发音频文件。
- 默认壁纸 `valley.jpg`：AI 生成的星露谷风格像素画。

愿祝尼魔的星光落在你的每一次编译上。
