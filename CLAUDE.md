# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目性质

这是一个**纯静态 HTML 站点**，无构建系统、无包管理、无依赖。所有练习页都是单文件 HTML（内嵌 CSS + JS），直接部署到 GitHub Pages。站点地址：<https://tangjianfang.github.io/worksheets/>

## 常用命令

```bash
# 本地预览（任选其一）
python -m http.server 8000
# 或
npx serve .

# 部署：直接 git push 到 master / main，.github/workflows/deploy.yml 自动发布
git add . && git commit -m "..." && git push
```

**没有 build / lint / test 命令** — 这是设计选择，所有内容都按最终形态直接提交。改动后用浏览器打开 HTML 验证。

## 目录与文件职责

```
index.html                首页导航卡片，列出所有练习入口
.github/workflows/deploy.yml   GitHub Pages 自动部署

数学/  语文/  英语/  家庭积分/   各科练习 + 参考词库（.txt）
科学/  道德与法治/              空目录，含 .gitkeep 预留
```

每个练习页**完全自包含**（HTML + 内嵌 `<style>` + 内嵌 `<script>`），不引用外部资源，不依赖其他文件（除 `../index.html` 返回首页链接）。

## 单页练习的固定结构

每个 `*.html` 练习页都遵循同一模式，改动时保持一致：

1. **Sticky 控制栏** `#controls`：范围/单元/题型选择、⟳ 重新生成、🖨 打印、（可选）👁 显示答案、← 首页 按钮。控制栏颜色按学科区分：数学 `#1565c0` 蓝、语文 `#2e7d32` 绿、英语 `#1565c0` 蓝。
2. **A4 页面容器** `#a4-page`：`width: 210mm; min-height: 297mm; padding: 12mm`，内部按 `page-title → info-bar (姓名/日期/用时/得分) → 各 section → page-footer` 顺序。
3. **打印样式** `@media print`：隐藏 `#controls`、强制黑白、字号缩小、设 `page-break-inside: avoid` 防止题目跨页。
4. **微信打印引导**：检测 `navigator.userAgent` 含 `MicroMessenger` 时弹遮罩 `<div id="wx-print-mask">`，提示用户去外部浏览器打印。
5. **词库数据**：放在 `<script>` 顶部 `const UNITS = { ... }` 或 `const WORDS = [ ... ]` 之类的常量。**家长会直接编辑这些 JS 数组**，所以要保持人可读、一行一项、按单元分组清晰。

## 添加新练习的检查清单

1. 选对学科目录（数学/语文/英语/家庭积分/其他），文件名用中文描述题型。
2. 复制现有同类型页面作为模板，保留控制栏/A4 容器/打印样式/微信引导四件套。
3. 把词汇/题目数据放在 JS 顶部的常量中，便于家长修订。
4. 在 `index.html` 的对应分组（基础练习 / 强化练习 / 家庭积分）里加一张卡片，链接到新文件。
5. 如属全新学科，目录需保留 `.gitkeep`（已存在于 `科学/` 和 `道德与法治/`）。
6. README.md 同步更新「已有练习」表格和目录结构。

## 设计约束

- **A4 单页可打印**是核心要求：内容必须能放进一张 210×297mm 的纸，不要做长滚动页。`@media print` 必须测试。
- 黑白打印友好：避免依赖颜色区分题目结构（用粗细/边框/符号代替）。
- 移动端可用：手机要能浏览和点打印，所以 `#controls` 在 ≤820px 改为 `position: relative` 并加横向滚动（见 `数学/小学数学加减法练习.html` 的 `@media (max-width: 820px)`）。
- 字体：数学/语文用 `"宋体", "SimSun", serif`；家庭积分用 `"Microsoft YaHei", "微软雅黑"`。
- 所有 UI 文本是中文；按钮图标用 emoji（⟳ 🖨 👁 ←）。

## 教材版本（变更前请确认）

- **语文**：人教统编版一年级下册（22 课 + 8 个语文园地），2025 春新版
- **英语**：沪教牛津深圳版 1B（2025 新版 6 Units）
- **数学**：人教版，与教材弱绑定（口算题模板）
