# Heaven's Favorite Special Training Analysis (Act666 / Active20140515KongKong)

## 1. Download Results

- The activity entry comes from `swf_cache/weeklyactivity`, the corresponding item is `id=26040702`, and the name is `Heaven's Favorite Special Training` (天之骄子的特训).
- The target SWF has been downloaded to `swf_cache/Active20140515KongKong.swf`.
- The scripts have been exported using FFDec to `swf_cache/decompiled/Active20140515KongKong/scripts/`.

## 2. AS3 Structure

- `Active20140515KongKong.as`
  - Entry class, only responsible for mounting `ActEnterUI`.
  - Destroys `EventCenter` and `DataCenter` upon exiting.
- `view/ActEnterUI.as`
  - Loads `enter_ui.swf`.
  - Registers `KongKongControl` and `KongKongModel`.
  - Pulls `open_ui` for the first time, and controls the start, sweep, buy attempts, exchange, and cooldown acceleration buttons based on the return values.
- `model/KongKongModel.as`
  - Uniformly parses server response packets.
  - Handles `open_ui / start_game / end_game / exchange_info / exchange / buy_play_game_cnt / buy_monster / reward / clear_cd_time / buy_gourd / sweep_info / sweep / catch / catch_res`.
- `view/GameMain.as`
  - Local side-scrolling shooting minigame.
  - Triggers boss preview and boss battle when distance advances to 3000.
  - Upon ending, settles according to `medalCount + checkCode + isPass` and sends the response packet.
- `view/Act666ExchWin.as` / `view/KongKongSweepWin.as`
  - Exchange panel and sweep panel.
- `view/GameDeadAlert.as`
  - Can revive or directly end after death.

## 3. Protocol Conclusions

This activity follows the unified activity protocol:

```text
Opcode = 1185429 (ACTIVITY_QINGYANG_NEW_SEND)
Params = 666
Body   = [opName:string][int32...]
```

The key operations and response packet formats are as follows:

| Operation | Request Body | Response Body |
|---|---|---|
| `open_ui` | None | `playCount, restTime, medalCount, passCount, getAwardFlag, bestFlag, rewardList[4], catchId, catchList[2]` |
| `start_game` | `isPropt, type`, currently UI actually passes `0, 0` | `result, playCount, checkCode` |
| `choose_type` | `type` | `result, type`, currently basically unreached in source code |
| `end_game` | `medalCount, clientCheckCode, isPass` | `type, playCount, restTime, score, bestFlag, passCount, medalCount, exp, coin` |
| `exchange_info` | None | `len + [current, max] * len` |
| `exchange` | `index, num` | `result, index, currentCount, medalCount` |
| `buy_play_game_cnt` | None | `result, playCount` |
| `buy_monster` | None | `result` |
| `reward` | `index` | `result` |
| `clear_cd_time` | `restTime` | `result, restTime, kbCoin` |
| `buy_gourd` | `type, buyCnt` | `result` |
| `catch` | `monsterId` | `result, medalCount` |
| `catch_res` | None | Only triggers UI refresh |
| `sweep_info` | None | `medalCount, exp, coin` |
| `sweep` | None | `type, playCount, restTime, medalCount, exp, coin` |

Additional notes:

- `clientCheckCode = serverCheckCode & (userId % serverCheckCode)`.
- `game_event` and `choose_type` are defined in AS3, but barely used in the main flow, so they can be considered reserved interfaces for now.

## 4. Local Gameplay Conclusions

- This is a locally rendered shooting minigame, not every bullet is sent to the server.
- The server only cares about:
  - Game start `start_game`
  - Game end `end_game`
  - Auxiliary operations like exchange / sweep / buy attempts / buy pet / cooldown / catch
- In `GameMain`, what truly determines the settlement is the locally accumulated `medalCount`, which is then submitted along with the `checkCode` provided by the server.

## 5. C++ Solution

### 5.1 State Layer

It is recommended to add a new `Act666State`, or directly reuse a set of fields in the existing activity state manager. At minimum, save:

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

### 5.2 Packet Layer

Reuse the existing `BuildActivityPacket(Opcode::ACTIVITY_QINGYANG_NEW_SEND, 666, op, values)`:

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

### 5.3 Response Parsing

In `src/hook/wpe_hook.cpp`, add `ProcessAct666Response()`:

- First read the string op
- Then parse int32 according to the table above
- Upon success, synchronize to the state layer
- Synchronously refresh `UIBridge` prompt text

### 5.4 Automation Flow

Minimum Viable Product (MVP) solution:

1. Pull `open_ui`
2. Validate attempts and cooldown
3. If sweeping and `bestFlag > 0`, pull `sweep_info` first then send `sweep`
4. Otherwise pull `start_game`
5. After getting `checkCode`, submit `end_game`

If full automation is desired, the local minigame could also be simulated with a thread, but this is not a prerequisite for the minimum protocol closed loop.

### 5.5 Integration Points

- `src/hook/wpe_hook.cpp`
  - Add Act666 state, sending functions, response handling, and thread entry points.
- `include/internal/activity_states_internal.h`
  - If making an independent state, place a new `Act666State` here.
- `include/protocol/packet_protocol.h`
  - No new opcode is needed, the existing `ACTIVITY_QINGYANG_NEW_SEND` is sufficient.
- `src/core/web_message_handler.cpp`
  - If a button is added to the frontend, supplement a UI command entry.

## 6. Conclusion

- This activity strictly uses `ID=666` of the unified activity protocol and doesn't require a new set of opcodes.
- The C++ side should prioritize completing the "state + protocol + settlement" closed loop, rather than entirely rewriting the local shooting gameplay just yet.
- If needed, I can directly add the Act666 branch in `wpe_hook.cpp` following this solution.