# 图标与 SVG 规范

配套素材：`icons.md`（46 个内联 SVG，分导航、操作、状态、对象四类，可整行复制进页面）。

## 1. 一句话原则

图标是形状语言，不是装饰。同一产品内只允许一套几何规则：一个 viewBox、一档线宽、一种端点、一组尺寸档位、一种上色方式；任何一个图标偏离这套规则，整页视觉秩序失效。

## 2. 几何规范（硬指标）

| 指标 | 取值 | 落地位置 |
| --- | --- | --- |
| 画布 | `viewBox="0 0 24 24"` | 每个 `<svg>` 根元素 |
| 描边 | `stroke-width="1.5"`；16px 档用 1.75 | 根元素或 CSS |
| 端点 | `stroke-linecap="round"` 与 `stroke-linejoin="round"` 同时存在 | 根元素，禁止子元素覆盖 |
| 填充 | `fill="none"`；描边与填充二选一，不混 | 根元素 |
| 栅格 | 坐标落在整数或 .5；不出现 0.25、0.75 | path 数据 |
| 图标本体 | 20×20，四周各留 2 边距，内容区为 2 到 22 | path 数据 |
| 视觉重心 | 按实际包围盒居中，不按数学中心硬放 | 绘制后目测 |
| 上色 | `stroke="currentColor"` | 根元素 |

- 1.5 是 24 画布下对应 14 到 16px 正文界面的稳定线宽，曲线与直线同一线宽；不为“更精致”降到 1，不为“更醒目”加到 2。
- 端点统一 round，连接统一 round；方形端点只属于坐标轴、图表这类纯几何图形，不进入图标库。
- 24 网格按 1 单位对齐：直线端点取整数或 .5，圆弧半径取整数或 .5，保证 16、20、24 三档缩放后不出现半像素发虚。
- 内容严格画在 2 到 22 之间，四周各留 2 单位；不把图形画满到 0 或 24，否则不同图标在页面里视觉大小不一致。

正确写法（内联 `<svg>`，不是 `<img>`，不是 icon font）：

```html
<button class="icon-btn" type="button" aria-label="搜索">
  <svg class="icon" viewBox="0 0 24 24" width="20" height="20"
       fill="none" stroke="currentColor" stroke-width="1.5"
       stroke-linecap="round" stroke-linejoin="round"
       aria-hidden="true" focusable="false">
    <circle cx="11" cy="11" r="6"/>
    <path d="M15.5 15.5 20 20"/>
  </svg>
</button>
```

```css
.icon {
  display: block;
  flex: none;
  width: 20px;
  height: 20px;
}
```

图标必须是页面 DOM 里的内联 `<svg>`。`<img src="icon.svg">` 无法继承 `currentColor`；icon font 无法保证 1px 栅格、线宽与端点；CSS 背景图同样失去颜色继承与无障碍控制。这三种形式都不进入组件代码。

## 3. 尺寸档位

| 档位 | 使用位置 | 线宽 |
| --- | --- | --- |
| 16px | 行内文字旁、表格单元格、标签内、紧凑菜单 | 1.75 |
| 20px | 按钮、输入框前缀、卡片操作、下拉选项 | 1.5 |
| 24px | 顶部导航、侧边栏、工具栏、页面标题区 | 1.5 |

- 只允许 16、20、24 三档，宽高必须同时设为相同值；24 是设计基准，16 与 20 是整数等比缩放，16 档把线宽补偿到 1.75 以抵消缩小后的视觉变轻。
- 禁止 18、22、30 这类非档位尺寸；禁止 `transform: scale(1.2)`、`zoom`、`width: 1.2em` 等非整数缩放。
- 12px 以下不使用描边图标：小于 12 的行内标记改成单色填充图形（实心圆、实心勾），或直接放回 16 档。
- 同一行内的图标尺寸一致；图标与文字基线对齐用 `vertical-align: -0.125em` 或 flex 居中，不靠 margin 硬调。

```css
.icon { width: 20px; height: 20px; stroke-width: 1.5; }
.icon[data-size="16"] { width: 16px; height: 16px; stroke-width: 1.75; }
.icon[data-size="24"] { width: 24px; height: 24px; stroke-width: 1.5; }
```

## 4. 颜色

