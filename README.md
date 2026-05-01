# 📊 DATATHON — Bài Nộp Phân Tích Dữ Liệu Thương Mại Điện Tử Việt Nam

## Tổng quan dự án

Dự án phân tích toàn diện dữ liệu của một nền tảng thương mại điện tử tại Việt Nam, bao gồm 4 giai đoạn chính: **Khám phá dữ liệu (EDA)**, **Tiền xử lý dữ liệu (Data Preprocessing)**, **Dự báo doanh thu (Sales Forecasting)**, và **Trực quan hóa (Visualization)** bằng Power BI.

---
> **📌 Lưu ý:** File dashboard Power BI (`[DATATHON] Final.pbix`) có dung lượng lớn nên không thể tải lên GitHub. File được đính kèm trực tiếp trong file Dashboard.docx
---

## 🧪 Phần 1: EDA & Data Preprocessing

**File:** `EDA+ Data Preprocessing.ipynb`

### Dữ liệu đầu vào

Dự án làm việc với **3 nhóm bảng dữ liệu** chính:

| Nhóm | Bảng |
|---|---|
| **Master Data** (Danh mục) | `customers`, `products`, `geography`, `promotions` |
| **Giao dịch** (Transactions) | `orders`, `order_items`, `payments`, `inventory`, `sales` |
| **Vận hành & Phản hồi** | `shipments`, `returns`, `reviews`, `web_traffic`, `users` |

### Công cụ sử dụng

- `pandas` — đọc và xử lý dữ liệu
- `ydata_profiling` — tạo báo cáo EDA tự động
- `matplotlib`, `seaborn` — trực quan hóa

### Quy trình EDA

Mỗi bảng được phân tích tự động bằng `ProfileReport` từ thư viện `ydata_profiling`, xuất ra file `.html` tương tác với đầy đủ:
- Thống kê mô tả (min, max, mean, median, std)
- Phân phối giá trị
- Phát hiện giá trị thiếu và ngoại lệ
- Ma trận tương quan

### Quy trình Tiền xử lý

| Bước | Mô tả |
|---|---|
| Xử lý giá trị null — `promotions` | Điền `'All'` vào cột `applicable_category` khi không có danh mục áp dụng |
| Xử lý giá trị null — `order_items` | Điền `'None'` vào cột `promo_id` và `promo_id_2` khi không có khuyến mãi |
| Chuẩn hóa vùng địa lý | Xây dựng bảng ánh xạ Thành phố → Miền (Bắc / Trung / Nam) cho bảng `geography`, xuất ra `geography_updated.csv` |
| Xuất dữ liệu sạch | `promotions_cleaned.csv`, `order_items_cleaned.csv`, `geography_updated.csv` |

#### Bảng ánh xạ vùng địa lý

- **Miền Bắc:** Hà Nội, Hải Phòng, Hạ Long, Bắc Ninh, Nam Định, Ninh Bình, Lào Cai, Thái Nguyên, Bắc Giang, …
- **Miền Trung & Tây Nguyên:** Đà Nẵng, Huế, Đà Lạt, Nha Trang, Quy Nhơn, Buôn Ma Thuột, Hội An, …
- **Miền Nam:** TP. Hồ Chí Minh, Biên Hòa, Vũng Tàu, Cần Thơ, Mỹ Tho, Cà Mau, …

---

## 📊 Phần 2: Visualization

**File:** `[DATATHON] Final.pbix` *(đính kèm trong tài liệu nộp bài — không upload GitHub do dung lượng lớn)*

Dashboard Power BI được xây dựng từ dữ liệu đã qua xử lý, tổng hợp insights về:
- Hiệu suất kinh doanh theo thời gian và vùng miền
- Hành vi khách hàng và xu hướng đơn hàng
- Hiệu quả khuyến mãi và vận chuyển
- Tỷ lệ đánh giá, hoàn trả và lưu lượng web

---

## 🤖 Phần 3: Sales Forecasting

**File:** `Sales_Forecasting.ipynb`

Notebook dự báo doanh thu tương lai (548 ngày) dựa trên dữ liệu lịch sử từ `sales.csv`, sử dụng kiến trúc **Residual Learning + Stacking Ensemble**.

### Dữ liệu đầu vào

| File | Mô tả |
|---|---|
| `sales.csv` | Doanh thu lịch sử từ 2012-07-04 đến 2022|
| `sample_submission.csv` | Khung dự báo 548 ngày tương lai |

### Thư viện sử dụng

- `xgboost`, `lightgbm`, `catboost` — mô hình gradient boosting
- `prophet` — mô hình chuỗi thời gian của Meta
- `optuna` — tối ưu siêu tham số tự động (Bayesian)
- `shap` — giải thích tầm quan trọng của đặc trưng
- `sklearn` — đánh giá mô hình, cross-validation

### Quy trình mô hình hóa

#### Bước 1 — Feature Engineering

| Nhóm feature | Mô tả |
|---|---|
| **Calendar** | Year, Month, Day, Quarter, DayOfWeek, WeekOfYear, IsWeekend, IsMonday, IsFriday |
| **Cyclical encoding** | Sin/cos của Month, DayOfWeek, DayOfYear (tránh lỗi thứ tự tuyến tính) |
| **Peak proximity** | Độ gần với các tháng doanh thu cao điểm (4, 5, 6) |

#### Bước 2 — Walk-Forward Cross Validation

Dữ liệu được chia theo 5 fold thời gian (không dùng k-fold ngẫu nhiên) để tránh data leakage:

```
Fold 1: Train đến 2018 → Validate 2019
Fold 2: Train đến 2019 → Validate 2020
Fold 3: Train đến 2020 → Validate 2021
Fold 4: Train đến 2021 → Validate 2022
```

#### Bước 3 — Kiến trúc Residual Learning + Stacking

```
[Prophet]  →  Dự báo xu hướng + mùa vụ (log scale)
     ↓
[Residual] =  Revenue_log - Prophet_pred
     ↓
[XGBoost / LightGBM / CatBoost]  →  Học phần dư (residual)
     ↓
[Meta-model: Huber Regressor]  →  Stacking OOF predictions
     ↓
[Final Forecast]  →  submission.csv
```

#### Bước 4 — Tối ưu siêu tham số
Sử dụng **Optuna** để tự động tìm bộ siêu tham số tối ưu cho XGBoost, LightGBM, CatBoost (Bayesian optimization với pruning).

#### Bước 5 — Đánh giá & Giải thích

Các metric đánh giá: **MAE**, **RMSE**, **R²**

SHAP values được tính cho cả 3 mô hình (LightGBM, XGBoost, CatBoost) để giải thích tầm quan trọng của từng feature trong dự báo.

### Output

| File | Mô tả |
|---|---|
| `submission.csv` | Dự báo Revenue cho 548 ngày tương lai |

---

## ⚙️ Yêu cầu môi trường

```bash
pip install pandas numpy matplotlib seaborn ydata-profiling
pip install xgboost lightgbm catboost prophet optuna shap
```

Để mở file dashboard: **Microsoft Power BI Desktop**

---

## 👥 Thông tin

| Mục | Chi tiết |
|---|---|
| Cuộc thi | DATATHON 2026 — Round 1 |
| Lĩnh vực | Thương mại điện tử Việt Nam |
| Ngày nộp | 01/05/2026 |
| Ngôn ngữ | Python 3, DAX (Power BI) |
