# Phân tích Đặc huấn của Con Cưng của Trời (Act666 / Active20140515KongKong)

## 1. Kết quả tải xuống

- Lối vào hoạt động đến từ `swf_cache/weeklyactivity`, mục tương ứng là `id=26040702`, tên là `Đặc huấn của Con Cưng của Trời` (天之骄子的特训).
- SWF đích đã được tải xuống tại `swf_cache/Active20140515KongKong.swf`.
- Kịch bản đã được xuất bằng FFDec tới thư mục `swf_cache/decompiled/Active20140515KongKong/scripts/`.

## 2. Cấu trúc AS3

- `Active20140515KongKong.as`
  - Lớp đầu vào, chỉ chịu trách nhiệm gắn `ActEnterUI`.
  - Hủy `EventCenter` và `DataCenter` khi thoát.
- `view/ActEnterUI.as`
  - Tải `enter_ui.swf`.
  - Đăng ký `KongKongControl` và `KongKongModel`.
  - Kéo `open_ui` lần đầu tiên, điều khiển các nút bắt đầu, càn quét, mua lượt, đổi thưởng và tăng tốc hồi chiêu (CD) dựa trên giá trị trả về.
- `model/KongKongModel.as`
  - Phân tích thống nhất các gói tin trả về từ máy chủ.
  - Xử lý `open_ui / start_game / end_game / exchange_info / exchange / buy_play_game_cnt / buy_monster / reward / clear_cd_time / buy_gourd / sweep_info / sweep / catch / catch_res`.
- `view/GameMain.as`
  - Minigame bắn súng màn hình ngang trên máy khách.
  - Khi khoảng cách tiến đến 3000 sẽ kích hoạt báo hiệu boss và đánh boss.
  - Khi kết thúc, tính toán dựa trên `medalCount + checkCode + isPass` và gửi gói tin phản hồi.
- `view/Act666ExchWin.as` / `view/KongKongSweepWin.as`
  - Bảng đổi thưởng và bảng càn quét.
- `view/GameDeadAlert.as`
  - Khi chết có thể hồi sinh hoặc kết thúc trực tiếp.

## 3. Kết luận Giao thức (Protocol)

Hoạt động này sử dụng giao thức hoạt động thống nhất:

```text
Opcode = 1185429 (ACTIVITY_QINGYANG_NEW_SEND)
Params = 666
Body   = [opName:string][int32...]
```

Các thao tác chính và định dạng gói tin trả về như sau:

| Thao tác | Phần thân yêu cầu | Phần thân trả về |
|---|---|---|
| `open_ui` | Không có | `playCount, restTime, medalCount, passCount, getAwardFlag, bestFlag, rewardList[4], catchId, catchList[2]` |
| `start_game` | `isPropt, type`, UI hiện tại thực tế truyền `0, 0` | `result, playCount, checkCode` |
| `choose_type` | `type` | `result, type`, mã nguồn hiện tại cơ bản không đi tới |
| `end_game` | `medalCount, clientCheckCode, isPass` | `type, playCount, restTime, score, bestFlag, passCount, medalCount, exp, coin` |
| `exchange_info` | Không có | `len + [current, max] * len` |
| `exchange` | `index, num` | `result, index, currentCount, medalCount` |
| `buy_play_game_cnt` | Không có | `result, playCount` |
| `buy_monster` | Không có | `result` |
| `reward` | `index` | `result` |
| `clear_cd_time` | `restTime` | `result, restTime, kbCoin` |
| `buy_gourd` | `type, buyCnt` | `result` |
| `catch` | `monsterId` | `result, medalCount` |
| `catch_res` | Không có | Chỉ kích hoạt làm mới UI |
| `sweep_info` | Không có | `medalCount, exp, coin` |
| `sweep` | Không có | `type, playCount, restTime, medalCount, exp, coin` |

Bổ sung:

- `clientCheckCode = serverCheckCode & (userId % serverCheckCode)`.
- `game_event`、`choose_type` được định nghĩa trong AS3 nhưng gần như không được sử dụng trong luồng chính, tạm thời có thể coi là giao diện dự phòng.

