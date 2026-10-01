# Irrigation Need Prediction

這是我參加 Kaggle 灌溉需求預測競賽時撰寫的機器學習實驗程式。目標是根據土壤、天氣、作物種類、田地條件與灌溉相關資訊，預測作物目前的灌溉需求。

預測目標欄位為 `Irrigation_Need`，共分成三個類別：

- `Low`
- `Medium`
- `High`

## 專案內容

主要程式集中在 `Irrigation_Need.ipynb`。Notebook 會讀取競賽資料，進行特徵工程、類別特徵編碼、模型訓練、交叉驗證、模型集成，最後產生可上傳 Kaggle 的 `submission.csv`。


## 檔案說明

```text
.
|-- README.md              # 專案說明
`-- Irrigation_Need.ipynb  # 主要程式碼
```


## 使用資料

原始資料包含訓練集、測試集與提交範例：

```text
train.csv
test.csv
sample_submission.csv
irrigation_prediction.csv
```


主要特徵包含：

- 土壤資訊：`Soil_Type`、`Soil_pH`、`Soil_Moisture`、`Organic_Carbon`、`Electrical_Conductivity`
- 天氣資訊：`Temperature_C`、`Humidity`、`Rainfall_mm`、`Sunlight_Hours`、`Wind_Speed_kmh`
- 作物資訊：`Crop_Type`、`Crop_Growth_Stage`、`Season`
- 灌溉資訊：`Irrigation_Type`、`Water_Source`、`Previous_Irrigation_mm`
- 田地資訊：`Field_Area_hectare`、`Mulching_Used`、`Region`

## 方法

Notebook 中的流程大致如下：

1. 讀取訓練資料、測試資料與提交範例。
2. 將 `Irrigation_Need` 轉換成模型可訓練的類別標籤。
3. 加入額外特徵，例如：
   - 土壤乾燥指標
   - 土壤濕度與溫度的平方項
   - 低濕度、高溫、低雨量、高風速等條件特徵
   - 作物生長階段與覆蓋物相關特徵
   - 手動設計的交互特徵分數
4. 對類別欄位進行編碼：
   - XGBoost 原生類別特徵支援
   - Target Encoding
   - Frequency Encoding
5. 使用 5-fold Stratified Cross Validation 訓練模型。
6. 比較不同模型與集成方式的表現。
7. 輸出 Kaggle submission 檔案與訓練紀錄。

## 使用模型

Notebook 中嘗試了以下模型：

- XGBoost
- CatBoost
- LightGBM
- Logistic Regression stacking

儲存的最佳實驗紀錄中，使用 Logistic Regression stacking 作為最終集成方式，OOF balanced accuracy 約為 `0.97456`。

## 環境安裝

建立虛擬環境：

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

安裝套件：

```powershell
python -m pip install -r requirements.txt
```

如果 PowerShell 無法啟用虛擬環境，可以先執行：

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

## 執行方式

將 Kaggle 資料放在專案根目錄後，開啟並依序執行：

```text
Irrigation_Need.ipynb
```

Notebook 預期根目錄中存在以下檔案：

```text
train.csv
test.csv
sample_submission.csv
irrigation_prediction.csv
```

程式中的 XGBoost、CatBoost 和 LightGBM 參數有使用 GPU 設定。如果在沒有 GPU 的環境執行，需要將相關參數改成 CPU 版本。

## 輸出結果

每次執行完成後，會在 `results/` 底下產生新的資料夾，內容包含：

```text
submission.csv
model_and_metrics.json
train_columns.txt
```


