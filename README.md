# MedGemma: Xử lý Dữ liệu và Tóm tắt Bệnh án Lâm sàng

Dự án này hướng dẫn cách xử lý tập dữ liệu y tế quy mô lớn (MIMIC-IV) và sử dụng mô hình ngôn ngữ lớn chuyên biệt cho y tế (**MedGemma**) để tóm tắt các ghi chú xuất viện (Discharge Summaries).

## 1. Cấu trúc và Yêu cầu Hệ thống

Dự án bao gồm 2 file notebook chính:

| Notebook | Chức năng | Cấu hình Colab |
| :--- | :--- | :--- |
| `data_processing.ipynb` | Lọc bệnh nhân từ file gốc, tạo tập mẫu 100 người. | **CPU** (Mặc định) |
| `med_gemma.ipynb` | Load mô hình MedGemma 4B và tóm tắt bệnh án. | **GPU T4** (Bắt buộc) |

## 2. Chuẩn bị Dữ liệu trên Google Drive

Để mã nguồn chạy chính xác, bạn cần tổ chức thư mục trên Google Drive cá nhân như sau:

1. Tạo thư mục: `MyDrive/thuc-tap/datasets/`
2. Tải file dữ liệu gốc `discharge.csv.gz` vào thư mục trên.
3. Notebook sẽ tự động đọc từ đường dẫn này và xuất file mẫu cùng vị trí.

## 3. Thiết lập Hugging Face Access Token

Vì `google/medgemma-1.5-4b-it` là mô hình có điều kiện truy cập, bạn cần thực hiện:

1. Truy cập [Hugging Face MedGemma](https://huggingface.co/google/medgemma-1.5-4b-it) và nhấn **Accept License**.
2. Tạo **Access Token** (loại Read) trong cài đặt tài khoản Hugging Face của bạn.
3. Tại giao diện Google Colab, chọn biểu tượng **Secrets** (hình chìa khóa) ở thanh bên trái:
   - Thêm Secret mới với Name: `HF_TOKEN`.
   - Dán Token của bạn vào phần Value.
   - Bật **Notebook access** cho token này.

## 4. Hướng dẫn Sử dụng

### Bước 1: Chạy `data_processing.ipynb`
- Notebook này giúp giảm nhẹ dữ liệu để có thể xử lý nhanh trên Colab.
- Hệ thống sẽ lọc các bệnh nhân có trên 5 bản ghi chú (`subject_id` xuất hiện > 5 lần) để đảm bảo có lịch sử bệnh án phong phú.
- Kết quả sẽ được lưu thành file `discharge_sample_100_patients_gt5.csv.gz` trên Drive.

### Bước 2: Chạy `med_gemma.ipynb`
- **Quan trọng:** Phải đổi Runtime sang **T4 GPU** trước khi chạy.
- Notebook sẽ cài đặt các thư viện cần thiết: `transformers`, `bitsandbytes`, `accelerate`.
- Mô hình được load bằng kỹ thuật lượng tử hóa **4-bit** (NF4) để tiết kiệm VRAM.
- Cuối cùng, notebook sẽ lấy văn bản lâm sàng thô (`raw_text`) và yêu cầu mô hình tạo tóm tắt theo cấu trúc: *Patient Presentation*, *Hospital Course*, và *Outcome*.

## 5. Lưu ý về Mã nguồn
- Các đoạn mã không chứa comment và icon để đảm bảo tính gọn gàng trong quá trình thực thi.
- Dữ liệu đầu ra được nén định dạng `.gz` để tối ưu dung lượng lưu trữ trên Google Drive.