## 4. Kết luận Lối chơi Máy khách

- Đây là một minigame bắn súng được render trên máy khách, không phải viên đạn nào cũng gửi về máy chủ.
- Máy chủ chỉ quan tâm:
  - Bắt đầu `start_game`
  - Kết thúc `end_game`
  - Các thao tác phụ trợ như đổi thưởng / càn quét / mua lượt / mua pet / làm choán CD / bắt pet
- Trong `GameMain`, điều thực sự quyết định kết quả là `medalCount` được tích lũy trên máy khách, sau đó gửi kèm với `checkCode` do máy chủ cung cấp.

## 5. Phương án C++

### 5.1 Tầng Trạng thái

Khuyến nghị thêm mới một `Act666State`, hoặc tái sử dụng trực tiếp một nhóm trường trong quản lý trạng thái hoạt động hiện có, ít nhất cần lưu:

- `playCount`
- `restTime`
- `medalCount`
- `passCount`
- `getAwardFlag`
- `bestFlag`
- `rewardList[4]`
- `catchId`
- `catchList[2]`
- `limitList`
- `checkCode`
- `awardList`
- `isPass`

### 5.2 Tầng Gói tin

Tái sử dụng `BuildActivityPacket(Opcode::ACTIVITY_QINGYANG_NEW_SEND, 666, op, values)` hiện có:

- `SendAct666OpenUiPacket()`
- `SendAct666StartGamePacket()`
- `SendAct666EndGamePacket(medalCount, clientCheckCode, isPass)`
- `SendAct666ExchangeInfoPacket()`
- `SendAct666ExchangePacket(index, num)`
- `SendAct666BuyCountPacket()`
- `SendAct666ClearCdPacket(restTime)`
- `SendAct666SweepInfoPacket()`
- `SendAct666SweepPacket()`
- `SendAct666CatchPacket(monsterId)`

### 5.3 Phân tích Gói trả về

Trong `src/hook/wpe_hook.cpp` thêm `ProcessAct666Response()`:

- Trước tiên đọc chuỗi op
- Tùy theo bảng trên để phân tích int32
- Sau khi thành công, đồng bộ lên tầng trạng thái
- Làm mới đồng bộ các đoạn text thông báo của `UIBridge`

### 5.4 Luồng Tự động hóa

Phương án tối thiểu khả thi:

1. Kéo `open_ui`
2. Kiểm tra số lượt và thời gian hồi chiêu
3. Nếu đi theo hướng càn quét và `bestFlag > 0`, trước tiên kéo `sweep_info` rồi gửi `sweep`
4. Nếu không, kéo `start_game`
5. Sau khi lấy được `checkCode` thì submit `end_game`

Nếu muốn tự động hóa hoàn chỉnh, có thể cấu trúc minigame máy khách thành một dạng mô phỏng đa luồng, nhưng điều này không bắt buộc đối với vòng gửi nhận tối thiểu của giao thức.

### 5.5 Điểm Tích hợp

- `src/hook/wpe_hook.cpp`
  - Thêm trạng thái Act666, hàm gửi, xử lý gói trả về và lối vào luồng.
- `include/internal/activity_states_internal.h`
  - Nếu làm trạng thái độc lập, đặt một `Act666State` mới.
- `include/protocol/packet_protocol.h`
  - Không cần opcode mới, `ACTIVITY_QINGYANG_NEW_SEND` hiện tại đã đủ dùng.
- `src/core/web_message_handler.cpp`
  - Nếu frontend muốn thêm nút bấm, bổ sung thêm một đường vào lệnh UI.

## 6. Kết luận

- Hoạt động này sử dụng đúng giao thức hoạt động thống nhất `ID=666`, không cần xây dựng một bộ opcode khác.
- Phía C++ nên ưu tiên hoàn thành vòng kín "trạng thái + giao thức + kết toán" trước, chưa cần viết lại toàn bộ cơ chế bắn súng máy khách.
- Nếu cần, tôi có thể trực tiếp bổ sung nhánh Act666 trong `wpe_hook.cpp` dựa theo phương án này.