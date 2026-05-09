# Phân tích Tự động hóa Nhiệm vụ và Quy chuẩn Biên soạn

## 1. Mục tiêu

Tài liệu này chỉ làm một việc duy nhất: chia tách "tự động hóa nhiệm vụ" thành một quy trình có thể thúc đẩy từng bước, có thể kiểm chứng và có thể mở rộng.

- Phân tích từng chuỗi (series) một, sau đó mới mở rộng sang chuỗi tiếp theo.
- Xác nhận bộ khung XML trước, sau đó xác nhận các nút (node) runtime và gói tin trả về (packet), cuối cùng mới chốt thành các bước tự động hóa.
- Sản phẩm cuối cùng của tài liệu này không phải là một "Khung phân tích", mà là "công thức gói tin có thể thực thi" (executable packet recipe): mỗi một bước đều phải chỉ rõ gói tin bắt nguồn từ đâu, gửi thế nào, đợi gói tin trả về nào và khôi phục từ đâu.
- Hiện tại mới chỉ lấy `Bát Quái Linh Bàn` làm mẫu, các chuỗi nhiệm vụ sau sẽ tiếp tục được thêm vào theo cùng mẫu này.

## 2. Thứ tự Ưu tiên Nguồn Code

Cơ sở thực tế của các nhiệm vụ tự động hóa, theo mức độ ưu tiên từ cao xuống thấp là:

1. `data/taskinfo.xml`
2. `config/tasktheme.xml` (Được tải ở runtime bởi `SortedTaskList`; hiện không lưu trực tiếp trong snapshot kho lưu trữ)
3. Code AS3 đã dịch ngược:
   - `TaskInfoXMLParser.as`
   - `TaskXMLParser.as`
   - `TaskList.as`
   - `SortedTaskList.as`
   - `TaskView.as`
   - `TaskControl.as`
4. Thực thi tự động trên máy khách cục bộ (local):
   - `src/hook/wpe_hook.cpp`

Bổ sung:
- `data/taskinfo.xml` chỉ cung cấp thẻ root / subtask / targets / award / after / before, không cung cấp từng bước hành động.
- `taskprop/<dialogId/1000>0.xml` mới là nguồn dữ liệu đầu tay của các nút đối thoại trong lúc chạy (runtime); nếu thiếu tệp này, chỉ có thể viết đến tầng khung xương, và không thể đoán trình tự gửi gói tin làm kết luận cuối.
- ... (Các quy luật logic nội tại khác của AS3 không thay đổi, nhấn mạnh độ ưu tiên của dữ liệu runtime so với XML thô).
Khi ra quyết định, luôn ưu tiên tin tưởng vào quá trình chạy gói tin (packet behavior) thực tế, sau đó mới đến thứ tự XML bề ngoài.

## 3. Quy trình Phân tích Chuẩn

Mọi series mới đều phải đi theo một quy trình chung, không được nhảy bước, không dự đoán song song.

1. **Xác định Phạm vi**: Xác nhận tên chuỗi, root `taskID`, các subtask dự kiến.
2. **Tìm Điểm neo (Anchor)**: Tìm bộ rễ và các nhánh trong `data/taskinfo.xml`, dò tên trong AS3.
3. **Trích xuất Khung Nhiệm vụ**: Ghi lại các tham số, đánh dấu những bước là đối thoại, thu thập, chiến đấu, animation...
4. **Đọc Diễn giải Runtime**: Xem các thư viện Parser / Control trong game diễn dịch XML ra hành động kiểu gì.
5. **Khôi phục Thứ tự Thi hành**: Chỉnh lại thứ tự theo thực tế packet gửi đi.
6. **Xác định Quy tắc Gói tin (Packet)**: Nguồn, thao tác, phản hồi kết quả...
7. **Đánh dấu Ngắt và Phục hồi (Interrupt / Resume)**: Ghi lại các mốc timeout hay retry.
8. **Đưa ra Kết luận**: Chỉ output các phần Đã Kiểm Chứng. Phần còn lại đưa vào "Chờ xác nhận".

## 4. Ràng buộc Cơ bản

- Không phân tích tất cả các chuỗi nhiệm vụ cùng một lúc.
- Mỗi lần chỉ làm cho một chuỗi, hoặc một mắt xích trong chuỗi.
- Không coi thứ tự sắp xếp trên UI là thứ tự thực tế.
- Bắt buộc phải nhìn đồng thời `taskID`, `subtaskID`, `dialogId`, `sceneId`. Miễn có bước nào không rõ đường đi packet hay chuyển cảnh, lập tức dừng lại bổ sung bằng chứng.

## 5. Quy tắc AS3 / XML

- `taskinfo.xml` chuyên định nghĩa khung xương nhiệm vụ.
- `TaskInfoXMLParser` phân giải XML ra danh sách có thể thao tác, check quan hệ Tiền/Hậu.
- `TaskXMLParser` phân giải ở thời gian thực (như `alert`, `desc`, `battle`, vv.).
- `TaskList` là danh sách trạng thái của nhiệm vụ trên máy (ví dụ Chưa nhận, Đang làm, Đã xong).

### 5.7 Quy tắc Ánh xạ Gói tin (Packet Mapping)
Khi phân tích, "Loại nút" phải được phiên dịch sang "Hành động gửi".
- *Truy vấn tiến độ*: `QueryTaskZoneUserTaskListProgress()`
- *Đối thoại/Lựa chọn thông thường*: `SendTaskZoneTalkPacket()`
- *Giao tiếp chạm (Click)*: `SendTaskZoneClickPacket()`
- *Chuyển cảnh*: Đi qua `TaskControl.toOtherScene()`
- *Bắt đầu Mở đánh (Battle)*: `SendBattlePacket()` -> `STARTBATTLE` ...

### 5.8 Hệ thống Trám lỗ hổng (Gap Filling)
Bất cứ khi nào thiếu dữ liệu tĩnh hay file thoại thực tế (taskprop), phải làm theo trình tự thủ tục chuẩn để vá: Tìm Neo tĩnh -> Dịch giải Runtime -> Bù bằng chứng Gói tin.

## 6. Mẫu Bát Quái Linh Bàn
Cung cấp chi tiết cách trò chơi gửi các chuỗi tiến độ từ bước `400600101` đến `400600803` theo logic tĩnh và động kết hợp. 

## 7. Quy cách Phân tích Chuỗi Nhiệm vụ về sau
Các chuỗi dài như Truyền thuyết Ma tinh, Địa tâm hay Kabu,... đều đi qua đủ 6 bước cứng. Bất kì nhánh nào cũng phải xuất ra được 5 khối thông tin cốt lõi (điều kiện vào, subtask, packet nguồn, interrupt, mục chưa xác nhận).

## 8. Mẫu Trình bày (Template)
Dành cho người phân tích viết tài liệu khi muốn thêm 1 chủ đề nhiệm vụ mới.

## 10. Kết luận 
- Bát Quái Linh Bàn đã chuẩn hóa đầy đủ theo mốc bước tự động hóa.
- Mỗi tài liệu bắt buộc phải bám sát trục "Series Đơn - Chia Giai Đoạn - Phục Hồi".

## 12. & 13. Phân bổ Mục lục Toàn tập và Tuyến chính
Trỏ trực tiếp về các file Markdown riêng (ví dụ: `task-automation-series-full-catalog.md` và `task-automation-mainline-2001000.md`) cho mục lục tra cứu nhiệm vụ đầy đủ và nhiệm vụ cốt truyện chính.