---
title: 城市计算研究综述
type: topic
date: 2026-04-05
tags: [研究, 城市计算, 时空数据, 交通, 环境监测]
---

# 城市计算研究综述

## 概述

基于郑宇老师（京东副总裁、IEEE/ACM Fellow）29 篇精选论文的系统梳理，涵盖智能交通、环境监测、轨迹挖掘、城市规划、时空预测、跨域数据融合六大领域，时间跨度 2008-2025。

## 关键论文

| # | 论文 | 会议/期刊 | 年份 | 核心贡献 |
|---|------|----------|------|---------|
| 01 | Urban Computing 综述 | ACM TIST | 2014 | 城市计算概念、方法论、应用四层框架 |
| 02 | Trajectory Data Mining 综述 | ACM TIST | 2015 | 轨迹数据处理与分析系统分类 |
| 03 | Cross-Domain Data Fusion | IEEE TBD | 2015 | 跨域数据融合三大范式 |
| 04 | U-Air | KDD | 2013 | 基于协同训练的半监督空气质量推断 |
| 05 | Functional Zones | KDD | 2012 | POI+人流发现城市功能区（LDA 变体） |
| 07 | T-Drive | SIGSPATIAL | 2010 | 基于出租车轨迹的智能导航 |
| 08 | Travel Time Estimation | KDD | 2014 | 稀疏轨迹实时路径时间估计 |
| 09 | T-Share | ICDE | 2013 | 大规模实时拼车（Best Paper Runner-up）|
| 11 | Air Quality Forecasting | KDD | 2015 | 细粒度空气质量预测 48h |
| 12 | Bike Lanes Planning | KDD | 2017 | 共享单车轨迹自行车道规划 |
| 13 | Urban Noise | UbiComp | 2014 | 311 投诉+社媒推断噪音分布 |
| 14 | Passenger Finding | UbiComp | 2011 | 司机载客位置概率推荐 |
| 15 | Driving Knowledge | KDD | 2011 | 个性化驾驶行为建模 |
| 16 | Taxi Ridesharing | TKDE | 2015 | 城市级拼车，服务 40% 额外用户 |
| 17 | Air Quality Station | KDD | 2015 | 半监督学习优化监测站选址 |
| 18 | pg-Causality | IEEE TBD | 2017 | 空气污染时空因果路径识别 |
| 20 | Urban Water Quality | IJCAI | 2016 | 多任务多视图水质预测 |
| 22 | ST-MVL | IJCAI | 2016 | 时空数据缺失值填充 |
| 24 | Map Matching (Low Sampling) | SIGSPATIAL | 2009 | ST-Matching 低采样率地图匹配 |
| 26 | Reducing Uncertainty | ICDE | 2012 | IVMM 轨迹不确定性降低 |
| 28 | ST-ResNet | AAAI | 2017 | 深度残差网络城市人流预测 |
| 29 | Cross-Domain Knowledge Fusion | ACM TIST | 2025 | 跨域知识融合四层框架（最新） |
| 30 | 城市治理一网统管 | 武大学报 | 2022 | 数据打通+协同处置城市治理 |

## 核心方法论

### 关键数据类型
GPS 轨迹、POI、气象数据、社交媒体、311 投诉、道路网络

### 核心技术栈
- **跨域融合**：特征级/模型级/语义级融合（Co-Training、多视图学习）
- **时空建模**：张量分解、CNN+LSTM、ST-ResNet
- **不确定性处理**：概率推理、历史数据利用（IVMM、RICK）
- **优化方法**：动态规划、整数规划、贪心启发式

### 核心启示
1. 跨域融合是解决城市问题的关键（单数据源不够）
2. 半监督是常态（城市数据稀疏标注 >99%）
3. 时空分离建模有效
4. 出租车是"移动传感器"

## 与其他主题的关系

- [[topics/virtual-user-research.md|虚拟用户研究]]：虚拟用户可作为城市感知增强器、行为模拟器、决策辅助
- [[topics/da-casebase.md|DA CaseBase 研究]]：城市时空数据分析方法可迁移到企业数据场景
