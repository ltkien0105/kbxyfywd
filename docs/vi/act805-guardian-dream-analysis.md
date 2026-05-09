# Phân tích Giấc mơ Vệ thần (Act805 / Activity20140213Lele)

## 1. Định vị Hoạt động Tuần

- Mục `swf_cache/weeklyactivity` mới nhất là `id=26041002`, tên `Giấc mơ vệ thần` (守护梦境).
- SWF lối vào tương ứng là `assets/activity/201402/20140213Lele/Activity20140213Lele.swf`.
- Đã tải xuống tại `swf_cache/Activity20140213Lele.swf` và trích xuất kịch bản vào `swf_cache/decompiled/Activity20140213Lele/scripts/`.

## 2. Cấu trúc AS3

- Lớp đầu vào: `Activity20140213Lele.as`
  - Đăng ký `KBLeleControl`, `KBLeleModel`, giao diện chính là `KBEnterView`.
- Tầng Điều khiển: `control/KBLeleControl.as`
  - Gửi gói tin thống nhất thông qua `MsgDoc.OP_CLIENT_ACTIVITY_QINGYANG_NEW.send`.
  - Các thao tác chính là `game_info / start_game / end_game / sweep_info / sweep`.
- Tầng Mô hình: `model/KBLeleModel.as`
  - Phân tích tất cả các gói tin trả về của 805.
  - `decodeOpenUI()` đọc số lượt, hồi chiêu, huy chương, điểm cao nhất lịch sử, điểm lần trước.
  - `decodeStartGame()` đọc `checkcode`.
  - `decodeEndGame()`, `decodeSweepInfo()`, `decodeSweep()` đọc phần thưởng.
- Tầng Dữ liệu: `KBDataCenter.as`, `model/KBLeleData.as`
  - `max_score = 350`, thú cưng đổi từ hoạt động là `1354 Chim mắt to` (大眼雀).
- Tầng Chiến đấu: `view/KBBatttleView.as`
  - Minigame ở máy khách (local), khi kết thúc gửi `sendEndGame(awardNum, 1)`.

## 3. Kết luận Giao thức

- ID Hoạt động: `805`
- Opcode Yêu cầu: `1185429`
- Opcode Trả về: `1316501`
- Định dạng yêu cầu:

```text
Opcode = 1185429 (ACTIVITY_QINGYANG_NEW_SEND)
Params = 805
Body   = [opName:string][int32...]
```

- Các yêu cầu / thao tác đặc trưng:
  - `game_info`
    - Trả về: `result, playCount, restCdTime, isPop, medalNum, historyBestScore, lastScore`
  - `start_game`
    - Trả về: `result, playCount, checkCode`
  - `end_game`
    - Gửi đi: `clientCheckCode, score`
    - Trả về: `result, playCount, restCdTime, medalNum, exp, coin`
  - `sweep_info`
    - Trả về: `result, endType, medal, exp, coin`
  - `sweep`
    - Trả về: `result, playCount, restTime, medal, exp, coin`

## 4. Các Xác thực Quan trọng

- `clientCheckCode = userId % 1000 + checkCode + score`
- Sau khi gửi `end_game`, AS3 sẽ gửi thêm một lệnh:

```text
Opcode = 1184791 (TASK_DAILY_SEND)
Params = 3
Body   = [3, 4039001, 0]
```

- Bước này được sử dụng để đồng bộ tiến độ nhiệm vụ hàng ngày liên quan đến minigame.

## 5. Điểm Neo Code

- Thêm khai báo giao thức `Act805` mới: `include/activities/activity_minigames.h`
- Thêm `Act805State` và lối vào quản lý trạng thái: `include/internal/activity_states_internal.h`
- Thêm quy trình 1-click `Act805`, phân tích trả về và đăng ký phản hồi: `src/hook/wpe_hook.cpp`
- Lối vào "Hoạt động mới nhất" ở frontend đổi thành `Giấc mơ vệ thần`: `resources/ui.html`
- Xử lý thông báo WebView thêm `one_key_act805`: `src/core/web_message_handler.cpp`

## 6. Kết luận Thực hiện

- `Giấc mơ vệ thần` có thể chép lại trực tiếp khung minigame hoạt động thống nhất hiện tại, không cần opcode mới.
- Vòng lặp tối thiểu là:
  - `game_info`
  - Có thể càn quét thì chạy `sweep_info -> sweep`
  - Nếu không thì `start_game -> end_game(350 điểm)`
- Đoạn mã hiện tại đã được tích hợp dựa trên vòng lặp khép kín này.