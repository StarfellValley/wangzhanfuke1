# 作品集网站

个人作品集站点，单文件 HTML，零构建、零依赖（除了 Google Fonts）。
交互与动效体系按 [perfolios.](https://directoryyy.com/) 的实测规格实现。

## 本地打开

双击 `index.html` 就行。要看字体和深色模式的最佳效果，用本地服务器起一下更稳：

```bash
python -m http.server 8000
# 然后访问 http://localhost:8000
```

## 目录结构

```
index.html                          主站（就是这个仓库的全部产出）
reference/perfolios-clone.html      照搬 directoryyy.com 的交互参考版，只给自己研究动效用，不是我的设计，别公开分发
archive/2026-10-23-restructure.html 被否掉的「换骨架」版（挑三件讲 / 我怎么做事情 / 全部作品 三段式），留档
img/                                放作品截图的地方，目前是空的
```

## 改内容只动两个地方

都在 `index.html` 的 `<script>` 开头，不用碰 CSS 和 JS 逻辑。

`CFG`（第 410 行起）——名字、邮箱、筛选项、合作过的客户：

```js
person: "陈澄",
contact: "hello@example.com",   // ← 换成你的邮箱
clients: [ {name:"…", rest:"…", url:"…"} ]
```

`WORKS`（第 438 行起）——作品列表，一条一个对象：

```js
{slug:"w01", title:"…", kind:"visual", scene:"xhs", year:"2026",
 hue:212, v:0, blurb:"…", role:"…", deliver:"…", out:["…","…"]}
```

`kind` 取 `CFG.kinds` 里的 `k`，`scene` 取 `CFG.scenes` 里的 `k`，对不上会导致筛选筛不到。

## 换成真图

`img/` 里放图，然后在对应作品上加一个 `img` 字段：

```js
{slug:"w01", …, img:"img/w01-01.jpg"}
```

写了 `img` 就用真图（顶部对齐裁切），没写就退回 `shotHTML()` 用 CSS 画的假网页版式（第 506 行起，4 种版式，由 `v` 决定用哪个）。首屏那叠牌取 `WORKS` 前 5 条。

## 动效体系

想调手感的话，参数集中在 `:root` 的 CSS 变量里：

- `--ease-out-quint: cubic-bezier(.22,1,.36,1)` — 位移、缩放、旋转，500ms
- `--ease-spring: cubic-bezier(.34,1.42,.55,1)` — 近似 Framer 的 `spring{duration:.35, bounce:.15}`，用在滑块指示器上
- 色彩过渡 150ms，独立属性各自过渡（`transform` / `translate` / `scale` / `rotate` 分开写），丝滑感主要来自这里
- 列表筛选重排用 FLIP；行入场用 IntersectionObserver
- 弹窗进出用 `@starting-style` + `transition-behavior: allow-discrete`
- 深浅色切换用 View Transitions 的圆形裁切，`prefers-reduced-motion` 下全部退化为瞬时
- `data-squircle` + `corner-shape: squircle` 是真圆角矩形，只有新版浏览器支持，不支持的就是普通圆角

主题状态存在 `localStorage.theme`，`<html class="dark">`。

## 还没替换的占位内容

- 邮箱 `hello@example.com`
- 三条合作案例（名字是「某技术团队」这类，链接全是 `example.com`）
- 16 条作品数据全是我编的示例，图片一张都没有，现在看到的是 CSS 画的假版式

## 以后想上线

GitHub 上 Settings → Pages，选分支和根目录即可。仓库现在是私有的，免费计划的私有仓库 Pages 有访问限制，要公开访问就把仓库转 Public（**转之前先把上面的占位内容换掉**）。
