# 数据获取

数据来源于 CNB 仓库 [Isaac80686/University_Information](https://cnb.cool/Isaac80686/University_Information)，该仓库包含 **33,579 份**高校校园生活真实问卷数据，覆盖 **5,000+** 所院校。

## 1. 仓库数据概览

仓库提供以下数据文件（CSV + Parquet 双格式）：

| 文件 | 内容 | 数量 |
|------|------|------|
| `results_processed.csv/.parquet` | 脱敏后完整问卷数据 | 33,579 条 |
| `colleges.csv/.parquet` | 教育部标准高校名单 | 2,759 所 |
| `schools_unique.csv/.parquet` | 去重学校列表 | 5,122 所 |
| `school_containment.csv/.parquet` | 学校包含关系（分校/校区/简称） | 1,852 条 |

### 学校报告目录 `school_reports_v2/`

按 **省份** 组织，共 31 个省级行政区：
- 一级目录：`{4位地理编码}_{省份}`（如 `0001_上海`、`0004_北京`）
- 二级文件：`{3位拼音序号}_{学校名}.md`（如 `001_东华大学.md`）
- 总索引：`_index.csv` / `_index.parquet`

## 2. 数据获取步骤

### 步骤 1：下载全国高校目录

```bash
wget "https://cnb.cool/Isaac80686/University_Information/-/blob/main/%E5%85%A8%E5%9B%BD%E9%AB%98%E6%A0%A1%E5%90%8D%E5%8D%95.md"
```

下载后用 `read_file` 读取内容，文件中按省份列出了所有已收录的高校及其路径。

路径格式：`school_reports_v2/{省码}_{省份名}/{序号}_{学校名}.md`

### 步骤 2：查找目标大学

在目录中搜索目标大学名称，找到对应的省份目录和文件名（含序号）。

### 步骤 3：下载学校报告

```bash
wget "https://cnb.cool/Isaac80686/University_Information/-/blob/main/school_reports_v2/{省份编码}_{省份名}/{序号}_{学校名}.md"
```

下载后直接 `read_file` 读取即可，内容就是 Markdown 格式的问卷数据。

**完整示例** — 获取中国政法大学报告：

```bash
wget "https://cnb.cool/Isaac80686/University_Information/-/blob/main/school_reports_v2/0004_%E5%8C%97%E4%BA%AC/170_%E4%B8%AD%E5%9B%BD%E6%94%BF%E6%B3%95%E5%A4%A7%E5%AD%A6.md"
```

### 步骤 4：如果目录中找不到目标大学

用 wget 下载省份目录页面查找：

```bash
wget "https://cnb.cool/Isaac80686/University_Information/-/tree/main/school_reports_v2/{省份编码}_{省份名}" -O {省份}_dir.html
```

然后 `read_file` 读取，搜索目标大学文件名确定准确路径。

## 4. 学校报告内容结构

每个学校的 Markdown 报告包含以下内容：

- 学校名称和答卷数量统计
- 按问题分类的详细问答表格，涵盖 24+ 个维度：
  - 宿舍上床下桌、空调、独立卫浴
  - 早晚自习、晨跑、跑步打卡
  - 寒暑假时长、外卖、交通
  - 洗衣机、校园网、断电断网
  - 食堂价格、热水供应、电瓶车
  - 限电、通宵自习、电脑携带
  - 消费卡、银行卡、超市
  - 快递、共享单车、门禁、查寝
- 每份回答附有来源信息（匿名校友投稿）

## 5. 信息呈现

获取到信息后，以结构化方式呈现给用户：

1. **标注数据来源**：说明数据来自 University_Information 仓库的校友问卷
2. **标注答卷数量**：报告中会显示该学校的总答卷数，数值越大可信度越高
3. **如果多个回答不一致**，分别列出不同情况（如新校区 vs 老校区）
4. **如果仓库中没有该大学的信息**，明确告知用户该大学暂未被收录

## 6. 已知限制

- 部分大学的答卷数量可能较少（仅 1-3 份），统计意义有限
- 数据主要来自在校生/校友的匿名投稿，不一定完全准确或最新
- 评分仅基于公开的校友反馈，不包含官方数据
- 文件通过 CNB blob 路径直接用 wget 下载即可
- `全国高校名单.md` 较大（~400KB），建议先下载再搜索；如果部分省份被截断，直接用 wget 列出对应省份目录来查找目标大学
