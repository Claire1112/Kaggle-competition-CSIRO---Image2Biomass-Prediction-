# Kaggle-competition-CSIRO---Image2Biomass-Prediction-

本專案基於 Kaggle 競賽 **CSIRO – Image2Biomass Prediction Challenge**，建立一套從牧草影像預測生物量（biomass）的深度學習模型，並透過系統性實驗分析不同模型架構對效能的影響。

---

## 專案背景與動機

牧草生物量是智慧農業中重要指標，但傳統量測方式需人工收割，成本高且具破壞性。因此，本研究嘗試透過影像建立**非破壞性預測模型**。

然而，此任務面臨以下挑戰：

- 背景複雜（裸土、陰影、枯草）
- 草地特徵細微且分布不均
- 模型容易被非植被區域干擾  

核心問題：**如何讓模型專注在真正影響生物量的區域？**

## 研究目標

本專案目標為建立一個：

- 高準確度（低 RMSE）
- 具可解釋性（attention visualization）
- 抗背景雜訊能力強

的生物量預測模型，並分析不同模組的影響（Soft Mask / MTL）。

## 方法概述

本研究採用「**Baseline → 模組設計 → 消融實驗**」的完整流程：

### 1️ 資料前處理與增強
- 使用影像增強（旋轉、翻轉、亮度調整）
- 利用 **Excess Green (ExG)** 建立 pseudo mask（弱監督）

### 2️ 模型架構設計

#### 🔹 Backbone：EfficientNet-B0
- 採用 MBConv（inverted bottleneck）
- 具備高效率與強語義特徵表現  

#### 🔹 核心模組

本研究設計四種模型進行比較：

1. **Baseline**
   - EfficientNet-B0（純影像回歸）

2. **Soft Mask Model（SMA）**
   - 加入 Attention 機制
   - 讓模型聚焦於植被區域

3. **MTL Model**
   - 加入 segmentation 任務（覆蓋率）
   - 強化結構理解

4. **PastureMask-MTLNet**
   - 結合 Soft Mask + MTL  

## 實驗結果（EfficientNet-B0）

### 🔥 消融實驗結果

| Model | RMSE ↓ | R² ↑ | 結論 |
|------|--------|------|------|
| Baseline | 10.70 | 0.76 | 基準模型 |
| + Soft Mask | **10.13** | **0.79** | 最佳 |
| + MTL | 11.68 | 0.72 | 負遷移 |
| Full Model | 10.77 | 0.76 | 無明顯提升 |

=> Soft Mask 提升約 **+3.26%**，為最佳方法  


## 關鍵發現

### 1️ Soft Mask 是最關鍵技術
- 能有效過濾背景（土壤、陰影）
- 讓模型「學會看哪裡」

### 2️ MTL 出現負遷移
- segmentation 任務與 regression 衝突
- 模型被迫保留過多無關細節  

### 3️ EfficientNet-B0 優於 ResNet
- 特徵維度更高（1280 vs 512）
- 更能捕捉：
  - 紋理差異
  - 草地結構
  - 光影變化  

### 4️ Attention 比模型複雜度更重要
- Soft Mask 僅增加 **+2.36% 參數**
- 卻帶來最大效能提升  


## 模型可解釋性

透過 Grad-CAM 與 Attention Heatmap：

- **Baseline**
  - 注意力分散，易受背景干擾

- **Soft Mask**
  - 高度聚焦於植被區域
  - 預測更穩定

- **MTL**
  - 注意力失焦，效果不佳

=> 顯示模型已成功學會：

> 「哪些區域真正影響生物量」

---

##  延伸實驗

我們進一步測試： **影像 + Metadata（NDVI / Height / Month）**

結果：

- ResNet：大幅提升（突破瓶頸）
- EfficientNet：效果有限（已接近極限）

=> 結論：

> 強模型可從影像中自動學習環境資訊  


## 🏁 最終結論

- **最佳模型：EfficientNet-B0 + Soft Mask**
- 最低 RMSE、最高 R²
- 幾乎不增加參數量
- 可解釋性最佳

=> 關鍵策略：

> **強語義 backbone（EfficientNet） + 輕量注意力（Soft Mask）**

## 專案價值

- 解決農業影像背景干擾問題
- 提供可解釋 AI（可視化 attention）
- 可應用於：
  - 智慧放牧
  - 精準農業
  - 環境監測

## 作者

- 李怡君  
- 查欣瑜  

## GitHub

https://github.com/liyijun0210/mis583-group07
