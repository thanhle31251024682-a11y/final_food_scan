# 🍱 AI Canteen — Nhận diện Khay Cơm / Canteen Tray Food Recognition

> **VI** — Hệ thống AI nhận diện từng ngăn trên khay cơm căng-tin và tự động tính tiền.  
> **EN** — An AI system that identifies each compartment of a canteen food tray and automatically calculates the total price.

---

## 📁 Cấu trúc Project / Project Structure

```
ai-canteen/
├── modeltrain_prj.py        # Huấn luyện model chính (MobileNetV2, 2 phase)
├── tunning.py               # Fine-tune model trên dữ liệu mới
├── copy_of_ai_canteen_colab.py  # Web app (Flask + ngrok) chạy trên Colab
├── requirements.txt
└── README.md
```

---

## 🧠 Luồng hoạt động / How It Works

```
Ảnh khay cơm
     │
     ▼
Cắt từng ngăn (JS phía client)
     │
     ▼
Gửi crop (base64) lên Flask API
     │
     ▼
Model MobileNetV2 nhận diện từng ngăn
     │
     ▼
Trả kết quả: tên món + giá → tổng tiền
```

---

## 🍽️ Danh sách món & giá / Menu & Prices

| # | Món ăn / Dish | Giá / Price |
|---|--------------|-------------|
| 1 | Cơm trắng / Steamed rice | 10,000 đ |
| 2 | Đậu hũ sốt cà / Tofu in tomato sauce | 25,000 đ |
| 3 | Cá hú kho / Braised fish | 30,000 đ |
| 4 | Thịt kho trứng / Braised pork & egg | 30,000 đ |
| 5 | Thịt kho / Braised pork | 25,000 đ |
| 6 | Canh chua / Sour soup | 10,000 đ |
| 7 | Sườn nướng / Grilled ribs | 30,000 đ |
| 8 | Canh rau / Vegetable soup | 7,000 đ |
| 9 | Rau xào / Stir-fried vegetables | 10,000 đ |
| 10 | Trứng chiên / Fried egg | 25,000 đ |

---

## 🚀 Hướng dẫn sử dụng / Usage Guide

Toàn bộ project chạy trên **Google Colab** + **Google Drive**. Không cần cài đặt local.  
*The entire project runs on **Google Colab** + **Google Drive**. No local installation needed.*

---

### Bước 1 — Chuẩn bị dữ liệu / Step 1 — Prepare Data

- Tạo thư mục `AI CUỐI KỲ` trong Google Drive.
- Bên trong tạo **10 thư mục con**, mỗi thư mục là 1 món (đặt tên theo format `1. Cơm trắng`, `2. Đậu hũ sốt cà`, …).
- Mỗi thư mục nên có **tối thiểu 50 ảnh** để đạt độ chính xác tốt.

> *Create a folder `AI CUỐI KỲ` in Google Drive with 10 subfolders (one per dish, named `1. Cơm trắng`, etc.), each containing at least 50 images.*

---

### Bước 2 — Train Model / Step 2 — Train the Model

Mở file `modeltrain_prj.py` trên Colab (File → Upload notebook hoặc mở từ Drive).

*Open `modeltrain_prj.py` in Colab.*

**Chạy lần lượt các cell:**

| Cell | Mô tả | Description |
|------|-------|-------------|
| 1 | Cấu hình đường dẫn & hyperparameters | Config paths & hyperparameters |
| 2 | Kiểm tra dataset | Verify dataset |
| 3 | Tự động chia train/val (80/20) | Auto split train/val |
| 4 | Tạo data generators + class weights | Build generators & class weights |
| 5 | Build model MobileNetV2 | Build MobileNetV2 model |
| 6 | **Phase 1**: Train classifier head (base frozen, LR=1e-3, 25 epochs) | Train head only |
| 7 | **Phase 2**: Fine-tune top blocks (LR=1e-5, 40 epochs) | Fine-tune top layers |
| 8 | Đánh giá + lưu model cuối | Evaluate & save final model |
| 9 | *(Tuỳ chọn)* Test 1 ảnh bất kỳ | *(Optional)* Test single image |

