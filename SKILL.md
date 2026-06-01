---
name: paper-guide
description: "Generate a comprehensive HTML reading guide from an academic paper (PDF). Use this skill whenever the user asks to create a paper reading guide, summarize a paper into HTML, generate a paper walkthrough, or wants to turn a PDF paper into a readable annotated guide. Triggers on phrases like '论文导读', '论文解读', 'paper guide', 'paper reading guide', 'paper walkthrough', '生成导读', '解读这篇论文', '帮我读论文'. Also use when the user provides a PDF paper and asks for an explanation, summary, or annotated version in HTML format."
---

# Paper Guide — 学术论文导读 HTML 生成器

## Overview

读取一篇 PDF 格式的学术论文，生成一份结构清晰、排版精美的单页 HTML 导读文档。导读用中文撰写讲解，引用论文原文时保留原语言。面向有一定专业基础但不一定熟悉该论文具体内容的读者。

## When to Use

- 用户提供了一篇 PDF 论文并要求生成导读/解读
- 用户说"帮我做个论文导读"、"解读一下这篇 paper"
- 用户想把论文转化为易读的 HTML 格式

## Process

### Step 1 — 读取论文

用 Read 工具读取 PDF 文件。如果论文较长（超过 10 页），分批读取（每次 20 页以内）。

读取时重点关注：
- 标题、作者、摘要
- 各章节的标题和层级结构
- 正文内容（包括所有定义、定理、算法、实验描述）
- 图表和公式（记录其位置和含义）
- 结论

**跳过**：参考文献列表、附录（除非附录包含正文引用的关键证明或数据）。


### Step 2 — 构建导读结构

按照论文的原始章节结构组织导读，不要重新发明结构。典型的导读结构如下：

```
1. 论文概览（标题、作者、一句话总结、关键贡献）
2. 研究背景与动机
3. 相关工作（简述，不需要逐条列举）
4. 方法/模型（核心部分，详细讲解）
5. 实验与结果
6. 讨论与局限性
7. 结论
8. 总结评价（你的综合点评）
```

但实际结构应跟随论文本身。如果论文有独特的章节安排，导读也应反映这一点。

### Step 3 — 撰写导读内容

对每个章节：

1. **概括**：用中文概括该章节的核心内容（2-4 句话）
2. **原文引用**：引用论文中的关键段落、定义、公式，保留原语言
3. **讲解**：用中文添加解释性评注，帮助读者理解：
   - 这部分在做什么，为什么重要
   - 关键术语或概念的通俗解释
   - 方法的直觉理解（不只是复述公式）
   - 与领域内其他工作的联系（如果你了解的话）
   - 实验结果的意义和启示

**讲解的分寸**：面向有一定基础的读者，不需要解释基础概念（如什么是神经网络、什么是梯度下降），但对论文特有的新概念、新术语、新方法应该给出清晰的解释。

**内容完整性**：导读应覆盖论文正文的所有实质性章节。读者读完导读后，应该对论文的完整内容有全面的了解，不应遗漏任何重要章节。

### Step 4 — 生成 HTML

生成一个自包含的 HTML 文件，所有样式内联在 `<style>` 标签中，不依赖外部资源。

#### 设计要求

**整体风格**：
- 浅色背景（`#f8f9fa` 或类似的温暖浅灰）
- 正文区域白色背景卡片，带轻微阴影
- 最大宽度 `860px`，居中
- 适当的行间距（`1.8` 以上）和段间距

**字体**：
- 中文正文：`"Noto Serif SC", "Source Han Serif SC", "思源宋体", serif`
- 英文/代码：`"JetBrains Mono", "Fira Code", "Source Code Pro", monospace`
- 标题可使用：`"Noto Sans SC", "Source Han Sans SC", "思源黑体", sans-serif`

**色彩方案**：
- 正文：`#2c3e50`
- 标题：`#1a252f`
- 引用块背景：`#f0f4f8`，左边框 `#3498db`
- 讲解/评注块背景：`#fef9e7`，左边框 `#f39c12`
- 链接：`#2980b9`

**排版元素**：

论文原文引用使用引用块样式：
```html
<blockquote class="paper-quote">
  <p>原文内容...</p>
</blockquote>
```

讲解评注使用专门的评注块：
```html
<div class="commentary">
  <div class="commentary-label">导读讲解</div>
  <p>讲解内容...</p>
</div>
```

图表说明使用 `.figure-note` 块，用文字描述图表内容和含义（读者对照原 PDF 查看图表）：
```html
<div class="figure-note">
  <div class="figure-note-label">图 X 说明</div>
  <p>图表内容的文字描述和讲解...</p>
</div>
```

