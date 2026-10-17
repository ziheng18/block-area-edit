# 噪域编辑器 · Block Area Edit

一个**单文件**的 Phigros / RPE **噪域（blockArea）编辑与导出**工具。
双击 `block-area-edit.html` 即用：**无构建、无依赖、不联网**。

也可以直接用**在线版**（GitHub Pages，根路径自动进编辑器）：<https://ziheng18.github.io/block-area-edit/>

- 编辑 **RPE 噪域判定线**（Recorder / Line2Area 约定）与**官方 `blockAreaList`**
- 8 轨事件模型（中心X / 中心Y / 宽 / 高 / 旋转 / **颜色** / 出现 / 生效）+ 时间轴关键帧
- **彩色噪域**：颜色作为独立事件类型（RGB），写回 Line2Area 的 `extended.colorEvents`；
  没有颜色事件 = 默认噪域红 `255,84,84`。推荐白色 `isSubtract0/1.png`（染色更直观）。
- 按 Ctrl 多选事件、`↑↓` 沿时间轴平移、批量编辑、克隆到其它噪域
- 图片 → 噪域拟合；噪域暂存目录（侧车存储，不用写回 RPE）
- 导出：官方谱面 / 官方 pez / RPE / RPE pez / 纯 `blockAreaList.json`

> **本项目为非官方项目**：与南京鸽游网络有限公司（Pigeon Games）及《Phigros》官方**不存在授权、合作或运营关系**；
> 项目不附带任何官方素材（不含游戏资源文件、音乐、曲绘、界面图形等），也不代表官方立场。
> 依据：《Phigros》同人创作及第三方项目规范（2026 版）· 第三方工具前置条件 (3)。

## 支持范围

- **两种谱面格式都支持**：**RPE** 谱面（`.json` / `.pez`）与**官方 Phigros** 谱面（`formatVersion 3`）。
  载入时**按内容识别**，顶栏两个入口都能开对方格式，`.pez` 里的音乐也会一并读入。
- **适配 Phira Pro**：导出的官方谱面可直接放进 Phira Pro 使用。
- **RPE 噪域可直接导入**：支持 B 站 **@不会特效の点缀星空** 给出的 **Line2Area 协议** ——
  用 `Texture = isSubtract0/1.png` 的判定线承载噪域（其上音符不渲染），
  这类 RPE 噪域谱面**可直接导入**本编辑器继续编辑。
- **彩色噪域（Line2Area 扩展）**：颜色控制是独立事件类型，存在噪域线的
  `extended.colorEvents`（`start`/`end` 为 `[r,g,b]`，与普通事件一样支持时间/缓动）。
  没有颜色事件时按 Recorder 约定使用默认色 `255,84,84`（标准噪域红）；
  推荐使用白色 `isSubtract0.png` / `isSubtract1.png`（RPE Tools 噪域文件夹里的
  「用来做彩色噪域的白色噪域贴图.zip」，染色更直观，但不是必须）。
  不同颜色噪域重叠时按 RPE 的 **zOrder / 线号**决定谁在上（最高者在最上方）；
  两个反转块产生的噪域也由最上面的反转块颜色决定最终颜色。编辑器会按列表顺序
  （及噪域自身的 zOrder）复现这套遮挡关系。
- **RPE 噪域可反向导出（可选）**：导出功能里勾选「**RPE 含噪域线**」后，「导出 RPE / 写回目录」
  会把每个噪域写成 **Line2Area 判定线**（`Texture = isSubtract0/1.png`、生命周期走速度道 `1/2/3/4`、
  `anchor` 为贴图归一化支点）。给 **RPE Recorder / Phira Pro** 这类认该约定的渲染器用；
  普通 RPE 播放器会把噪域线当普通判定线画，**默认关闭**。
  **同一时刻不重叠的噪域会合并进同一条判定线**（一条线的速度道本来就支持多段生命周期），
  按加减算分开后尽量压到理论最少条数；代价是文件变大、合并线在 RPE 里不再是一个噪域一个支点。
  **官方谱也能直接导出成 RPE**：判定线 / 音符会先按单位换算转过去（不再只剩噪域线），
  载体线上的音符在重建 / 合并时也会保留。
  **彩色噪域也走这条路径**：颜色事件写进每条噪域线的 `extended.colorEvents`，默认色省略；
  合并线的颜色按各自窗口同步写入，不同颜色重叠时用 `zOrder` 保持与编辑器一致的遮挡顺序。
