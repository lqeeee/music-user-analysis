# music-user-analysis
音乐平台用户行为洞察与流失预警分析

> 基于近 1900 万条真实用户播放日志，完成从留存诊断、用户分层到流失预测与召回策略的全链路数据分析项目。

## 📊 项目概览

本项目使用 Last.fm 公开数据集，对近 **1000 名用户** 长达 4 年的听歌行为进行分析。核心目标是通过数据驱动的方式：

1. **量化平台健康度**：通过同期群留存分析，定位用户流失的关键时期。
2. **识别高风险群体**：构建 RFM 模型与 K-Means 聚类，实现用户价值分层。
3. **预测流失并干预**：训练逻辑回归模型预测用户流失概率，并基于特征重要性设计三套差异化召回策略。

最终输出包含 **分析报告、Tableau 可视化仪表盘** 以及完整可复现的 Python 代码。

## 🗂️ 仓库结构
```
music-user-analysis/
├── README.md # 本文件
├── Music_User_Behavior_Churn_Analysis.twb # 仪表盘
├── data_cleaning.ipynb # 数据清洗与探索
├── retention_rfm.ipynb # 用户留存分析与价值分层
├── churn_model.ipynb # 流失预警建模与召回策略
```

## 📦 数据来源

**Last.fm 1K User Dataset**
- 包含 992 名用户 2005 年 2 月至 2009 年 5 月的全部播放记录，共约 1900 万条。
- 格式：`user_id \t timestamp \t artist_id \t artist_name \t track_id \t track_name`
- 来源：https://www.kaggle.com/search?q=Last.fm+1K

> 注：原始数据未上传至本仓库。请从上方链接下载 `lastfm-dataset-1K` 文件夹，并置于项目根目录下。

## 🔍 分析流程与方法

### 1. 数据清洗与预处理
- 处理异常行与时间戳（ISO 8601 格式）
- 截断至官方截止日期 2009-05-05
- 提取 `hour`、`weekday` 等时间特征
- 由于与 Spotify 音频特征表的时间错位（相差约 10 年），决策放弃音频特征，聚焦用户行为。

### 2. 用户留存同期群分析
- 按月聚合首次活跃用户，计算注册后第 0 天至第 30 天的留存率。
- 定位低次日留存同期群，对比其与正常群体的行为特征（平均回访间隔、活跃天数等）。

### 3. 用户价值分层 (RFM + K-Means)
- 以数据截止日为参照，计算每个用户的 **R**（最近播放距今）、**F**（活跃天数）、**M**（总播放次数）。
- 使用 K-Means（k=4）将用户聚为四类：**核心忠诚、普通用户、沉睡用户、彻底流失**。

### 4. 流失预警模型 (Logistic Regression)
- **流失定义**：截止日前 30 天无任何播放记录。
- **特征工程**：基于截止日前 60 天窗口，提取活跃天数、播放总量、播放间隔、前后期播放衰减率等特征。
- **模型**：使用逻辑回归（`class_weight='balanced'`）预测流失概率，测试集召回率 **67%**。
- **关键信号**：**trend_ratio**（后 30 天播放量相对前 30 天的变化）是流失最强烈的负向指标，系数达 **-5.15**。

### 5. 策略设计
基于特征重要性，输出三类可落地的差异化召回策略：
- **趋势崩塌型**：播放量腰斩的用户 → 推送历史偏好歌手新作。
- **逐渐疏远型**：活跃间隔拉长 → 在高频活跃时段发送 APP 提醒。
- **暴饮暴食型**：单日高强度使用后衰减 → 引导“少食多餐”的每日推荐习惯。

## 📈 可视化仪表盘

核心洞察已集成至 Tableau Public 交互看板，包含留存热力图、用户分层饼图、24小时活跃热力图、流失特征重要性图。

👉 [点击查看在线仪表盘]（https://public.tableau.com/app/profile/.20935443/viz/Music_User_Behavior_Churn_Analysis/1?publish=yes）

## 🛠️ 技术栈

`Python` `Pandas` `Scikit-learn` `Matplotlib` `Seaborn` `Tableau` `Jupyter`

- 数据处理与分析：Pandas, NumPy
- 机器学习：Scikit-learn（Logistic Regression, K-Means, StandardScaler）
- 可视化：Matplotlib, Seaborn, Tableau Public

## 🚀 如何复现

1. 克隆仓库并下载数据到本地（见 `data/` 说明）。
2. 安装依赖：
   ```bash
   pip install pandas numpy scikit-learn matplotlib seaborn
按顺序执行三个 Notebook。
