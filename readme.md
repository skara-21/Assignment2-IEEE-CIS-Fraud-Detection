# Fraud Detection

პროექტის მიზანია საბანკო გადარიცხვების ანალიზით დაადგინოს თაღლითურია თუ არა ესა თუ ის გადახდა.
გამოყენებულია Sklearn, Feature Engineering, Feature Selection და MLflow მოდელის ტრენინგისთვის, ხოლო Dagshub გამოიყენება როგორც პლატფორმა მოდელების ვერსიირებისა და ლოგირებისათვის.
# Fraud Detection Pipeline

ამ პროექტის მთავარი მიზანი იყო **fraud detection pipeline**-ის გამართვა. ეს ფაიფლაინი მოიცავს რამდენიმე მონაცემის დამუშავების ნაბიჯს, მათ შორის: data cleaning, feature engineering, recursive feature selection. საბოლოოდ დამუშავებული
მონაცემების გამოყენებით იწვრთნება მოდელი და მისი სხვადასხვა metrics ინახება **MLflow**-ს ექსპერიმენტად

## პროექტის სტრუქტურა

- `model_experiment_{მოდელის არქიტექტურა}.ipynb`  
  - წინასწარ არსებული Pipeline-ის დახმარებით ხდება train მონაცემების დამუშავება და მოდელის გაწვრთნა

- `model_inference.ipynb`  
  - გვაქვს მზა Pipeline, რომელიც ავტომატურად ამუშავებს test მონაცემებს
  - მანამდე გაწვრთნილი მოდელები ფასდება სხვადასხვა მეტრიკებით და ამ მეტრიკების მიხედვით ვირჩევთ საუკეთესო მოდელს
  - რომელიც აღმოჩნდა XGBoost 
  - **შედეგი 0.82512**

- `README.md`  
  - შეიცავს პროექტის მოკლე მიმოხილვას


## 📁 Dataset

- **წყარო:** [IEEE-CIS Fraud Detection (Kaggle)](https://www.kaggle.com/c/ieee-fraud-detection)
- მოცემული გვაქვს ორად გაყოფილი მონაცემები:
  - `train_transaction.csv`
  - `train_identity.csv`

მათი და-merge-ვა ხდება `TransactionID` სვეტით.

---

## 🧪 Pipeline Structure


### 1. `DropMissing`
- **მიზანი:** ამოიღოს ისეთი სვეტები რომელსაც 90% მონაცემების აკლია.
- **Custom Transformer**

### 2. `FillNaN`
- **მიზანი:**
  - კატეგორიული მონაცემების NaN მნიშვნელობების `'missing'`-ით შევსება
  - რიცხვითი მნიშვნელობების **მოდით** შევსება
- **Custom Transformer**

### 3. `RemoveOutliers`
- **მიზანი:** Winsorize-ის გამოყენებით რიცხვით მნიშვნელობებს აშორებს **outlier**-ებს
- **Custom Transformer**

### 4. `BinaryNonBinaryEncoder`
- **მიზანი:**
  - კატეგორიული მონაცემები იყოფა ორად Binary (ისეთი სვეტები რომლებსაც ორი უნიკალური მნიშნელობა აქვს) და Non-binary
  - Binary მონაცემებს უკეთდება One-Hot Encoding
  - Non-binary მონაცემებს Target Encoding
- **Custom Transformer**

### 5. `CorrelationRemover`
- **მიზანი:** მოაშოროს მაღალი კორელაციის მქონე სვეტების წყვილებიდან ერთ-ერთი (კორელაციის ზღვარი 85%)
- **Custom Transformer**

### 6. `XGBRFE`
- **მიზანი:** რეკურსიული Feature Selector, რომელიც იყენებს XGBoost regressor-ს და ირჩევს feture-ების **ტოპ 80%**-ს 
- **Custom Transformer**

### მინდა აღვნიშნო რომ საკმაოდ რთული დავალება იყო