图标一律 `stroke="currentColor"`，不在 SVG 内写死色值；颜色由使用处的 `color` 决定，一处改动全站生效。组件 CSS 里也不写 hex，统一引用 `assets/design-tokens.css` 的语义 token。

语义色只用于状态图标：成功 `var(--color-success)`、警告 `var(--color-warning)`、错误 `var(--color-danger)`、信息 `var(--color-primary)`；普通图标跟随文字色 `var(--color-text)`。

四种状态只改 `color`，不换图标、不改线宽、不改尺寸：

| 状态 | 规则 | 参考值 |
| --- | --- | --- |
| hover | 前景色转向主色，可叠加 1px 上移 | `var(--color-primary)` |
| active | 前景色加深，可叠加 `scale(.96)` | `var(--color-primary-active)` |
| disabled | 前景降到弱化灰，不叠加 opacity，避免二次发灰 | `var(--color-text-disabled)` |
| 选中 | 前景用主色，容器加浅底或左侧指示条，图标本身不换实心版 | `var(--color-primary)` + `var(--color-primary-soft)` |

```css
.icon-btn { color: var(--color-text); }
.icon-btn:hover { color: var(--color-primary); }
.icon-btn:active { color: var(--color-primary-active); }
.icon-btn[disabled] { color: var(--color-text-disabled); cursor: not-allowed; }
.icon-btn[aria-pressed="true"] { color: var(--color-primary); background: var(--color-primary-soft); }
```

## 5. 可访问性

- 装饰性图标（旁边有文字、或只做视觉强化）加 `aria-hidden="true"` 与 `focusable="false"`，从无障碍树移除；`focusable` 用于兼容旧版浏览器把 SVG 当可聚焦元素的行为。
- 纯图标可点击控件，可读名称写在 `<button>` 或 `<a>` 的 `aria-label` 上，不写在 `<svg>` 上，也不依赖 `<title>`。
- 图标加文字时，图标 `aria-hidden="true"`，由文字提供名称；不要再写一遍 `aria-label`，否则读屏重复播报。
- 状态变化用 `aria-pressed`、`aria-expanded`、`aria-current` 表达，图标旋转只是视觉反馈，不承担语义。
- 不用颜色单独表达成功、失败、警告；状态图标必须配文字或可读标签。

纯图标按钮：

```html
<button type="button" class="icon-btn" aria-label="关闭对话框">
  <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor"
       stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"
       aria-hidden="true" focusable="false">
    <path d="M6 6l12 12"/>
    <path d="M18 6L6 18"/>
  </svg>
</button>
```

图标加文字：

```html
<button type="button" class="btn">
  <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor"
       stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"
       aria-hidden="true" focusable="false">
    <path d="M12 4v12"/>
    <path d="M7 11l5 5 5-5"/>
    <path d="M4 20h16"/>
  </svg>
  下载报表
</button>
```

展开与收起：

```html
<button type="button" class="icon-btn" aria-expanded="false"
        aria-controls="filter-panel" aria-label="展开筛选">
  <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor"
       stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"
       aria-hidden="true" focusable="false">
    <path d="M6 9l6 6 6-6"/>
  </svg>
</button>
```

## 6. 禁止清单

**1. 用 emoji 代替图标**

错误示例：在按钮、导航、表格状态列里直接写 emoji 字符（放大镜、垃圾桶、对勾、警告三角这类彩色表情，如 `U+1F50D`、`U+1F5D1`），代码里表现为 `<span class="icon">emoji 字符</span>`。

改法：换成 `icons.md` 里对应的内联 SVG，颜色由父级 `color` 决定，跨平台渲染一致。

**2. 用 icon font**

错误示例：`<i class="iconfont icon-search"></i>`，或 `@font-face` 引入图标字体。

改法：内联 SVG。图标字体会出现字形缺失、基线偏移、无法按 1px 对齐、颜色只能整字改、读屏播报不可控。

**3. 用 PNG 位图**

错误示例：`<img src="/icons/search.png" width="20" height="20">`、`background-image: url(search@2x.png)`。

改法：复制 `icons.md` 对应 `<svg>`。位图在 1.5 倍屏发虚、暗色模式无法反色、hover 变色需要准备两套图。

**4. 混用线性与填充两套风格**

错误示例：同一导航里搜索用描边、首页用填充。

```html
<svg fill="none" stroke="currentColor">...</svg>
<svg fill="currentColor" stroke="none">...</svg>
```

