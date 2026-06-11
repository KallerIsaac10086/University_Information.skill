# 精美网页生成器

在生成 Markdown 调研报告后，**主动询问用户**是否需要生成一个精美的可互动网页来展示数据。如果用户同意，则根据下方的 HTML 模板生成一个自包含的网页文件。

---

## 1. 工作流程

```
[报告生成完毕] → [询问用户：是否生成精美网页？]
                         ↓ 用户同意
                  [按模板生成 HTML 文件]
                         ↓
                  [告知用户文件路径，建议用浏览器打开]
```

---

## 2. 网页功能要求

生成的网页必须包含以下特性：

### 布局结构（从上到下）
1. **导航栏**（固定顶部）：大学名称 + 快速锚点
2. **Hero 区**：大字展示综合生存指数 + 评级徽章 + 星级图标
3. **雷达图区**：用 Chart.js 绘制四维雷达图（住宿条件、学业氛围、生活便利、自由程度）
4. **维度卡片区**：4 张卡片横向排列，每张显示维度名、得分、权重、进度条
5. **详细明细区**：4 个可折叠面板，每个面板内列出该维度下所有问题的得分和回答内容
6. **总结与建议区**：结合用户权重给出个性化评价
7. **页脚**：数据来源 cn.colleges.chat + 生成时间

### 交互效果
- 雷达图带淡入动画
- 维度卡片悬停放大效果
- 详细明细区支持**折叠/展开**（默认展开第一个）
- 导航栏锚点平滑滚动
- 评分进度条带渐变色动画

---

## 3. 必须使用的技术栈

- **纯 HTML + CSS + JS**（单文件自包含，可直接用浏览器打开）
- **Chart.js** CDN 绘制雷达图：`https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js`
- 不使用任何其他外部框架

---

## 4. HTML 模板

以下是**必须使用**的 HTML 模板。生成时，将 `{{PLACEHOLDER}}` 替换为实际数据：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{大学名称}} · 校园生活调研报告</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<style>
/* ========== 基础样式 ========== */
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", sans-serif;
  background: linear-gradient(135deg, #0f0c29 0%, #302b63 50%, #24243e 100%);
  color: #e0e0e0;
  line-height: 1.6;
  min-height: 100vh;
}