公式使用代码块或保留原始 LaTeX 记法（如果有 MathJax 可用则使用 MathJax）：
```html
<!-- 在 <head> 中加入 MathJax -->
<script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
```

行内公式用 `\(...\)`，独立公式用 `\[...\]`。

**章节导航**：
- 页面顶部生成可点击的目录
- 每个章节标题有锚点 `id`
- 提供"回到顶部"的浮动按钮

#### HTML 模板骨架

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>论文导读：[论文标题]</title>
  <script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
  <style>
    /* === 全局 === */
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      background: #f8f9fa;
      color: #2c3e50;
      font-family: "Noto Serif SC", "Source Han Serif SC", serif;
      line-height: 1.9;
      padding: 2rem 1rem;
    }
    .container {
      max-width: 860px;
      margin: 0 auto;
      background: #fff;
      padding: 3rem 3.5rem;
      border-radius: 12px;
      box-shadow: 0 2px 20px rgba(0,0,0,0.06);
    }

    /* === 标题 === */
    h1 {
      font-family: "Noto Sans SC", "Source Han Sans SC", sans-serif;
      font-size: 1.8rem;
      color: #1a252f;
      border-bottom: 3px solid #3498db;
      padding-bottom: 0.8rem;
      margin-bottom: 1.5rem;
    }
    h2 {
      font-family: "Noto Sans SC", "Source Han Sans SC", sans-serif;
      font-size: 1.4rem;
      color: #1a252f;
      margin-top: 2.5rem;
      margin-bottom: 1rem;
      padding-left: 0.8rem;
      border-left: 4px solid #3498db;
    }
    h3 {
      font-size: 1.15rem;
      color: #2c3e50;
      margin-top: 1.8rem;
      margin-bottom: 0.6rem;
    }

    /* === 论文元信息 === */
    .meta {
      background: #eef2f7;
      padding: 1.2rem 1.5rem;
      border-radius: 8px;
      margin-bottom: 2rem;
      font-size: 0.95rem;
    }
    .meta .title { font-size: 1.2rem; font-weight: bold; color: #1a252f; }
    .meta .authors { color: #555; margin-top: 0.3rem; }
    .meta .venue { color: #777; font-style: italic; margin-top: 0.2rem; }

    /* === 目录 === */
    .toc {
      background: #f7f9fc;
      border: 1px solid #e3e8ee;
      border-radius: 8px;
      padding: 1.2rem 1.5rem;
      margin-bottom: 2.5rem;
    }
    .toc-title {
      font-family: "Noto Sans SC", sans-serif;
      font-weight: bold;
      margin-bottom: 0.5rem;
      color: #1a252f;
    }
    .toc a {
      color: #2980b9;
      text-decoration: none;
      display: block;
      padding: 0.2rem 0;
    }
    .toc a:hover { text-decoration: underline; }

    /* === 原文引用 === */
    .paper-quote {
      background: #f0f4f8;
      border-left: 4px solid #3498db;
      padding: 1rem 1.2rem;
      margin: 1rem 0;
      border-radius: 0 6px 6px 0;
      font-size: 0.93rem;
      color: #34495e;
    }

    /* === 导读讲解 === */
    .commentary {
      background: #fef9e7;
      border-left: 4px solid #f39c12;
      padding: 1rem 1.2rem;
      margin: 1rem 0;
      border-radius: 0 6px 6px 0;
    }
    .commentary-label {
      font-family: "Noto Sans SC", sans-serif;
      font-size: 0.8rem;
      font-weight: bold;
      color: #e67e22;
      margin-bottom: 0.4rem;
      text-transform: uppercase;
      letter-spacing: 0.05em;
    }

    /* === 图表说明 === */
    .figure-note {
      background: #f5f0ff;
      border-left: 4px solid #8e44ad;
      padding: 0.8rem 1.2rem;
      margin: 1rem 0;
      border-radius: 0 6px 6px 0;
      font-size: 0.9rem;
    }
    .figure-note-label {
      font-weight: bold;
      color: #8e44ad;
      font-size: 0.8rem;
      margin-bottom: 0.3rem;
    }

    /* === 表格 === */
    table {
      width: 100%;
      border-collapse: collapse;
      margin: 1rem 0;
      font-size: 0.9rem;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 0.5rem 0.8rem;
      text-align: left;
    }
    th { background: #f0f4f8; font-weight: bold; }

    /* === 代码/公式 === */
    code {
      font-family: "JetBrains Mono", "Fira Code", monospace;
      background: #f4f4f4;
      padding: 0.15rem 0.4rem;
      border-radius: 3px;
      font-size: 0.88rem;
    }
    pre {
      background: #f8f8f8;
      padding: 1rem;
      border-radius: 6px;
      overflow-x: auto;
      font-size: 0.85rem;
      line-height: 1.5;
    }

    /* === 段落 === */
    p { margin-bottom: 0.9rem; }

    /* === 回到顶部 === */
    .back-to-top {
      position: fixed;
      bottom: 2rem;
      right: 2rem;
      background: #3498db;
      color: #fff;
      border: none;
      border-radius: 50%;
      width: 44px;
      height: 44px;
      font-size: 1.2rem;
      cursor: pointer;
      box-shadow: 0 2px 8px rgba(0,0,0,0.15);
      display: none;
    }
    .back-to-top:hover { background: #2980b9; }

    /* === 响应式 === */
    @media (max-width: 640px) {
      .container { padding: 1.5rem 1.2rem; }
      h1 { font-size: 1.4rem; }
      h2 { font-size: 1.2rem; }
    }

    /* === 总结评价 === */
    .summary-box {
      background: linear-gradient(135deg, #e8f4fd, #f0f7ee);
      border: 1px solid #b8d4e8;
      border-radius: 8px;
      padding: 1.5rem;
      margin-top: 2rem;
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- 论文元信息 -->
    <div class="meta">
      <div class="title">[论文标题]</div>
      <div class="authors">[作者列表]</div>
      <div class="venue">[发表会议/期刊, 年份]</div>
    </div>

    <h1>论文导读</h1>

    <!-- 一句话总结 -->
    <p><strong>一句话总结：</strong>[用中文一句话概括论文的核心贡献]</p>

    <!-- 目录 -->
    <nav class="toc">
      <div class="toc-title">目录</div>
      <a href="#sec-overview">1. 论文概览</a>
      <a href="#sec-background">2. 研究背景与动机</a>
      <!-- ... 按实际章节填充 ... -->
    </nav>

    <!-- 各章节内容 -->
    <section id="sec-overview">
      <h2>1. 论文概览</h2>
      <p>[中文概括]</p>
      <blockquote class="paper-quote">
        <p>[原文关键段落]</p>
      </blockquote>
      <div class="commentary">
        <div class="commentary-label">导读讲解</div>
        <p>[你的讲解和评注]</p>
      </div>
    </section>

    <!-- ... 更多章节 ... -->

    <!-- 总结评价 -->
    <section id="sec-conclusion">
      <h2>总结评价</h2>
      <div class="summary-box">
        <p>[对论文的整体评价：贡献、优点、不足、对领域的影响]</p>
      </div>
    </section>
  </div>

  <!-- 回到顶部按钮 -->
  <button class="back-to-top" onclick="window.scrollTo({top:0,behavior:'smooth'})" id="backToTop">↑</button>
  <script>
    window.addEventListener('scroll', function() {
      document.getElementById('backToTop').style.display =
        window.scrollY > 300 ? 'block' : 'none';
    });
  </script>
</body>
</html>
```

### Step 5 — 输出文件

将生成的 HTML 保存到与 PDF 同目录下，文件名为 `[论文标题简写]-导读.html`。如果论文标题很长，截取前几个关键词。

用中文告知用户文件已生成，并简要说明导读的主要结构。

## Writing Guidelines

### 讲解的语气

- 像一位资深同行在向同事介绍一篇论文，既专业又易懂
- 避免学生论文式的冗余（"众所周知..."、"近年来..."）
- 直接切入要点，用简练的语言解释复杂概念
- 可以表达对论文方法的评价（巧妙之处、潜在问题），但要有理有据

### 引用原文的原则

- 关键定义、定理、假设 — 必须引用原文
- 实验设置和结果中的关键数据 — 引用原文
- 一般性描述 — 用中文概括即可，不必引用
- 引用时保留原语言（英文论文引英文，中文论文引中文）

### 公式处理

- 论文中的重要公式用 MathJax 渲染
- 行内公式：`\( formula \)`
- 独立公式：`\[ formula \]`
- 在公式后用中文解释各符号的含义

### 图表处理

- 由于 HTML 中无法直接嵌入 PDF 中的图片，用文字描述图表内容
- 使用 `.figure-note` 样式块来描述图表
- 如果图表对理解论文至关重要，详细描述其内容和含义

## Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| 导读变成了翻译 | 导读是讲解，不是逐句翻译。概括+引用关键句+评注 |
| 跳过"看起来不重要"的章节 | 除参考文献和附录外，所有章节都应覆盖 |
| 讲解太浅，只是复述 | 要加入理解和评价，帮读者建立直觉 |
| 公式不解释 | 每个重要公式后都应说明各符号含义和直觉理解 |
| HTML 依赖外部 CSS/JS | 除 MathJax CDN 外，所有样式必须内联 |
| 背景色太深或太花哨 | 保持浅色、素雅、专业的学术风格 |