改法：默认全站线性；只有 12px 以下的小标记允许填充，并以 `data-style="solid"` 统一登记，同一组图标不混。

**5. 描边 1、2、3 混着用**

错误示例：同一页出现 `stroke-width="1"`、`stroke-width="2"`、`stroke-width="3"`。

改法：根元素统一 1.5，16 档统一 1.75，用 CSS 变量控制；禁止在单个 `<path>` 上覆盖线宽。

**6. 图标被拉伸（宽高比不一致）**

错误示例：`width="20" height="24"`、`style="width:20px;height:24px"`、`transform: scaleX(1.2)`、`preserveAspectRatio="none"`。

改法：width 与 height 永远相等，只用 16、20、24 三档；不设置 `preserveAspectRatio`，保持默认等比。

**7. 圆角端点与直角端点混用**

错误示例：`stroke-linecap="butt"` 或 `stroke-linejoin="miter"` 与 round 出现在同一套图标里。

改法：所有图标根元素 `linecap` 与 `linejoin` 都为 `round`，子元素不覆盖。

**8. 图标里塞渐变和多重阴影**

错误示例：`<linearGradient>` 填充描边、`<filter>` 加投影、`filter: drop-shadow(0 2px 4px #000)`。

改法：单色描边；需要强调时改父级 `color` 或给容器加背景，不在图标内部做装饰。

**9. 图标比文字大一号以上**

错误示例：14px 正文配 24px 行内图标，或 20px 标题配 32px 图标。

改法：行内 16px 图标配 14px 文字，按钮内 20px 图标配 14 到 15px 文字；图标不超过所在行行高的 1.2 倍，相邻只允许差一档。

**10. 用图标单独表达关键状态而不配文字**

错误示例：表格里只放一个红色三角表示“同步失败”，没有文字，也没有 `aria-label`。

改法：警告三角（`var(--color-warning)`）加“同步失败，请重试”；图标 `aria-hidden="true"`，由文字承载语义。

## 7. 三个可复制片段

### 7.1 图标按钮

```html
<button type="button" class="icon-btn" aria-label="设置">
  <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor"
       stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"
       aria-hidden="true" focusable="false">
    <path d="M4 7h6"/>
    <path d="M14 7h6"/>
    <circle cx="12" cy="7" r="2"/>
    <path d="M4 17h2"/>
    <path d="M10 17h10"/>
    <circle cx="8" cy="17" r="2"/>
  </svg>
</button>
```

```css
.icon-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  padding: 0;
  border: 0;
  border-radius: 8px;
  background: transparent;
  color: var(--color-text);
  cursor: pointer;
  transition: color var(--duration-base) var(--ease-out),
              transform var(--duration-base) var(--ease-out),
              background-color var(--duration-base) var(--ease-out);
}
.icon-btn:hover {
  color: var(--color-primary);
  background: var(--color-primary-soft);
  transform: translateY(-1px);
}
.icon-btn:active {
  color: var(--color-primary-active);
  transform: scale(.96);
}
.icon-btn:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}
.icon-btn[disabled] {
  color: var(--color-text-disabled);
  cursor: not-allowed;
  transform: none;
}
@media (prefers-reduced-motion: reduce) {
  .icon-btn,
  .icon-btn:hover,
  .icon-btn:active {
    transition: none;
    transform: none;
  }
}
```

### 7.2 带图标的输入框

```html
<label class="field">
  <span class="field__label">搜索订单</span>
  <span class="field__control">
    <svg class="field__icon" viewBox="0 0 24 24" width="20" height="20"
         fill="none" stroke="currentColor" stroke-width="1.5"
         stroke-linecap="round" stroke-linejoin="round"
         aria-hidden="true" focusable="false">
      <circle cx="11" cy="11" r="6"/>
      <path d="M15.5 15.5L20 20"/>
    </svg>
    <input class="field__input" type="search" placeholder="订单号或客户名称">
  </span>
</label>
```