/* ========== 导航栏 ========== */
.navbar {
  position: fixed; top: 0; left: 0; right: 0; z-index: 1000;
  background: rgba(15, 12, 41, 0.95);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid rgba(255,255,255,0.08);
  padding: 0 24px;
  display: flex; align-items: center; justify-content: space-between;
  height: 56px;
}
.navbar .logo { font-size: 1.1rem; font-weight: 700; color: #a78bfa; }
.navbar .nav-links { display: flex; gap: 20px; list-style: none; }
.navbar .nav-links a {
  color: #ccc; text-decoration: none; font-size: 0.875rem;
  transition: color 0.2s;
}
.navbar .nav-links a:hover { color: #a78bfa; }

/* ========== 主容器 ========== */
.container { max-width: 1200px; margin: 0 auto; padding: 80px 24px 60px; }

/* ========== Hero 区 ========== */
.hero {
  text-align: center; padding: 60px 0 40px;
}
.hero .university-name {
  font-size: 2.5rem; font-weight: 800; color: #fff;
  background: linear-gradient(135deg, #a78bfa, #f472b6);
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  background-clip: text;
}
.hero .score-circle {
  display: inline-flex; align-items: center; justify-content: center;
  width: 160px; height: 160px; border-radius: 50%;
  background: linear-gradient(135deg, rgba(167,139,250,0.2), rgba(244,114,182,0.2));
  border: 4px solid rgba(167,139,250,0.5);
  margin: 24px 0 12px;
  flex-direction: column;
}
.hero .score-number {
  font-size: 3.5rem; font-weight: 800;
  background: linear-gradient(135deg, #a78bfa, #f472b6);
  -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  background-clip: text;
  line-height: 1;
}
.hero .score-label { font-size: 0.85rem; color: #999; margin-top: 4px; }
.hero .rating-badge {
  display: inline-block; padding: 6px 20px; border-radius: 20px;
  font-size: 1rem; font-weight: 600; margin-top: 8px;
}
.rating-badge.excellent { background: rgba(16,185,129,0.2); color: #10b981; border: 1px solid rgba(16,185,129,0.3); }
.rating-badge.good     { background: rgba(59,130,246,0.2); color: #3b82f6; border: 1px solid rgba(59,130,246,0.3); }
.rating-badge.average  { background: rgba(245,158,11,0.2); color: #f59e0b; border: 1px solid rgba(245,158,11,0.3); }
.rating-badge.poor     { background: rgba(239,68,68,0.2); color: #ef4444; border: 1px solid rgba(239,68,68,0.3); }
.rating-badge.hard     { background: rgba(107,114,128,0.2); color: #6b7280; border: 1px solid rgba(107,114,128,0.3); }

/* ========== 区块标题 ========== */
.section-title {
  font-size: 1.6rem; font-weight: 700; color: #e0e0e0;
  text-align: center; margin: 48px 0 28px;
}
.section-title::after {
  content: ''; display: block; width: 60px; height: 3px;
  background: linear-gradient(135deg, #a78bfa, #f472b6);
  margin: 10px auto 0; border-radius: 2px;
}

/* ========== 雷达图容器 ========== */
.chart-container {
  max-width: 500px; margin: 0 auto;
  background: rgba(255,255,255,0.03);
  border-radius: 16px; padding: 24px;
  border: 1px solid rgba(255,255,255,0.06);
}

/* ========== 维度卡片 ========== */
.dimension-cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 20px; margin-top: 8px;
}
.dim-card {
  background: rgba(255,255,255,0.04);
  border: 1px solid rgba(255,255,255,0.06);
  border-radius: 16px; padding: 24px;
  transition: transform 0.3s, box-shadow 0.3s;
  cursor: pointer;
}
.dim-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 40px rgba(0,0,0,0.3);
}
.dim-card .dim-icon { font-size: 2rem; margin-bottom: 8px; }
.dim-card .dim-name { font-size: 1.1rem; font-weight: 700; color: #fff; }
.dim-card .dim-score { font-size: 2rem; font-weight: 800; margin: 8px 0; }
{{维度卡片渐变色占位 - 通过 JS 动态设置或提前预置四个维度的颜色类}}

.dim-card .dim-weight { font-size: 0.8rem; color: #888; }
.dim-card .progress-bar {
  margin-top: 12px; height: 6px; border-radius: 3px;
  background: rgba(255,255,255,0.08); overflow: hidden;
}
.dim-card .progress-fill {
  height: 100%; border-radius: 3px;
  transition: width 1s ease;
}

/* ========== 明细折叠面板 ========== */
.detail-section { margin-top: 8px; }
.detail-panel {
  background: rgba(255,255,255,0.03);
  border: 1px solid rgba(255,255,255,0.06);
  border-radius: 12px; margin-bottom: 12px; overflow: hidden;
}
.detail-header {
  display: flex; align-items: center; justify-content: space-between;
  padding: 16px 20px; cursor: pointer; user-select: none;
  transition: background 0.2s;
}
.detail-header:hover { background: rgba(255,255,255,0.04); }
.detail-header .header-left { display: flex; align-items: center; gap: 12px; }
.detail-header .header-icon { font-size: 1.4rem; }
.detail-header .header-title { font-size: 1.05rem; font-weight: 600; color: #e0e0e0; }
.detail-header .header-score {
  font-size: 0.9rem; font-weight: 700; padding: 2px 12px;
  border-radius: 12px;
}
.detail-header .arrow {
  font-size: 0.8rem; color: #666;
  transition: transform 0.3s;
}
.detail-header.open .arrow { transform: rotate(180deg); }

.detail-body {
  max-height: 0; overflow: hidden;
  transition: max-height 0.4s ease, padding 0.4s ease;
}
.detail-body.expanded { max-height: 2000px; padding: 0 20px 20px; }

.detail-item {
  display: flex; align-items: center; justify-content: space-between;
  padding: 10px 0; border-bottom: 1px solid rgba(255,255,255,0.04);
}
.detail-item:last-child { border-bottom: none; }
.detail-item .item-label { color: #bbb; font-size: 0.9rem; }
.detail-item .item-value { color: #888; font-size: 0.85rem; max-width: 50%; text-align: right; }
.detail-item .item-score {
  display: inline-block; min-width: 36px; text-align: center;
  font-weight: 700; font-size: 0.9rem; margin-left: 8px;
}
.score-high  { color: #10b981; }
.score-mid   { color: #f59e0b; }
.score-low   { color: #ef4444; }

/* ========== 总结区 ========== */
.summary-box {
  background: rgba(167,139,250,0.08);
  border: 1px solid rgba(167,139,250,0.2);
  border-radius: 16px; padding: 28px; margin-top: 8px;
}
.summary-box p { color: #ccc; margin-bottom: 12px; line-height: 1.8; }

/* ========== 页脚 ========== */
.footer {
  text-align: center; padding: 40px 24px;
  border-top: 1px solid rgba(255,255,255,0.06);
  color: #666; font-size: 0.85rem; margin-top: 48px;
}
.footer a { color: #a78bfa; text-decoration: none; }

/* ========== 回到顶部按钮 ========== */
.back-to-top {
  position: fixed; bottom: 32px; right: 32px;
  width: 44px; height: 44px; border-radius: 50%;
  background: rgba(167,139,250,0.3);
  border: 1px solid rgba(167,139,250,0.4);
  color: #a78bfa; font-size: 1.2rem; cursor: pointer;
  display: flex; align-items: center; justify-content: center;
  opacity: 0; transition: opacity 0.3s; z-index: 100;
}
.back-to-top.visible { opacity: 1; }

/* ========== 响应式 ========== */
@media (max-width: 768px) {
  .hero .university-name { font-size: 1.6rem; }
  .hero .score-number { font-size: 2.5rem; }
  .hero .score-circle { width: 120px; height: 120px; }
  .dimension-cards { grid-template-columns: 1fr; }
  .navbar .nav-links { gap: 10px; }
  .navbar .nav-links a { font-size: 0.75rem; }
}
</style>
</head>
<body>

<!-- ========== 导航栏 ========== -->
<nav class="navbar">
  <div class="logo">🎓 大学调研</div>
  <ul class="nav-links">
    <li><a href="#hero">总览</a></li>
    <li><a href="#chart">雷达图</a></li>
    <li><a href="#dimensions">维度</a></li>
    <li><a href="#details">明细</a></li>
    <li><a href="#summary">总结</a></li>
  </ul>
</nav>

<div class="container">

<!-- ========== Hero 区 ========== -->
<section id="hero" class="hero">
  <h1 class="university-name">{{大学名称}}</h1>
  <div class="score-circle">
    <span class="score-number">{{综合生存指数}}</span>
    <span class="score-label">综合生存指数</span>
  </div>
  <div class="rating-badge {{评级CSS类}}">
    {{星级图标}} {{评级文字}}（{{分数范围}}分）
  </div>
</section>

<!-- ========== 雷达图 ========== -->
<section id="chart">
  <h2 class="section-title">📊 四维度雷达图</h2>
  <div class="chart-container">
    <canvas id="radarChart" height="400"></canvas>
  </div>
</section>

<!-- ========== 维度卡片 ========== -->
<section id="dimensions">
  <h2 class="section-title">📋 维度得分总览</h2>
  <div class="dimension-cards" id="dimensionCards">
    <!-- JS 动态生成 -->
  </div>
</section>

<!-- ========== 详细明细 ========== -->
<section id="details">
  <h2 class="section-title">🔍 详细评分明细</h2>
  <div class="detail-section" id="detailSection">
    <!-- JS 动态生成 -->
  </div>
</section>

<!-- ========== 总结 ========== -->
<section id="summary">
  <h2 class="section-title">💡 总结与建议</h2>
  <div class="summary-box">
    {{总结与建议内容，使用<p>分段}}
  </div>
</section>

</div>

<!-- ========== 页脚 ========== -->
<footer class="footer">
  <p>数据来源：<a href="https://cn.colleges.chat/" target="_blank">cn.colleges.chat（大学生活质量指北）</a></p>
  <p>评分基于校友公开反馈，仅供参考 · 生成时间：{{生成时间}}</p>
</footer>

<!-- ========== 回到顶部 ========== -->
<button class="back-to-top" id="backToTop" onclick="window.scrollTo({top:0,behavior:'smooth'})">↑</button>

<script>
// ========== 所有数据 ==========
const universityData = {
  name: "{{大学名称}}",
  overallScore: {{综合生存指数}},
  ratingLabel: "{{评级文字}}",
  ratingClass: "{{评级CSS类}}",
  stars: "{{星级图标}}",
  scoreRange: "{{分数范围}}",
  generatedTime: "{{生成时间}}",
  dimensions: [
    {
      id: "accommodation",
      name: "住宿条件",
      icon: "🏠",
      score: {{住宿条件得分}},
      weight: {{住宿条件权重}},  // 小数，如 0.30
      color: "#a78bfa",
      items: [
        { label: "上床下桌", value: "{{上床下桌·实际回答}}", score: {{上床下桌·得分}} },
        { label: "空调",     value: "{{空调·实际回答}}",     score: {{空调·得分}} },
        { label: "独立卫浴", value: "{{独立卫浴·实际回答}}", score: {{独立卫浴·得分}} },
        { label: "洗衣机",   value: "{{洗衣机·实际回答}}",   score: {{洗衣机·得分}} },
        { label: "电瓶车",   value: "{{电瓶车·实际回答}}",   score: {{电瓶车·得分}} },
        { label: "限电",     value: "{{限电·实际回答}}",     score: {{限电·得分}} },
        { label: "超市",     value: "{{超市·实际回答}}",     score: {{超市·得分}} }
      ]
    },
    {
      id: "academics",
      name: "学业氛围",
      icon: "🎓",
      score: {{学业氛围得分}},
      weight: {{学业氛围权重}},
      color: "#60a5fa",
      items: [
        { label: "早晚自习", value: "{{早晚自习·实际回答}}", score: {{早晚自习·得分}} },
        { label: "晨跑",     value: "{{晨跑·实际回答}}",     score: {{晨跑·得分}} },
        { label: "跑步打卡", value: "{{跑步打卡·实际回答}}", score: {{跑步打卡·得分}} },
        { label: "通宵自习", value: "{{通宵自习·实际回答}}", score: {{通宵自习·得分}} },
        { label: "带电脑",   value: "{{带电脑·实际回答}}",   score: {{带电脑·得分}} },
        { label: "校园网",   value: "{{校园网·实际回答}}",   score: {{校园网·得分}} }
      ]
    },
    {
      id: "convenience",
      name: "生活便利",
      icon: "🎉",
      score: {{生活便利得分}},
      weight: {{生活便利权重}},
      color: "#f59e0b",
      items: [
        { label: "外卖",     value: "{{外卖·实际回答}}",     score: {{外卖·得分}} },
        { label: "交通",     value: "{{交通·实际回答}}",     score: {{交通·得分}} },
        { label: "食堂价格", value: "{{食堂价格·实际回答}}", score: {{食堂价格·得分}} },
        { label: "热水供应", value: "{{热水供应·实际回答}}", score: {{热水供应·得分}} },
        { label: "消费方式", value: "{{消费方式·实际回答}}", score: {{消费方式·得分}} },
        { label: "银行卡",   value: "{{银行卡·实际回答}}",   score: {{银行卡·得分}} },
        { label: "快递",     value: "{{快递·实际回答}}",     score: {{快递·得分}} },
        { label: "共享单车", value: "{{共享单车·实际回答}}", score: {{共享单车·得分}} }
      ]
    },
    {
      id: "freedom",
      name: "自由程度",
      icon: "🔓",
      score: {{自由程度得分}},
      weight: {{自由程度权重}},
      color: "#f472b6",
      items: [
        { label: "断电断网",     value: "{{断电断网·实际回答}}",     score: {{断电断网·得分}} },
        { label: "寒暑假",       value: "{{寒暑假·实际回答}}",       score: {{寒暑假·得分}} },
        { label: "门禁",         value: "{{门禁·实际回答}}",         score: {{门禁·得分}} },
        { label: "查寝封寝晚归", value: "{{查寝封寝晚归·实际回答}}", score: {{查寝封寝晚归·得分}} }
      ]
    }
  ],
  summary: "{{总结与建议HTML}}"
};

// ========== 渲染维度卡片 ==========
function renderDimensionCards() {
  const container = document.getElementById('dimensionCards');
  universityData.dimensions.forEach(dim => {
    const card = document.createElement('div');
    card.className = 'dim-card';
    card.onclick = () => {
      document.getElementById('panel-' + dim.id).scrollIntoView({ behavior: 'smooth' });
    };
    card.innerHTML = `
      <div class="dim-icon">${dim.icon}</div>
      <div class="dim-name">${dim.name}</div>
      <div class="dim-score" style="color:${dim.color}">${dim.score.toFixed(1)}<span style="font-size:0.9rem;color:#888">/10</span></div>
      <div class="dim-weight">权重 ${(dim.weight * 100).toFixed(0)}% · 加权 ${(dim.score * dim.weight).toFixed(2)}</div>
      <div class="progress-bar">
        <div class="progress-fill" style="width:0%;background:${dim.color};" data-width="${dim.score * 10}%"></div>
      </div>
    `;
    container.appendChild(card);
  });
  // 延迟执行进度条动画
  setTimeout(() => {
    document.querySelectorAll('.progress-fill').forEach(el => {
      el.style.width = el.dataset.width;
    });
  }, 300);
}

// ========== 渲染明细面板 ==========
function renderDetails() {
  const container = document.getElementById('detailSection');
  universityData.dimensions.forEach((dim, index) => {
    const panel = document.createElement('div');
    panel.className = 'detail-panel';
    panel.id = 'panel-' + dim.id;

    const header = document.createElement('div');
    header.className = 'detail-header';
    // 第一个默认展开
    const isFirst = index === 0;
    if (isFirst) header.classList.add('open');

    header.innerHTML = `
      <div class="header-left">
        <span class="header-icon">${dim.icon}</span>
        <span class="header-title">${dim.name}</span>
        <span class="header-score" style="background:${dim.color}22;color:${dim.color}">${dim.score.toFixed(1)} / 10</span>
      </div>
      <span class="arrow">▼</span>
    `;

    const body = document.createElement('div');
    body.className = 'detail-body';
    if (isFirst) body.classList.add('expanded');

    dim.items.forEach(item => {
      const div = document.createElement('div');
      div.className = 'detail-item';
      let scoreClass = 'score-mid';
      if (item.score >= 8) scoreClass = 'score-high';
      else if (item.score <= 4) scoreClass = 'score-low';
      div.innerHTML = `
        <span class="item-label">${item.label}</span>
        <span class="item-value">${item.value}</span>
        <span class="item-score ${scoreClass}">${item.score}</span>
      `;
      body.appendChild(div);
    });

    header.addEventListener('click', () => {
      header.classList.toggle('open');
      body.classList.toggle('expanded');
    });

    panel.appendChild(header);
    panel.appendChild(body);
    container.appendChild(panel);
  });
}

// ========== 绘制雷达图 ==========
function renderRadarChart() {
  const ctx = document.getElementById('radarChart').getContext('2d');
  new Chart(ctx, {
    type: 'radar',
    data: {
      labels: universityData.dimensions.map(d => d.name),
      datasets: [{
        label: '维度得分',
        data: universityData.dimensions.map(d => d.score),
        backgroundColor: 'rgba(167,139,250,0.2)',
        borderColor: 'rgba(167,139,250,0.8)',
        borderWidth: 2,
        pointBackgroundColor: universityData.dimensions.map(d => d.color),
        pointBorderColor: '#fff',
        pointBorderWidth: 2,
        pointRadius: 5,
        pointHoverRadius: 7
      }]
    },
    options: {
      responsive: true,
      animation: { duration: 1200, easing: 'easeOutQuart' },
      scales: {
        r: {
          beginAtZero: true,
          max: 10,
          min: 0,
          ticks: {
            stepSize: 2,
            color: '#666',
            backdropColor: 'transparent',
            font: { size: 11 }
          },
          grid: { color: 'rgba(255,255,255,0.1)' },
          angleLines: { color: 'rgba(255,255,255,0.1)' },
          pointLabels: {
            color: '#ccc',
            font: { size: 13, weight: 'bold' }
          }
        }
      },
      plugins: {
        legend: { display: false }
      }
    }
  });
}

// ========== 回到顶部按钮 ==========
function initBackToTop() {
  const btn = document.getElementById('backToTop');
  window.addEventListener('scroll', () => {
    btn.classList.toggle('visible', window.scrollY > 400);
  });
}

// ========== 导航锚点平滑滚动 ==========
function initNavLinks() {
  document.querySelectorAll('.nav-links a').forEach(link => {
    link.addEventListener('click', e => {
      e.preventDefault();
      const target = document.querySelector(link.getAttribute('href'));
      if (target) target.scrollIntoView({ behavior: 'smooth', block: 'start' });
    });
  });
}

// ========== 初始化 ==========
document.addEventListener('DOMContentLoaded', () => {
  renderDimensionCards();
  renderDetails();
  renderRadarChart();
  initBackToTop();
  initNavLinks();
});
</script>

</body>
</html>
```

---

## 5. 生成规则

### 5.1 占位符替换规则

| 占位符 | 数据来源 | 格式说明 |
|--------|----------|----------|
| `{{大学名称}}` | 用户查询的大学名 | 如：西南政法大学 |
| `{{综合生存指数}}` | scoring-guide.md 计算结果 | 保留两位小数，如：7.67 |
| `{{评级CSS类}}` | 根据评分映射 | excellent / good / average / poor / hard |
| `{{星级图标}}` | 根据评分映射 | ⭐⭐⭐⭐⭐ / ⭐⭐⭐⭐ / ⭐⭐⭐ / ⭐⭐ / ⭐ |
| `{{评级文字}}` | 根据评分映射 | 顶级体验 / 良好 / 一般 / 较差 / 艰苦 |
| `{{分数范围}}` | 根据评分映射 | 如：7-8分 |
| `{{生成时间}}` | 当前时间 | 格式：YYYY-MM-DD HH:mm |
| `{{住宿条件得分}}` 等 | scoring-guide 维度均分 | 保留一位小数，如：8.2 |
| `{{住宿条件权重}}` 等 | 用户指定的权重或默认 0.25 | 小数，如：0.30 |
| `{{上床下桌·得分}}` 等 | scoring-guide 逐项打分结果 | 整数 1-10 |
| `{{上床下桌·实际回答}}` | data-fetching 中获取的校友回答 | 精简到 15 字以内 |

### 5.2 评级映射表

```
评分 >= 9.0  →  excellent, ⭐⭐⭐⭐⭐, 顶级体验, 9-10分
评分 >= 7.0  →  good,      ⭐⭐⭐⭐,   良好,     7-8分
评分 >= 5.0  →  average,   ⭐⭐⭐,    一般,     5-6分
评分 >= 3.0  →  poor,      ⭐⭐,      较差,     3-4分
评分 <  3.0  →  hard,      ⭐,        艰苦,     1-2分
```

### 5.3 颜色规则

进度条和维度颜色固定使用：
- 住宿条件：`#a78bfa`（紫色）
- 学业氛围：`#60a5fa`（蓝色）
- 生活便利：`#f59e0b`（橙色）
- 自由程度：`#f472b6`（粉色）

### 5.4 总结与建议生成原则

`{{总结与建议内容}}` 和 `{{总结与建议HTML}}` 应根据以下逻辑生成 3-5 段 HTML 内容：

1. **总体评价**：根据综合生存指数给出整体印象
2. **亮点**：得分最高的维度，说明该校优势
3. **短板**：得分最低的维度，提示需要注意
4. **权重建议**：结合用户指定的权重，指出该校在最看重的方面表现如何
5. **横向对比**：该分数在全国高校中的大致定位（基于经验知识）

生成格式：每段用 `<p>...</p>` 包裹。

### 5.5 文件命名与输出

生成的文件命名为：`{大学拼音}-report.html`（如 `xi-nan-zheng-fa-da-xue-report.html`），放在当前工作目录下。

生成后告知用户文件路径，建议用浏览器打开。

---

## 6. 注意事项

- **严格使用上述模板**，不得自行更改布局结构
- 占位符必须**全部替换**为实际数据，不得留空
- 如果某个问题的数据未获取到（网站无此大学数据），在 `actual_answer` 中写"暂无数据"，score 用 `-` 表示
- 如果未获取到数据的条目超过一半，告知用户数据不足，建议不生成网页
- 生成的 HTML 文件必须是**可直接用浏览器打开**的完整文件
