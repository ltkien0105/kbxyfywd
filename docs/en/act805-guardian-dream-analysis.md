# Guardian Dream Analysis (Act805 / Activity20140213Lele)

## 1. Weekly Activity Positioning

- The latest `swf_cache/weeklyactivity` entry is `id=26041002`, named `Guardian Dream` (守护梦境).
- The corresponding entry SWF is `assets/activity/201402/20140213Lele/Activity20140213Lele.swf`.
- It has been downloaded to `swf_cache/Activity20140213Lele.swf`, and scripts have been exported to `swf_cache/decompiled/Activity20140213Lele/scripts/`.

## 2. AS3 Structure

- Entry class: `Activity20140213Lele.as`
  - Registers `KBLeleControl` and `KBLeleModel`, the main interface is `KBEnterView`.
- Control layer: `control/KBLeleControl.as`
  - Sends packets uniformly via `MsgDoc.OP_CLIENT_ACTIVITY_QINGYANG_NEW.send`.
  - Key operations are `game_info / start_game / end_game / sweep_info / sweep`.
- Model layer: `model/KBLeleModel.as`
  - Parses all 805 response packets.
  - `decodeOpenUI()` reads attempts, cooldown, medals, historical highest score, and last score.
  - `decodeStartGame()` reads `checkcode`.
  - `decodeEndGame()`, `decodeSweepInfo()`, `decodeSweep()` read rewards.
- Data layer: `KBDataCenter.as`, `model/KBLeleData.as`
  - `max_score = 350`, the activity exchange pet is `1354 Big-Eyed Bird` (大眼雀).
- Battle layer: `view/KBBatttleView.as`
  - Local minigame, submits `sendEndGame(awardNum, 1)` upon ending.

## 3. Protocol Conclusions

- Activity ID: `805`
- Request Opcode: `1185429`
- Response Opcode: `1316501`
- Request format:

```text
Opcode = 1185429 (ACTIVITY_QINGYANG_NEW_SEND)
Params = 805
Body   = [opName:string][int32...]
```

- Key operations:
  - `game_info`
    - Response: `result, playCount, restCdTime, isPop, medalNum, historyBestScore, lastScore`
  - `start_game`
    - Response: `result, playCount, checkCode`
  - `end_game`
    - Request: `clientCheckCode, score`
    - Response: `result, playCount, restCdTime, medalNum, exp, coin`
  - `sweep_info`
    - Response: `result, endType, medal, exp, coin`
  - `sweep`
    - Response: `result, playCount, restTime, medal, exp, coin`

## 4. Key Validations

- `clientCheckCode = userId % 1000 + checkCode + score`
- After sending `end_game`, AS3 will additionally send:

```text
Opcode = 1184791 (TASK_DAILY_SEND)
Params = 3
Body   = [3, 4039001, 0]
```

- This step is used to synchronize the daily task progress related to the minigame.

## 5. Code Locations

- Add new `Act805` protocol declarations: `include/activities/activity_minigames.h`
- Add `Act805State` and state management entries: `include/internal/activity_states_internal.h`
- Add `Act805` one-key flow, response parsing, and response registration: `src/hook/wpe_hook.cpp`
- Switch the "Latest Activity" entry on the frontend to `Guardian Dream`: `resources/ui.html`
- Add `one_key_act805` to WebView message handling: `src/core/web_message_handler.cpp`

## 6. Implementation Conclusions

- `Guardian Dream` can directly reuse the current unified activity minigame framework without needing a new opcode.
- The minimum closed loop is:
  - `game_info`
  - If sweep is available, go `sweep_info -> sweep`
  - Otherwise `start_game -> end_game(350 score)`
- The current code has already been integrated according to this closed loop.