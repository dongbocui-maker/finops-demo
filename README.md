# Envision FinOps Dashboard

一个全栈 FinOps Demo Dashboard，多维度追踪云资源使用、识别优化机会并生成行动指南。所有数据均为演示用途。

**Live Demo**: https://dongbocui-maker.github.io/finops-demo/

## 功能板块

1. 执行摘要 / 12 月趋势 vs 预算
2. 云成本总览（按服务 / 团队 / 环境 / 趋势）
3. FinOps Ontology（D3 力导向知识图谱，87 节点 / 181 关系）
4. 计算 EC2/EKS · 数据库 RDS · 存储 S3/EBS · 网络 · 许可证
5. AI & GenAI 成本（Token / GPU / Inference Whale）
6. 分析报告（4 优先级 14 findings）
7. 行动指南（优先级矩阵 + 14 行动项 + JSON-LD 导出）

## 技术栈

纯静态：HTML + Vanilla JS + Tailwind CDN + Chart.js 4 + D3 v7 + Font Awesome 6。无构建步骤。

## 本地启动

```bash
python3 -m http.server 8765
# 浏览器打开 http://localhost:8765/
```

## 行业参考

FinOps Foundation FOCUS 1.2 / OpenCost / Inform-Optimize-Operate 框架；AWS re:Invent 2025；CloudZero 2026。
