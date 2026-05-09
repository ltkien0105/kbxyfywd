# Chuỗi Tự động hóa Nhiệm vụ: Danh mục Toàn tập

## 1. Lời giới thiệu

Tài liệu này chứa danh sách phân loại toàn bộ chuỗi nhiệm vụ được trích xuất từ `data/taskinfo.xml` và biểu đồ AS3 (Parser).
Mục đích của nó là cung cấp một bản mục lục tổng thể cho mọi mạch nhiệm vụ có thể tự động hóa, cho phép người phân tích chọn một chuỗi, cấp gốc (root `taskID`), và thiết lập định mức nền tảng cho kịch bản chạy tự động (bot script).

## 2. Tuyến chính / Nhiệm vụ Cốt truyện (Mainline)

Đây là các nhiệm vụ đại diện cho diễn tiến cốt truyện chính. Độ phức tạp thường rất cao, liên quan đến lượng lớn các node chuyển cảnh, đối thoại NPC, và chiến đấu.

- **Hoàng Thành Nguy Cơ (Root ID: 2001000)**
  - Đã có tài liệu phân tích chi tiết tại: `docs/task-automation-mainline-2001000-huangcheng-weiji.md`
  - Phạm vi Subtask dự kiến: 2001001 ~ 200100X
- **Ma Tinh Chiến (Root ID: Đang cập nhật)**
  - Trạng thái: Chờ phân tích.
- **Kabu Đại Mạo Hiểm (Root ID: Đang cập nhật)**
  - Trạng thái: Chờ phân tích.

## 3. Chùm Nhiệm vụ Hoạt động & Phụ tuyến (Đợt 01)

Đây là các chuỗi hoạt động tồn tại độc lập hoặc bán độc lập. Chúng ít bị vướng các điều kiện tiên quyết phức tạp và là mục tiêu tuyệt vời để lên kịch bản bot tự động hóa theo mô-đun.

- **Bát Quái Linh Bàn (Root ID: 4006000)**
  - Phân tích chi tiết: Đã được hợp nhất làm quy chuẩn hướng dẫn tại `docs/task-automation-analysis-guide.md`
- **Giấc Mộng Vệ Binh (Root ID: 805)**
  - Phân tích chi tiết: `docs/act805-guardian-dream-analysis.md` (Đã có file đánh giá riêng cho minigame và đồ rớt).
- **Hành động 666 Không Không (Root ID: 666)**
  - Phân tích chi tiết: `docs/act666-kongkong-analysis.md`

## 4. Các Chuỗi Hoạt động Đang Chờ / Chưa Ánh xạ

Kho chứa các nhiệm vụ đã điểm danh được trên XML nhưng chưa được chạy qua hệ thống phân tích luồng chuẩn.

- **Truyền Thuyết Địa Tâm**
- **Thu Thập Linh Thú**
- **Điểm Danh Hàng Ngày và Minigame**
  - Lưu ý: Một số logic đã được code trong `activity_minigames.h` nhưng cần được lập hồ sơ rà soát lại (mapping) đối chiếu chéo lên XML chuẩn.
- **Sự kiện Shuangtai (Song Đài)**
  - Trạng thái: Tính năng tự động đã được làm sẵn tại `shuangtai.h` nhưng chưa có tài liệu XML rà soát ngược đàng hoàng.

## 5. Quy thức Bảo trì Danh mục

Khi có một chuỗi nhiệm vụ mới được chỉ định để tự động hóa:
1. Định vị gốc thư mục (root ID) của nó trong `taskinfo.xml` và điền tên nó vào danh lục này.
2. Tạo một file markdown báo cáo phân tích riêng cho chuỗi đó, sử dụng cấu trúc mẫu từ `docs/task-automation-analysis-guide.md`.
3. Gắn link markdown mới đó quay lại danh mục tổng (quy về mục "Đã xử lý").
4. TUYỆT ĐỐI KHÔNG dự đoán thứ tự gửi gói tin nếu chưa test chéo qua AS3 và bằng chứng packet động.