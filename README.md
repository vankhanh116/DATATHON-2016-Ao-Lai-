📊 DATATHON — Phân Tích Dữ Liệu Thương Mại Điện Tử Việt Nam
📝 Tổng quan dự án
Dự án thực hiện phân tích chuyên sâu dữ liệu nền tảng thương mại điện tử tại Việt Nam, bao gồm: Khám phá dữ liệu (EDA), Tiền xử lý (Preprocessing), Dự báo doanh thu (Sales Forecasting), và Trực quan hóa (Visualization).

📁 Cấu trúc thư mục
Bài nộp/
├── EDA & Data Preprocessing/
│   ├── EDA+ Data Preprocessing.ipynb      ← Notebook xử lý dữ liệu
│   └── EDA file/                           ← Báo cáo HTML tự động
├── Sales_Forecasting.ipynb                 ← Notebook dự báo doanh thu
└── Visualize/
    └── [DATATHON] Final.pbix               ← File thiết kế Power BI
🧪 Phần 1: EDA & Data Preprocessing
Quy trình xử lý chính:
Làm sạch dữ liệu: Xử lý giá trị thiếu (null) tại bảng promotions và order_items.

Chuẩn hóa địa lý: Ánh xạ Thành phố sang Miền (Bắc / Trung / Nam) để phân tích vùng miền.

Công cụ: Sử dụng ydata_profiling để xuất báo cáo thống kê tự động cho 14 bảng dữ liệu master và giao dịch.

📊 Phần 2: Visualization
Link Dashboard trực tuyến: Truy cập tại đây

Dashboard tập trung vào các chỉ số cốt lõi:

Kinh doanh: Doanh thu theo thời gian, lợi nhuận gộp và hiệu suất vùng miền.

Khách hàng: Hành vi mua sắm, xu hướng đơn hàng và đánh giá từ người dùng.

Vận hành: Hiệu quả khuyến mãi, tình trạng giao hàng và tỷ lệ hoàn trả.

🤖 Phần 3: Sales Forecasting
Dự báo doanh thu cho 548 ngày tiếp theo dựa trên kiến trúc Residual Learning (Prophet + Stacking Ensemble).

Feature Engineering (Đã tinh gọn):
Calendar: Năm, tháng, ngày, quý, thứ trong tuần và cuối tuần.

Cyclical encoding: Mã hóa Sin/Cos cho tháng và ngày để xử lý tính chu kỳ.

Promotions: Gắn cờ (flag) cho 6 chiến dịch khuyến mãi lớn hàng năm (Spring, Mid-Year, Fall, Year-End...).

Peak proximity: Độ gần với các tháng cao điểm doanh thu (tháng 4, 5, 6).

Mô hình hóa:
Prophet: Dự báo xu hướng và tính mùa vụ tổng thể.

XGBoost / LightGBM / CatBoost: Học các sai số (residuals) từ Prophet.

Optuna: Tối ưu hóa siêu tham số tự động.

⚙️ Yêu cầu môi trường
Bash
pip install pandas numpy matplotlib seaborn ydata-profiling
pip install xgboost lightgbm catboost prophet optuna shap
pip install scikit-learn
👥 Thông tin dự án
Cuộc thi: DATATHON 2026

Thí sinh: LE VAN KHANH

Ngày hoàn thành: 01/05/2026
