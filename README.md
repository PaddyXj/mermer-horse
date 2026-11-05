# 🫏 搬砖进度条 · 摸鱼版

上班摸鱼，摸出一个悬浮在桌面上的小游戏工具。半透明玻璃质感、Always-on-Top、可收起可拖动，老板来了一键缩小。

## ✨ 功能一览

| 模块 | 说明 |
|------|------|
| 🐟 摸鱼君 | 桌面宠物，心情值 + 摸鱼等级 + 经验进度 |
| 📊 搬砖进度 | 自定义每日目标 · 实时金币 · 摸鱼等级 |
| 🎮 摸鱼小游戏 | 方块消除 / 打砖块 / 点金币 / 专注模式 |
| 🐴 驴拉磨 | 放着就能涨金币和经验 |
| 🐾 搭子图鉴 | 解锁收集不同摸鱼搭子 |
| ⏰ 日程备忘 | 内置提醒，也能联动系统日历 |
| 📈 股价 | A 股/美股/港股实时行情面板 |
| 🌫️ 天气 | 桌面右下角小天气 |
| 💬 摸鱼语录 | 随机金句滚动 |

### 🐟 摸鱼等级系统（v2.0 新增）

- **8 级成长线**：摸鱼实习生 → 学徒 → 专员 → 高手 → 大师 → 宗师 → 传说摸鱼人 → 摸鱼之神
- **越升越难**：每级所需经验按 `300 × 1.65^lv` 递增（300 → 495 → 817 → ... → 9989）
- **心情加成**：心情越好经验涨得越快，从 ×0.35 到 ×1.6 共 6 档
- **经验来源**：小游戏得分、喂食搭子、派去摸鱼、驴拉磨、点金额、金币收益折算

## 📁 目录结构

```
mermer-horse/
├── index.html                    # 主程序 · 纯前端 · 改它就改了一切
├── index.v1.bak                  # v1.0 备份
├── 一起来搬砖.exe                # Electron 绿色版（双击即用 · 79 MB · 推荐）
├── 搬砖进度条-WebView2版.exe     # WebView2 单文件版（1.2 MB · 轻量）
└── demo-donkey.html              # 驴拉磨单独 demo
```

## 🚀 使用方法

两个 exe 都是 **绿色版 · 免安装 · 双击即开**：

- **`一起来搬砖.exe`**（推荐）— Electron 封装，带透明悬浮窗、收起/展开动画、多屏适配
- **`搬砖进度条-WebView2版.exe`**（轻量）— 单文件 1.2 MB，需要 Win10+ 自带的 WebView2 Runtime

> Win7 等旧系统跑轻量版会提示缺 WebView2，装一下微软官方 Runtime 即可。

**操作**：
- 空白处按住拖动 → 移动位置
- 悬停 / 点击右上角 ⊟ → 展开；再点 → 收起成小图标
- `F` 切换专注模式（隐藏界面，只留一个像素点）
- `空格` 快速赚金币（摸鱼彩蛋）

## 🔧 改完源码怎么重新打包

改了 `index.html` 之后，exe 里的内嵌 HTML 不会自动同步。两种做法：

### Electron 版（一起来搬砖.exe）

1. 解压原 exe（7-Zip 可直接解），把新 `index.html` 放进 `resources/app.asar` 里面替换
2. 同步更新 asar 头 JSON 和 BrickProgress.exe 里的 `ELECTRONASAR` 完整性哈希（Electron 启动会校验）
3. 重新压缩打包

```bash
# 快捷脚本（需要 pefile + 7z + NSIS）
python3 tools/repack_electron.py
```

或者找 NSIS 自解压模板，把改过的 `app.asar` 和 `BrickProgress.exe` 重新塞回去打包。

### WebView2 版（搬砖进度条-WebView2版.exe）

HTML 以 `RCDATA/202` 资源嵌入 PE 里，用 `pefile` 直接 patch：

```bash
python3 -c "
import pefile, struct
pe = pefile.PE('搬砖进度条-WebView2版.exe')
# 找 RCDATA 202，替换 Offset/Size，把新 HTML append 到文件末尾并回填 VA
"
```

WebView2 版没有完整性校验，patch 完直接双击就能用。

## 💾 数据存储

所有数据存在浏览器 localStorage 里，key 前缀 `brick.*`：

```
brick.cfg.enc.v1     # 加密配置
brick.fun.v3         # 金币 / 摸鱼君状态 / 搭子
brick.reports.v1      # 日报
brick.memos.v1        # 备忘
brick.logs.v1         # 日志
brick.marks.v1        # 摸鱼标记
```

Electron 版在 `%APPDATA%\BrickProgress`，WebView2 版在 WebView2 用户数据目录。两个版本数据 **不互通**，需要手动迁移（复制 localStorage 文件或各自手动清空重配）。

## 🛠️ 技术栈

- 纯 HTML / CSS / 原生 JS（无框架 · 约 3000 行）
- Electron 封装悬浮窗（透明 + Always-on-Top + 无框）
- WebView2 轻量单文件方案
- localStorage 持久化 + XOR 轻加密

## 📜 License

MIT，随便用。
