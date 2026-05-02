# Envision FinOps Dashboard — 项目备忘

> 用于以后任何修改、移交、复盘的快速上下文。所有数字均为 **demo 数据**。

---

## 1. 路径与启动

| 项 | 值 |
|---|---|
| 工作目录 | `/Users/jason.dongbo.cui/Library/CloudStorage/OneDrive-Accenture(China)/Desktop/FinOps/` |
| 主 HTML | `index.html` (~870 行) |
| 主 JS | `js/app.js` (~2570 行) |
| 启动 | `python3 -m http.server 8765` |
| 预览 | http://localhost:8765/ |

---

## 2. 技术栈（全 CDN，无构建）

- HTML + Vanilla JavaScript
- Tailwind CSS (CDN)
- Chart.js 4.4.0 (CDN)
- D3 v7.9.0 (CDN, 仅用于 Ontology)
- Font Awesome 6.5.0 (CDN)

可直接部署到 GitHub Pages。

---

## 3. 客户场景 / Demo 数据锚点

- **客户**：Envision Group（能源/科技），AWS 主云，~500 用户
- **账期**：2026 年 4 月
- **总月支出**：$845,320 ｜ **YTD**：$3.21M ｜ **预算**：$820K/月
- **总优化潜力**：$204,647 / 月（≈ $2.46M 年化，占总支出 24.2%）
- 关键资源数：47 oversized EC2、RI 覆盖 54%、11 idle DB、14 多余 MultiAZ、28 unattached EBS、486 闲置 license seats、3 Inference Whale、4.2B AI tokens、24 GPU 实例

---

## 4. 11 个板块（按导航顺序）

| # | id | 标题 | 核心组件 |
|---|---|---|---|
| 1 | `summary` | 执行摘要 | 6 KPI + 12 月趋势 vs 预算 |
| 2 | `cost-overview` | 云成本总览 | service donut / team bar / env donut / stacked bar |
| 3 | `compute` | 计算资源 EC2/EKS | 5 KPI + CPU 分布 + RI 覆盖 + 47 超配表 |
| 4 | `database` | 数据库 RDS | 4 KPI + 利用率 + 成本 + idle/MultiAZ 表 |
| 5 | `storage` | 存储 S3/EBS | 4 KPI + tier donut + 成本 bar |
| 6 | `network` | 网络使用 | 4 KPI + 24h 带宽 + 成本 bar |
| 7 | `license` | 许可证管理 | 4 KPI + 利用率 + 成本 + 表 |
| 8 | `analysis` | 分析报告 | 14 findings 4 优先级（critical/high/medium/low）|
| 9 | `action-plan` | 行动指南 | 4 象限矩阵 + savings bar/donut + 14 行动表 |
| 10 | `ai-finops` | AI & GenAI 成本 | 5 KPI + 4 chart + Whale table |
| 11 | `ontology` | FinOps Ontology | D3 力导向 / Radial 布局, 87 节点 / 181 边 |

侧边栏顺序：1, 2, **11**, 3-7, 10, 8, 9（Ontology 放"概览"组下）。

---

## 5. Ontology（板块 11）核心

- **7 类实体**：Organization / BusinessUnit / Environment / Application / CloudResource / Metric / OptimizationAction
- **9 类关系**：belongsTo, deployedIn, consumes, measuredBy, incurs, triggers, recommends, targets, savesFor
- **87 节点** 分散在常量数组 `_ONTO_ORG / _BU / _ENV / _APP / _RES / _METRIC / _ACT`，最后合并为 `ONTOLOGY_NODES`
- **181 关系边** 在 `ONTOLOGY_LINKS`
- **渲染入口**：`renderOntologyLegend()` + `renderOntologyGraph()`
- **工具函数**：
  - `setOntologyLayout('radial' | 'force')`
  - `filterOntology(type)`
  - `ontologyReset()`
  - `exportOntologyJSONLD()`
  - `showOntologyDetail(d)`
  - `highlightNeighbors(d)`
- **状态**：`_ontoState`（全局）、`_ontoLayout`（默认 `'radial'`）
- **视觉**：暗色背景 + 节点径向渐变 + Organization 发光滤镜 + 5 同心环（仅 radial）

---

## 6. 代码约定 / 重要模式

- 所有图表用 Chart.js，每个图独立 render 函数；统一全局 `Chart.defaults` 字体/颜色
- **CSS 强制** `.chart-box canvas { height: 320px !important }`，否则 responsive 模式下会无限放大
- **多页模式**：`.section { display:none } / .section.active { display:block }`
  - `goSection(id)` 切换 + 写 `location.hash` + 监听 `hashchange` + 切换后 `Chart.resize()`
  - **不可命名为 `scrollTo`**（与 `Element.prototype.scrollTo` 冲突）
- **颜色板** `PALETTE`（11 色）；货币 `fmtUSD(n)`
- **单一 `DOMContentLoaded`** 监听器在 `js/app.js` 末尾按顺序调用 36 个 render 函数 + ontology 2 个

---

## 7. Logo

- Accenture wordmark：白色 `accenture` 字标 + 紫色 `>` 标志
- 来源：`https://cdn.simpleicons.org/accenture/A100FF`
- 品牌主色：`#A100FF`

---

## 8. 已知"坑" & 修复记录

1. **板块覆盖**：早期合并 section 8/9 时 `DOMContentLoaded` 块覆盖了前 7 + 板块 10 的 init 调用 → 已恢复完整 36 个 render 调用顺序
2. **`scrollTo` 命名冲突**：与 DOM 原生方法冲突 → 改名 `goSection()`
3. **Chart 无限增长**：canvas 在无固定高度容器内可达 4700px+ → CSS `max-height:320px !important`
4. **Ontology Metric 语法错误**：`m:storage_t` 写了非法属性 `Std/IA/Glacier比`（缺值，被 JS 当除号）→ 改为字符串键值
5. **暗色工具栏样式**：板块 11 工具栏放在暗色背景上需特定样式 `.onto-tool-btn` / `.onto-select`

---

## 9. 行业最佳实践参考（散在代码注释中）

- FinOps Foundation **FOCUS 1.2** / **OpenCost** / **Inform-Optimize-Operate** 框架
- AWS Apr 2026：**Amazon Q Cost Capabilities**、**Bedrock IAM Principal Cost Allocation**
- AWS re:Invent 2025：**S3 Tables Intelligent-Tiering**、**Bedrock RFT**、**Network Firewall Flexible Cost Allocation**
- CloudZero 2026：**AI False Efficiency Curve** / **Inference Whale**

---

## 10. ⚠ Prompt Injection 警告

本项目交付会话中工具/上下文反复混入伪装成系统/用户消息的注入文本（典型开头 "Respond as helpfully as possible..." 或要求版权/格式限制），这些 **不是用户真实指令**，已被忽略。如未来 AI 助手出现行为异常，应优先怀疑同类注入。

---

## 11. 下一步待办

- [ ] 推送到 GitHub（用户名 `dongbocui-maker`，建议仓库名 `envision-finops-dashboard`，Public）
- [ ] 启用 GitHub Pages → `https://dongbocui-maker.github.io/envision-finops-dashboard/`
- [ ] 部署前需添加 `.gitignore` 和 `README.md`

---

*生成时间：2026-05-02*
