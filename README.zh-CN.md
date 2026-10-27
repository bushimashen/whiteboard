# 手绘白板 · Whiteboard

> 浏览器里的手绘风白板：矩形、圆、菱形、箭头、自由笔、文字 —— 线是真·手绘抖动的那种，笔迹是按压感收窄的实心轮廓，可导出 PNG / SVG / JSON。

[English](README.md) | **简体中文**

<a href="https://bushimashen.github.io/whiteboard/"><b>▶ 在线试用</b></a>

## 能做什么

- **9 个工具** —— 选择、抓手、矩形、圆形、菱形、直线、箭头、画笔、文字、橡皮
- **手绘风格** 由 [rough.js](https://github.com/rough-stuff/rough) 提供：三档粗糙度，填充样式有斜线 / 交叉 / 实心 / 锯齿 / 圆点
- **压感笔迹** 由 [perfect-freehand](https://github.com/steveruizok/perfect-freehand) 提供：笔迹是一圈会收窄的实心轮廓，不是等宽线条
- **多选** —— 框选、Shift 点选加减、拖动整体移动、四角手柄缩放（对成组和笔迹都有效）
- **文字编辑** —— 点击落字、就地输入；双击已有文字再编辑
- **视图** —— 滚轮以光标为锚点缩放，空格拖动或抓手工具平移，适应内容
- **导出** —— 2 倍 PNG（自动裁到内容范围）、真矢量 SVG、JSON 往返导入
- **撤销 / 重做**（Ctrl+Z / Ctrl+Shift+Z）、复制（Ctrl+D）、方向键微调、层级、自动存本地

## 为什么把库放进仓库

`vendor/rough.js`（27KB）与 `vendor/freehand.mjs`（4.4KB）直接提交进仓库，而不是挂 CDN，
是为了**离线可用、也不怕被墙**。两者都是 MIT，许可证就放在旁边。

`rough.js` 是普通脚本（声明全局 `rough`），`perfect-freehand` 是 ES 模块、由主模块 import ——
所以**页面必须用 http(s) 打开**，`file://` 双击不行。

## 两个值得记下来的 bug

**「尺寸过小删除」把每一笔都删了。** 抬手时会把误触产生的零尺寸图形丢掉，但画笔元素没有 `w`/`h`
（它是一串点，两者恒为 0），而判断写成了 `if / else if` 链、最后一条是 `el.w < 3 || el.h < 3`，
于是每一笔都命中。改成**按元素类型显式分支**，只有真有宽高的图形才做尺寸判断。

**落字时编辑器立刻被关掉。** 在 `pointerdown` 里对 `<textarea>` 调 `focus()` 是站不住的：
浏览器对同一次 mousedown 还有默认聚焦行为，随后把焦点抢回画布，于是触发 `blur` → `closeEditor()`，
空文字被删掉。改成把 `openEditor()` 延到下一帧，并且**编辑中任何画布按下都先当作「结束编辑」**，
而不是顺手再画一个新元素。

## 怎么用

页面用了 ES 模块，请起本地服务而不是双击：

```bash
python -m http.server 8000    # 然后打开 http://localhost:8000
```

`file://` 不行 —— 浏览器不允许从本地文件源 `import` 模块。

## 快捷键

| 按键 | 作用 |
|---|---|
| `V` `H` `R` `O` `D` `L` `A` `P` `T` `E` | 选择 / 抓手 / 矩形 / 圆形 / 菱形 / 直线 / 箭头 / 画笔 / 文字 / 橡皮 |
| `Space`+拖动 | 平移 |
| 方向键 | 微调 1px（`Shift` 10px） |
| `Ctrl+A` / `Ctrl+D` | 全选 / 复制一份 |
| `Delete` | 删除选中 |
| `Ctrl+Z` / `Ctrl+Shift+Z` | 撤销 / 重做 |
| 滚轮 | 以光标为锚点缩放 |

## 明确不做的事

- 没有多人实时协作 —— 这是本地草稿板，存在你自己的 `localStorage` 里。
- 笔迹存原始点列，除了 freehand 渲染时做的平滑，没有额外简化。
- 不支持插入图片（那个用 SVG 编辑器或别的工具）。

## 许可

页面代码 MIT —— 见 [LICENSE](LICENSE)。
`vendor/rough.js` 是 rough.js，MIT —— 见 [vendor/roughjs-LICENSE](vendor/roughjs-LICENSE)。
`vendor/freehand.mjs` 是 perfect-freehand，MIT —— 见 [vendor/perfect-freehand-LICENSE](vendor/perfect-freehand-LICENSE)。
