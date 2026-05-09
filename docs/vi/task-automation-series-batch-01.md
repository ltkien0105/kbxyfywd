# Ghi chép Theo Lô Chuỗi Tự Động Hóa Nhiệm Vụ (Lô 1)

## 1. Nguyên tắc chia lô

Lô này chỉ ghi lại những gì hiện có thể trực tiếp xác nhận từ `data/taskinfo.xml`, `TaskInfoXMLParser`, `TaskXMLParser` và AS3/Hook hiện có.

- Chỉ ghi chép theo "một khối chuỗi"
- Chỉ viết các `taskID` / `subtaskID` / `after` / `before` / `targets` đã được kiểm chứng
- Khi phát hiện tên chủ đề và tên gốc của nhiệm vụ không nhất quán, phải ghi lại cả hai
- Đối với những phần chưa có bằng chứng, thống nhất đánh dấu là `Chờ xác nhận` (待确认)

## 2. Quy trình phân tích lô này

Lô này không phải là "chép tên nhiệm vụ" một cách trực tiếp, mà được tách ra theo cùng một bộ quy trình:

1. Trước tiên, định vị `taskID` mục tiêu trong `data/taskinfo.xml`
2. Tiếp theo, đọc chuỗi `subtaskID` và `describe` của nó, xác nhận xem đó là nhiệm vụ một bước, chuỗi ngắn hay chuỗi dài
3. Sau đó, xem `targets` và `after`, phán đoán xem nó là tuyến chính, tuyến phụ, khối cốt truyện hay chỗ dành sẵn cho chủ đề
4. Quay lại `TaskArchivesVersion3` và AS3 liên quan để xác nhận trên UI nó được gắn dưới chủ đề nào
5. Dựa theo `TaskXMLParser` phán đoán xem có các tình huống hội thoại, lựa chọn, chiến đấu, chuyển cảnh phim, hay hoạt hình kết thúc hay không
   - Đồng thời ghi chép nguồn gốc gói tin (packet), thứ tự gửi và điểm nhận gói trả về
6. Nếu có những packet đặc biệt rõ ràng hoặc điểm phục hồi đặc biệt, hãy tách nó ra thành một quy tắc độc lập

Về mặt nguyên tắc, lô này chỉ làm ba việc:

- Xác nhận ranh giới khối nhiệm vụ
- Đánh dấu rõ thứ tự thực thi
- Đánh dấu những phần chưa thể xác nhận

## 3. Hệ Ma Tinh

"Hệ Ma Tinh" ở đây đề cập đến các chuỗi liên quan được xếp vào nhóm chủ đề `Ma tinh chiến ký` trong `TaskArchivesVersion3`, cũng như các khối nhiệm vụ trong `taskinfo.xml` xoay quanh Đá Ma tinh, Năng lượng Ma tinh, và La bàn Ma tinh.

### Đã kiểm chứng

- `2002000 Núi lở đất nứt`, điểm bắt đầu là `2002001 Hòn đá đáng sợ`, `2002002 Tia ma tinh`, `2002003 Kinh văn thất lạc`
- `2009000 Hỗn thế ma đồng`, các subtask đã xác nhận bao gồm `2009001`, `2009002 Ma đồng xuất thế`, `2009003`, `2009004`, `2009005 Sự thật của Bồ Tát`
- `2014000 Truyền thuyết Hồn Thạch (Thượng)`, các subtask đã xác nhận bao gồm `2014001`, `2014002 Giải đố Rừng Trúc Tím`, `2014003`, `2014004`
- `2015000 Truyền thuyết Hồn Thạch (Hạ)`, các subtask đã xác nhận bao gồm từ `2015001` đến `2015007`
- `3072000 Sao băng hủy diệt`, thuộc khối nhiệm vụ tiếp theo liên quan đến Năng lượng Ma tinh
- `7004000 La bàn Ma tinh`, thuộc khối nhiệm vụ một bước có nguồn gốc từ Năng lượng Ma tinh

### Lộ trình phân tích

- Trước tiên đọc từ `2002000` điểm khởi đầu của "Phát hiện Đá Ma Tinh", sau đó xem mô tả của nó và các nhiệm vụ ma tinh tiếp theo có cùng chung luồng cốt truyện không.
- `2009000` khối này tiếp tục xoay quanh đá ma tinh, đá ma tinh giả và các cảnh khác nhau, điều đó cho thấy nó không đơn thuần là nhiệm vụ đối thoại.
- `2014000` và `2015000` là điểm hình của hai đoạn cốt truyện, phần đầu làm nền, phần sau hội tụ, bắt buộc phải xem thành chương trên và chương dưới.
- `7004000` chỉ có một subtask, phù hợp để ghi lại thành loại nhiệm vụ một bước chốt gọn, không nên miễn cưỡng gộp vào chuỗi dài.

### Ràng buộc thực thi

- Không gộp các nhiệm vụ kiểu này chỉ vì mô tả "trông có vẻ giống ma tinh"
- Bắt buộc trước tiên phải xem `taskID` có liên tục không, sau đó xem `subtaskID` có thể tạo thành chuỗi thực tế không
- Miễn là xuất hiện việc chuyển cảnh, giao nhận vật phẩm, chiến đấu, animation kết thúc thì không thể dùng cùng một thứ tự khuôn mẫu để đẩy thẳng
- Các nút dạng `choose flag="3"`, `battle`, `flash` của `TaskXMLParser` bắt buộc xử lý riêng