- **适配 RPE Recorder**：把「**仅导出噪域数据**」得到的 json 放进 **RPE Recorder 的 `file` 文件夹**即可读取。
- **官方噪域格式：导入 / 导出双向兼容** —— 可读入官方谱面里已有的 `blockAreaList` 继续编辑。

## 快速开始

1. 用 **Chrome / Edge** 打开 `block-area-edit.html`（双击即可，`file://` 也能跑）
2. 顶栏「**打开 RPE 谱面 / pez**」或「**导入官方谱面 / pez**」，选谱面文件（`.pez` / `.json`）
3. 左侧列表或判定框里点选噪域 → 右侧属性面板编辑；时间轴打关键帧
4. 导出：
   - 「**导出官方**」→ 官方格式 `json`（`formatVersion 3` + `blockAreaList`）
   - 「**导出 pez**」→ 打包成 `.pez`（谱面 + 音乐 + 插图 + `info.txt`），可直接丢给 Phira
   - 「**仅导出噪域数据**」→ 输出**标准官谱结构** `blockAreaList.json`
     （顶层键与官谱一致 `formatVersion / offset / judgeLineList / blockAreaList`，`judgeLineList` 为**空数组** = 没有判定线）
   - 「**导出 RPE**」（勾选导出功能里的「**RPE 含噪域线**」）→ RPE 格式 `json`，噪域写成 **Line2Area 判定线**

## 支持的输入（一律按内容识别，按钮只是提示）

| 类型 | 说明 |
|---|---|
| RPE 谱面 `.json` | 含 Recorder 噪域判定线约定（`Texture = isSubtract*.png`） |
| 官方谱面 `.json` | `formatVersion 3` + `judgeLineList`（+ `blockAreaList`） |
| `.pez` | zip 包：谱面 json + 音乐（+ 插图 / `info.txt`）。**包里的音乐会一并读入播放器** |
| 纯噪域数据 | 标准官谱外壳 `{ formatVersion, offset, judgeLineList: [], blockAreaList }`（旧裸数组仍兼容） |

## 浏览器要求

- **目录直连**（「📁 RPE 目录」/「🗂 噪域暂存」/「写回目录」）需要 **Chromium 系**（Chrome / Edge）：
  用的是 File System Access API。
- **文件导入导出、`.pez` 解包、音乐播放**：现代浏览器都可以（Firefox / Safari 可用，但目录功能不可用）。
- 目录功能第一次要**授权一次**；换机器 / 清站点数据后要重新授权。

## 贴图（可选，本项目不带）

本项目**不附带任何贴图**（第三方皮肤包没有分发许可，见 [THIRD-PARTY.md](THIRD-PARTY.md)），
默认音符用**线段画法**。想要真贴图：

> 🎨 面板 → 「选择 respack.zip / 选择文件夹 / 选图片…」→ 导入你自己的 Phira 皮肤包。
> 导入后会记在浏览器里，下次打开自动恢复；「恢复内置」= 回到无贴图。

## 已知限制

- **RPE `<170` 且用了非线性流速缓动**的谱面：编辑器一律按 RPE **1.7.0 语义**积分（载入时会提示）。
- 导出官谱时，音符的 `size` / `yOffset` / `isFake`、hold 的 `speed`、`inclineEvents` /
  **`colorEvents`（官方噪声格式没有颜色）** 不保留（官方格式本身也表达不全）；
  需要彩色噪域请用「导出 RPE」并勾选「RPE 含噪域线」。
- 打击特效的 `hitFxRotate` / `hideParticles` 未生效；`.pez` 里的插图暂未使用。
- 噪域 `blockAreaList` 体积未优化（实测某 RPE 谱导出后 11 MB，其中约 9.7 MB 是它）。
- 判定线与音符是**只读参考**（用于对齐位置 / 节奏），不提供编辑。
- 撤销 / 重做覆盖**噪域与 BPM**；时间轴从第 1 拍在最下方、越往上拍越大。

## 许可

**GPL-3.0-only**（[LICENSE](LICENSE) 为 GPL-3.0 逐字全文）。部分算法 / 常量移植自
[Phira-Pro](https://github.com/Phira-Pro/Phira-Pro)（GPL-3.0-only），源码注释里逐处标注了上游 `文件:行号`。

---

> **说明**：除移植自 [Phira-Pro](https://github.com/Phira-Pro/Phira-Pro) 的代码外，本项目的其余代码与本文档均由 AI 生成。
