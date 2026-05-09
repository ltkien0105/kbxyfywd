# Phân tích Cốt truyện Chính 2001000 Khủng hoảng Hoàng thành và Quy chuẩn Hộp danh sách Khu vực Nhiệm vụ

Tài liệu này chỉ phân tích `2001000 Khủng hoảng Hoàng thành` (皇城危机), không trộn lẫn với các nhiệm vụ chính hay phụ khác. Đây là một trong những điểm xuất phát của tuyến chính family 2, phù hợp làm mẫu quy chuẩn đầu tiên cho "Hộp danh sách Khu vực Nhiệm vụ + Checkbox + Hoàn thành Hàng loạt + Ẩn Đã hoàn thành".

## 1. Phạm vi Lần này

- Nhiệm vụ Gốc: `2001000`
- Tên: `Khủng hoảng Hoàng thành`
- loại (type): `2`
- độ khó (diff): `4`
- Số lượng nhiệm vụ con: `8`
- Ràng buộc: Root hiện tại không có `before`, và `after` cũng không tham gia sắp xếp trong root. Thứ tự thực sự sẽ tiến triển theo chuỗi subtask.

## 2. Khung Xương Đã Xác Nhận

| subtaskID | Tên | targetScene | country | award | Trạng thái Kiểm chứng |
| --- | --- | ---: | ---: | --- | --- |
| 2001001 | Nhận sứ mệnh | 1003 | 10 | 600 Xu / 700 EXP | XML đã kiểm chứng, Chuỗi hành động đã xác nhận, packet chờ xác nhận |
| 2001002 | Âm thanh kỳ lạ | 2003 | 20 | 700 Xu / 800 EXP | XML đã kiểm chứng, Chuỗi hành động đã xác nhận, packet chờ xác nhận |
| 2001003 | Vô tình bóc bảng vàng | 2001 | 20 | 800 Xu / 1000 EXP | XML đã kiểm chứng, Chuỗi hành động đã xác nhận, packet chờ xác nhận |
| 2001004 | Ký ức của Đường Thái Tông | 2002 | 20 | 800 Xu / 800 EXP | XML đã kiểm chứng, Chuỗi hành động đã xác nhận, packet chờ xác nhận |
| 2001005 | Khủng hoảng rình rập | 2003 | 20 | 900 Xu / 1000 EXP | XML đã kiểm chứng, Chuỗi hành động đã xác nhận, thêm prompt đặc thù `200100504` chờ bổ sung |
| 2001006 | Đường Tam Tạng trong truyền thuyết | 2003 | 20 | 800 Xu / 1200 EXP | XML đã kiểm chứng, Chuỗi hành động đã xác nhận, packet chờ xác nhận |
| 2001007 | Ngươi không thoát được đâu! | 2005 | 20 | 900 Xu / 1200 EXP | XML đã kiểm chứng, Chuỗi hành động đã xác nhận, packet chờ xác nhận |
| 2001008 | Chỉ thị của Thần Tiên | 2005 | 20 | 700 Xu / 1000 EXP / 100 Tu vi / 50 Biết ơn | XML đã kiểm chứng, Chuỗi hành động đã xác nhận, sẽ hiển thị thêm `gratitude` |

## 3. Thứ Tự Tuyến Chính

1. `Nhận sứ mệnh`
2. `Âm thanh kỳ lạ`
3. `Vô tình bóc bảng vàng`
4. `Ký ức của Đường Thái Tông`
5. `Khủng hoảng rình rập`
6. `Đường Tam Tạng trong truyền thuyết`
7. `Ngươi không thoát được đâu!`
8. `Chỉ thị của Thần Tiên`

Đặc điểm cốt lõi của chuỗi này là sự tiến triển đa dạng nhưng chỉ trên 1 tuyến duy nhất. `describe` chịu trách nhiệm text cốt truyện, `targets` chịu trách nhiệm cảnh mục tiêu, `country` chịu trách nhiệm neo vị trí khu vực, và `award` cho phần thưởng.

## 4. Chuỗi Hành Động Đã Xác Nhận

Nguồn: `data/taskinfo.xml`, `TaskView` / `TaskDialog` / `TaskControl`, `data/npc.xml`, `data/map.xml`, và trang hướng dẫn 4399.
Những gì xác nhận ở đây là "đi đâu, nhấn vào đâu, và thứ tự", không phải công thức packet cuối cùng.

| subtaskID | Hành động Đã Xác nhận | Đối tượng Cảnh / Điểm neo | Ghi chú |
| --- | --- | --- | --- |
| 2001001 | Nhận nhiệm vụ tại Chú khoang lái hoặc Hồ sơ Nhiệm vụ | Khoang lái / Trợ lý Khoang nhỏ(12005) | Đăng ký bắt đầu |
| 2001002 | Mở bản đồ lớn tới Đại Đường, sau đó đi Song Xoa Lĩnh | Song Xoa Lĩnh / Bản đồ lớn | Lần hạ cánh đầu |
| 2001003 | Tới Thành Trường An, chạm vào bảng vàng bên cổng thành | Thành Trường An / Bảng Hoàng Gia | Vào tuyến Đại Đường |
| 2001004 | Vào Hoàng cung, nói chuyện với Đường Thái Tông | Đường Thái Tông | Cốt lõi Hoàng thành |
| 2001005 | Quay lại Song Xoa Lĩnh, chạm vào cây nĩa trên thân cây, liên tục nhấn nút để kéo ra, sau đó dùng phép thuật đầu tiên "Tia hạt" để đánh vào khu vực phát sáng | Cây lớn(21101), Nĩa(21103/21110), Kết giới phát sáng(21108), Bột phát sáng(21112) | Tương ứng với nút hướng dẫn đặc thù `200100504` |
| 2001006 | Click vào người được cứu trên cây và trò chuyện | Đường Tam Tạng(21105/21111) | Giai đoạn Cứu hộ |
| 2001007 | Từ bên trái Song Xoa Lĩnh đi tới Đỉnh Ngũ Chỉ Sơn, chạm vào hòn đá, đứng lên, kéo dây leo ở trên đỉnh, tìm Hổ Ma Vương | Đỉnh Ngũ Chỉ Sơn / Đá Sư đồ / Hổ Ma Vương(21502) | Tương tác Địa hình |
| 2001008 | Thảo luận với người bí ẩn ở đỉnh Ngũ Chỉ Sơn | Người bí ẩn(21514) | Kết thúc |
(Phần sau đã được xử lý và phân tích packet...)