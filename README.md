# 🎓 大学信息获取 Skill

基于 [cn.colleges.chat](https://cn.colleges.chat/)（大学生活质量指北）的 AI Skill，兼容 **CodeBuddy、Claude Code、Cursor、Copilot Chat、Cline** 等主流 AI 编程助手，用于调研中国大学校园生活条件，并生成综合评分报告和精美互动网页。

---

## 安装

让 AI 助手直接克隆本仓库并安装 skill 文件。对 AI 说：

```text
克隆 https://cnb.cool/Isaac80686/University_Information.skill，把 skill/ 目录下的所有文件复制到当前项目的 skill 目录中，并安装这个skill。
```

各平台 skill 目录路径参考：

| 平台 | Skill 安装路径 |
|------|---------------|
| CodeBuddy | `.codebuddy/skills/skill/` |
| Claude Code | `.claude/skills/skill/` |
| Cursor | `.cursor/skills/skill/` |
| Copilot Chat | `.github/copilot/skills/skill/` |
| Cline | `.cline/skills/skill/` |

---

## 快速开始

安装后，对 AI 说：

```
帮我调研一下中国政法大学
```

AI 会自动调用此 Skill，获取该大学在校友社区中的真实反馈，生成四维度评分报告，并可选输出精美网页。

---

## 功能

| 功能 | 说明 |
|------|------|
| 🔍 **校园问答** | 查询某大学的宿舍、空调、卫浴、自习、门禁等具体问题 |
| 📊 **综合评分** | 按住宿/学业/生活/自由四个维度打分，加权计算综合生存指数 |
| 🏆 **调研报告** | 结构化 Markdown 报告，含逐项得分、校友引用、总结建议 |
| 📄 **MD 产物交付** | 调研结束后自动输出 `.md` 文件作为最终交付物 |
| 🌐 **互动网页** | 可选生成 Chart.js 雷达图 + 折叠明细 + 进度条动画网页 |
| 🔄 **自动检查更新** | 每次调用时自动检查远端仓库是否有新版本，提示用户更新 |

---

## 评分体系

四个维度，用户可自定义权重（默认各 25%）：

| 维度 | 涵盖内容 |
|------|----------|
| 🏠 住宿条件 | 上床下桌、空调、独立卫浴、洗衣机、电瓶车、限电、超市 |
| 🎓 学业氛围 | 早晚自习、晨跑、跑步打卡、通宵自习、带电脑、校园网 |
| 🎉 生活便利 | 外卖、交通、食堂、热水、消费方式、银行卡、快递、共享单车 |
| 🔓 自由程度 | 断电断网、寒暑假、门禁、查寝封寝晚归 |

每项 1-10 分，综合指数 = Σ(维度均分 × 权重)。评级：⭐⭐⭐⭐⭐ 顶级 / ⭐⭐⭐⭐ 良好 / ⭐⭐⭐ 一般 / ⭐⭐ 较差 / ⭐ 艰苦

---

## 数据来源

数据来源于 **[大学生活质量指北](https://colleges.chat/)**（[colleges.chat](https://colleges.chat/)），这是一个收集中国高校校园生活真实反馈的社区平台。

- 原始数据仓库：[CollegesChat/university-information](https://github.com/CollegesChat/university-information)（CNB 镜像：`Isaac80686/University_Information`）
- 问卷数据总量：33,579 份，覆盖 5,000+ 所院校

---

## ⚠️ 免责声明

1. **非官方数据**：所有评分和报告内容均基于在校生/校友的匿名投稿，**不代表学校官方立场**，不构成任何报考或择校建议。
2. **仅供参考**：数据可能存在偏差、过时或不完整，部分大学答卷数量较少（仅 1-3 份），统计意义有限。
3. **主观性**：校友反馈具有主观性，同一学校的不同校区、不同年级可能有截然不同的体验。
4. **不承担法律责任**：使用本 Skill 生成的任何报告、评分、网页，使用者应自行判断其准确性，开发者不承担因使用这些信息而产生的任何法律责任。
5. **数据版权**：原始数据版权归 [colleges.chat](https://colleges.chat/) 及原作者所有，本 Skill 仅提供数据查询和可视化工具。

---

## 文件结构

```
├── SKILL.md                 # 入口：触发条件、流程概览
├── data-fetching.md         # 数据获取：URL slug、锚点对照表
├── scoring-guide.md         # 评分标准：逐项打分、计算公式
├── report-template.md       # Markdown 报告模板
├── webpage-generator.md     # 网页生成规则：评级映射、占位符
└── README.md                # 本文件
```

---

## 网页模板稳定性保障

生成的网页 HTML 内置三层容错：

1. **Chart.js 双 CDN 兜底** — jsdelivr 失败自动切 unpkg
2. **雷达图安全检查** — `typeof Chart === 'undefined'` 降级为友好提示
3. **纯静态自包含** — 单 HTML 文件，无任何后端依赖

---

## 示例

### 中国政法大学 · 报告摘要

```
🏠 住宿条件:  6.4/10  (六人间上下铺 + 大澡堂)
🎓 学业氛围:  8.7/10  (无早晚自习、无晨跑、无打卡)
🎉 生活便利:  7.4/10  (外卖方便、昌平郊区进城慢)
🔓 自由程度:  7.8/10  (寒暑假长约50天、不查寝)
─────────────────────────────────
🏆 综合生存指数:  7.58 / 10  ⭐⭐⭐⭐ 良好
```

生成网页：`zhong-guo-zheng-fa-da-xue-report.html`

---

## License

Apache-2.0