**Output lưu tại** `Google Drive/canteen_output/`:
- `canteen_model_final.h5` — model cuối cùng
- `class_indices.json` — mapping class
- `training_history.png`, `confusion_matrix.png`, `per_class_accuracy.png`

---

### Bước 3 — Fine-tune thêm (Tuỳ chọn) / Step 3 — Additional Fine-tuning (Optional)

Dùng `tunning.py` khi có **dữ liệu mới** và muốn cải thiện model đã train mà không train lại từ đầu.

*Use `tunning.py` when you have new data and want to improve the existing model without retraining from scratch.*

1. Đặt ảnh mới vào thư mục `canteen_new_data` trên Drive (cùng cấu trúc 10 thư mục con).
2. Mở `tunning.py` trên Colab và chạy toàn bộ.
3. Model tốt nhất sẽ lưu tại `best_mmodel(2).h5`.

> *Put new images in `canteen_new_data/` on Drive (same subfolder structure), then run `tunning.py`. Best model saves as `best_mmodel(2).h5`.*

---

### Bước 4 — Chạy Web App / Step 4 — Run the Web App

Mở `copy_of_ai_canteen_colab.py` trên Colab và chạy từng cell:

*Open `copy_of_ai_canteen_colab.py` in Colab and run each cell:*

| Cell | Việc cần làm | Action |
|------|-------------|--------|
| 1 | Cài thư viện | Install libraries |
| 2 | Mount Drive | Mount Drive |
| 3 | Kiểm tra đường dẫn model | Verify model paths |
| 4+5 | Load model + khởi tạo các hàm | Load model & functions |
| 6 | Upload file `index_new.html` (giao diện web) | Upload your HTML frontend |
| 7 | Cấu hình Flask server | Configure Flask server |
| 8 | Khởi động ngrok + Flask | Start ngrok & Flask |

Sau khi chạy cell 8, terminal sẽ in ra URL dạng:
```
🌐 APP ĐANG CHẠY TẠI:
   https://xxxx-xx-xx-xxx.ngrok-free.app
```
Mở URL đó trên trình duyệt để dùng app.

> *After running cell 8, a public ngrok URL will be printed. Open it in any browser to use the app.*

⚠️ **Lưu ý / Note:** Thay `NGROK_TOKEN` trong cell 8 bằng token của bạn tại [dashboard.ngrok.com](https://dashboard.ngrok.com).  
*Replace `NGROK_TOKEN` in cell 8 with your own token from [dashboard.ngrok.com](https://dashboard.ngrok.com).*

---

## ⚙️ Yêu cầu môi trường / Environment Requirements

Xem file `requirements.txt`. Khi chạy trên Colab, cell 1 của `copy_of_ai_canteen_colab.py` sẽ tự cài.

*See `requirements.txt`. When running on Colab, cell 1 of the web app script installs everything automatically.*

---

## 📌 Lưu ý quan trọng / Important Notes

- **GPU**: Bật GPU trên Colab (`Runtime → Change runtime type → T4 GPU`) để train nhanh hơn.  
  *Enable GPU on Colab for faster training.*
- **Class order**: Thứ tự class trong `class_indices.json` phải khớp giữa lúc train và lúc chạy app. Không đổi tên thư mục sau khi train.  
  *Class order in `class_indices.json` must match between training and inference. Do not rename folders after training.*
- **Model path**: `copy_of_ai_canteen_colab.py` mặc định load `best_mmodel(2).h5` (output của `tunning.py`). Nếu bỏ qua bước fine-tune, sửa đường dẫn thành `canteen_model_final.h5`.  
  *The web app loads `best_mmodel(2).h5` by default. If you skip fine-tuning, change the path to `canteen_model_final.h5`.*

---

## 🛠️ Tech Stack

- **Model**: TensorFlow / Keras — MobileNetV2 (transfer learning)
- **Backend**: Flask + flask-cors
- **Tunnel**: pyngrok (ngrok)
- **Platform**: Google Colab + Google Drive
- **Frontend**: HTML/JS (crop & gửi ảnh từ trình duyệt)