### Phán đoán hiện tại

Hệ ma tinh không phải là một chuỗi nhiệm vụ đơn, mà là nhiều khối nhiệm vụ xâu chuỗi tạo thành tuyến cốt truyện. Sau này nếu tiếp tục bổ sung hệ này, khuyến nghị nên xuất ra từng khối theo `taskID`, thay vì cố gắng viết xong mọi nhiệm vụ liên quan đến ma tinh trong một nốt nhạc.

## 4. Hệ Tâm Trái Đất (Địa Tâm)

Trong bản snapshot hiện tại, khối đại diện có thể xác nhận trực tiếp là:

- `3236000 Vị khách từ Tâm Trái Đất`
- subtask chỉ có `3236001`
- `after="2235"`

### Trạng thái hiện tại

Chuỗi này trong snapshot code hiện tại chỉ thể hiện dưới dạng chuỗi cực ngắn, và trong `taskinfo.xml` hiện cũng chỉ mới lộ ra một đoạn subtask có thể xác nhận trực tiếp.

### Lộ trình phân tích

- Trước tiên xác nhận nhiệm vụ gốc là `3236000 Vị khách từ Tâm Trái Đất`
- Sau đó đọc ra hiện tại nó chỉ có một subtask `3236001`
- Xong rồi thì xem mô tả "Viện nghiên cứu / Tiến sĩ Ding Ding" có chung một lối vào với các khối nhiệm vụ khác ở giai đoạn sau không
- Bởi vì hiện tại mới chỉ thấy khối một bước duy nhất, nên tạm thời coi nó như mồi nhử cốt truyện, đừng vội bổ sung thành một series hoàn chỉnh

### Quy tắc khi biên soạn

- Đừng viết trực tiếp "Địa tâm truyền thuyết" (Truyền thuyết Tâm trái đất) thành tên nhiệm vụ đã được xác nhận, snapshot hiện tại không có chuỗi lý tự chính xác này
- Lấy `Vị khách từ Tâm Trái Đất` làm khối gốc đã kiểm chứng trước tiên
- Nếu sau này có `tasktheme.xml` mới hoặc nhiều đoạn `taskinfo.xml` hơn thì mới bù vào ánh xạ chủ đề

## 5. Hệ Kabu

Trong snapshot hiện tại, các khối liên quan tới Kabu có thể trực tiếp xác nhận gồm có:

- `2016000 Tử Nguyệt Xích Linh`
- `3256000 Mã hiệu: Hành động Kabu!`
- `7011000 Tiểu Kabu Thật Giả`

### Đã kiểm chứng

- `2016000` không phải nhiệm vụ một bước, hiện tại ít nhất có thể thấy `2016001 Mở đặc huấn Kabu`, `2016002 Khảo nghiệm trí tuệ`, `2016003 Khảo nghiệm lòng dũng cảm`, `2016004 Đạt được Tử Nguyệt Xích Linh`
- `3256000` hiện là chuỗi ngắn, subtask `3256001` chỉ thẳng đến Viện Nghiên Cứu
- `7011000` hiện cũng là khối một bước, xoay quanh Phòng Lưu trữ và Bella

### Lộ trình phân tích

- `2016000` phải được hiểu theo 4 giai đoạn "mở màn, trải qua 2 khảo nghiệm, và nhận thưởng", không làm thành đường thẳng tắp
- `3256000` giống với điểm tiếp sức cốt truyện hơn, khi phân tích chú trọng việc nó ráp nối với chuỗi trước và sau, không quan trọng bản thân nó mấy bước
- `7011000` là chuỗi ngắn độc lập, phù hợp làm "mẫu một nhiệm vụ trong khối UI"
- Trong lô này không thấy 4 chữ "Kabu Mạo Hiểm" xuất hiện chính xác, nên đành coi nó là chủ đề ở bậc cao hơn Chờ xác nhận.

### Trạng thái hiện tại

- Snapshot hiện tại không thấy trực tiếp 4 chữ "Kabu lịch hiểm" (Kabu phiêu lưu).
- Tạm xem nó làm neo chủ đề mức trên, không viết ép thành tên gốc đã xác nhận.
- Cột mấy khối này phải tiếp tục dựa theo "khối cốt truyện" mà tách, đừng hợp nhất theo ấn tượng truyền miệng.

## 6. Lô sau bù thế nào

Lô sau chỉ được thêm cho một nhánh hệ, trình tự đề xuất:

1. Điền đủ hết một khối `taskID` trước
2. Sau đó điền tiền đề và hậu đề tạo thành khối của nó
3. Điền nguồn gốc packet, thứ tự lệnh, điểm hứng packet
4. Cuối mới bù nốt quy tắc giao điểm ở UI và automation

## 7. Mẫu chung định

Về sau từng khối hệ series viết theo mẫu:

- Tên chuỗi (Series)
- Nhiệm vụ Gốc đã Xác nhận
- Thứ tự Subtask
- Cảnh cốt lõi
- Packet hạch tâm (Nguồn / Gửi / Trả về / Điểm neo hồi phục)
- Điểm đứt đoạn
- Điểm khôi phục (Resume)
- Hạng mục chờ tiếp tục xác nhận
