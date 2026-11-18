# ASCII 视频转换器 / ASCII Video Converter

> 把任意视频实时渲染成 ASCII 字符动画 —— 纯前端、单文件、零依赖，**全部在本机处理，视频不会上传到任何服务器**。

一款跑在浏览器里的复古风 ASCII 视频播放器：载入一段视频，右边实时吐出用字符拼出来的画面，颜色、字符密度、亮度、对比度全部可调，还能把它导出成可直接分享的独立 HTML 动画或 WebM 视频。

界面刻意做成 Windows 95 / 98 的经典风格（墨绿桌面、银灰凸起面板、蓝色渐变标题栏），复古感拉满。

---

## ✨ 功能特性

### 实时转换
- **拖拽或选择视频**：点击「选择文件」，或直接把视频拖进右侧输出窗口
- **列数可调**：30 – 200 列，决定字符画面的横向分辨率（默认 96 列）
- **4 套字符集**：
  - 经典 10 级 `.:-=+*#%@`
  - 细腻 16 级 `.'`-:~=+*?%$#&@`
  - 方块 `░▒▓█`
  - 二值 `█`
- **彩色 / 反相**：保留原视频色调，或翻转亮度映射
- **亮度调节**：-60 ~ +60
- **对比度调节**：0.5 ~ 2.2

### 播放与音频
- 播放 / 暂停 / 进度条 / 时间显示 / 拖动跳转
- **原声播放**：可开关，音量 0 – 100 可调（走 Web Audio，音量与画面同步）

### 五种导出方式
| 按钮 | 产物 | 说明 |
| --- | --- | --- |
| 存当前帧 TXT | `.txt` | 当前画面纯字符文本 |
| 存当前帧 HTML | `.html` | 单帧彩色 HTML，双击即看 |
| 导出动画 HTML | `.html` | **完整循环动画播放器**，15 fps 抓帧（最多 240 帧），可选内嵌原声 WAV 音轨，单文件可离线分享 |
| 录制 WebM | `.webm` | 边播边录，通过 `MediaRecorder` 生成视频文件 |
| 复制 | 剪贴板 | 一键复制当前帧字符到剪贴板 |

### 隐私与工程
- **零外部依赖**：没有任何 CDN、库、字体、统计脚本
- **纯本地处理**：视频帧、音频解码、编码全在浏览器内存里完成，不发起任何网络请求
- **单文件部署**：`index.html` 一个文件就是全部程序，可下线保存、可断网使用
- **自适应**：随窗口 / 屏幕旋转自动重算字号与行高，手机、平板、桌面都能用

---

## 🚀 在线使用

直接打开已部署的站点即可，无需安装：

**https://scn68s6qt2b0.doubao.link/app/app_17j9xhg8z5c**

也可以自己托管：仓库里的 `index.html` 就是全部程序，丢进任意静态托管（GitHub Pages、Cloudflare Pages、对象存储、NAS）都能直接跑。若要用 GitHub Pages，在仓库 `Settings → Pages` 里把 Source 选为 `Deploy from a branch`、分支选 `main`、目录选 `/ (root)`，保存后等一两分钟即可用 `https://hellociao1.github.io/ascii-video-converter/` 访问。

## 💻 本地运行

下载 `index.html`，双击用浏览器打开就行。不需要 Node、不需要 Python、不需要服务器。

如果想用本地服务跑（某些浏览器对 `file://` 的视频解码有限制）：

```bash
# 任意一种，能起个静态服务就行
python3 -m http.server 8000
# 然后浏览器访问 http://localhost:8000/index.html
```

## 📖 使用步骤

1. 点击左侧「选择文件」，或把视频拖入右侧输出窗口
2. 视频载入后会自动渲染首帧；点「▶ 播放」开始实时转换
3. 拖动「列数」滑块，字符颗粒度立刻变化
4. 切换字符集，试试方块和方块渐变的效果差异
5. 想要可分享的成品？点「导出动画 HTML」，等抓帧和音轨提取完成即可下载

> ⚠️ 提示：「导出动画 HTML」默认抓 15 fps、最多 240 帧（约 16 秒）。视频更长时只会截取前段，建议分段导出。若内嵌音轨体积超过 5 MB 会自动丢弃音轨，只保留画面。

---

## 🧠 实现原理

整体是一条极简的「视频 → 字符」流水线：

1. **采样**：视频帧画到隐藏的 `<canvas>`，按字符宽高比压缩到 `列数 × 行数` 的网格
2. **亮度映射**：对每个格子取像素亮度（`0.299R + 0.587G + 0.114B`），套用亮度 / 对比度变换
3. **字符替换**：亮度归一化后查字符集表，暗部对应空格 / 疏字符，亮部对应密集字符
4. **着色**：彩色模式下每个字符包裹一层 `<span style="color:...">` 保留原始色相
5. **播放**：用 `requestAnimationFrame` 逐帧拉取，实现实时转换

导出动画时，先逐帧 `seek` 抓字符画面存进数组，再把数组和可选音轨一起序列化成一段自包含的 HTML；音轨用 `OfflineAudioContext` 重采样到 16 kHz 单声道并编码为 WAV，以 base64 内嵌。

## 🗂️ 项目结构

```
ascii-video-converter/
├── index.html   # 全部程序：UI + 转换逻辑 + 导出（单文件，零依赖）
├── README.md
└── LICENSE      # MIT
```

## 🌐 浏览器兼容

| 浏览器 | 状态 |
| --- | --- |
| Chrome / Edge (桌面 & Android) | ✅ 完整支持 |
| Safari (macOS & iOS) | ✅ 支持（`playsinline` 已处理，iOS 需用户手势触发播放） |
| Firefox | ✅ 支持 |
| IE / 旧版浏览器 | ❌ 不支持 |

依赖的核心能力：Canvas 2D、`MediaRecorder`（录制 WebM）、Web Audio API、`navigator.clipboard`。录制 WebM 在 Safari 上可能不可用，其余功能正常。

## 🤝 贡献

欢迎提 Issue 和 PR。想加新功能可以优先考虑这些方向：

- 更多字符集 / 自定义字符输入
- 抖动（dithering）算法，让暗部层次更细腻
- 导出 GIF
- 预设参数存档与分享链接

## 📄 许可证

本项目基于 [MIT License](LICENSE) 开源，可自由使用、修改、分发、商用，保留版权声明即可。

---

## English

**ASCII Video Converter** — a single-file, zero-dependency, fully client-side tool that turns any video into live ASCII art right in your browser.

Drop in a video, tune columns / charset / color / brightness / contrast, and watch it render as characters in real time. Export the current frame as TXT or HTML, produce a self-contained looping HTML animation (with optional embedded audio track), or record the output as WebM.

Nothing is uploaded — every frame is decoded and encoded locally. UI styled as classic Windows 95/98.

**Live demo:** https://scn68s6qt2b0.doubao.link/app/app_17j9xhg8z5c

Run it locally by simply opening `index.html`. No build step, no dependencies.

Licensed under the MIT License.

---

Made by [hellociao1](https://github.com/hellociao1)