```css
.field { display: block; }
.field__label {
  display: block;
  margin-bottom: 6px;
  font-size: 13px;
  color: var(--color-text);
}
.field__control { position: relative; display: block; }
.field__icon {
  position: absolute;
  left: 10px;
  top: 50%;
  transform: translateY(-50%);
  width: 20px;
  height: 20px;
  color: var(--color-text-secondary);
  pointer-events: none;
}
.field__input {
  box-sizing: border-box;
  width: 100%;
  height: 36px;
  padding: 0 12px 0 38px;
  border: 1px solid var(--color-border-strong);
  border-radius: 8px;
  background: var(--color-bg);
  color: var(--color-text);
  font: inherit;
  font-size: 14px;
  transition: border-color var(--duration-base) var(--ease-out),
              box-shadow var(--duration-base) var(--ease-out);
}
.field__input::placeholder { color: var(--color-text-secondary); }
.field__control:focus-within .field__icon { color: var(--color-primary); }
.field__input:focus {
  outline: none;
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px rgba(15, 118, 110, .15);
}
```

### 7.3 带图标的空状态

```html
<div class="empty">
  <span class="empty__icon">
    <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor"
         stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"
         aria-hidden="true" focusable="false">
      <path d="M3 6.5a2 2 0 0 1 2-2h4l2 3h8a2 2 0 0 1 2 2v8a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/>
    </svg>
  </span>
  <p class="empty__title">还没有项目</p>
  <p class="empty__desc">新建后即可添加成员与文件。</p>
  <button class="empty__action" type="button">新建项目</button>
</div>
```

```css
.empty {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 48px 24px;
  text-align: center;
}
.empty__icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: var(--color-primary-soft);
  color: var(--color-primary);
}
.empty__title {
  margin: 16px 0 4px;
  font-size: 15px;
  font-weight: 600;
  color: var(--color-text);
}
.empty__desc {
  margin: 0 0 16px;
  font-size: 13px;
  color: var(--color-text-secondary);
}
.empty__action {
  height: 32px;
  padding: 0 14px;
  border: 0;
  border-radius: 8px;
  background: var(--color-primary);
  color: var(--color-primary-on);
  font: inherit;
  font-size: 14px;
  cursor: pointer;
}
.empty__action:hover { background: var(--color-primary-hover); }
.empty__action:focus-visible {
  outline: 2px solid var(--color-primary-hover);
  outline-offset: 2px;
}
```

## 8. 动画规则

- 过渡只服务于含义变化：chevron 翻转 180° 表示展开与收起、加减号切换表示折叠、刷新箭头旋转表示刷新进行中。没有含义变化的图标不加过渡。
- 时长统一 200ms，缓动统一 `cubic-bezier(.16, 1, .3, 1)`；进入与退出用同一条曲线。
- 只动画 `transform` 与 `opacity`，不动 `stroke-width`、`width`、`height`；颜色过渡可以 200ms，但不允许颜色闪烁。
- 禁止持续旋转、呼吸、跳动、闪烁。loading 是唯一例外：旋转只出现在真实等待期间，等待结束立即停止。
- 每个动画都要有明确终态，禁止用 `infinite` 动画表达“进行中”以外的含义。
- `prefers-reduced-motion: reduce` 时去掉位移、旋转、缩放，只保留即时的状态切换。

```css
.chevron {
  transition: transform var(--duration-base) var(--ease-out);
}
[aria-expanded="true"] .chevron {
  transform: rotate(180deg);
}
@media (prefers-reduced-motion: reduce) {
  .chevron,
  [aria-expanded="true"] .chevron {
    transition: none;
    transform: none;
  }
}
```

```css
.spinner {
  animation: icon-spin 800ms linear infinite;
}
@keyframes icon-spin {
  to { transform: rotate(360deg); }
}
@media (prefers-reduced-motion: reduce) {
  .spinner { animation: none; }
}
```

## 9. 自检清单

- [ ] 所有图标的 viewBox 都是 `0 0 24 24`，没有 20、32 或自定义画布。
- [ ] `stroke-width` 只有 1.5 与 1.75 两个值，linecap 与 linejoin 都是 round。
- [ ] 图标只出现在 16、20、24 三档，宽高相等，没有 transform 缩放。
- [ ] 颜色全部走 `currentColor`，页面里没有写死 `stroke` 色值的图标。
- [ ] 纯图标按钮有 `aria-label`，装饰图标都有 `aria-hidden="true"` 与 `focusable="false"`。
- [ ] 没有 emoji、icon font、PNG 或背景图形式的图标。
- [ ] hover、active、disabled、选中四态只改 `color` 与容器背景，未替换图标。
- [ ] 动画只出现在含义变化处，时长 200ms，reduced-motion 下关闭位移与旋转。
