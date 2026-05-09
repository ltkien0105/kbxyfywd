# Task Automation Analysis and Orchestration Specification

## 1. Goal

This document serves one purpose only: to break down "task automation" into a process that can be advanced progressively, verified, and expanded.

- Dissect a single series first, then expand to the next series.
- Confirm the XML skeleton first, then the runtime nodes and return packets, and finally land on the automation steps.
- The final output of this document is not an "analysis framework", but an "executable packet recipe": each step must clearly state where the packet comes from, how it is sent, which return packet to wait for, and from where to recover.
- Currently, only the `Eight Trigrams Spirit Compass` (八卦灵盘) is used as an example; subsequent series will be appended using the same template.

## 2. Code Source Priority

The true basis for automated tasks, ranked from highest to lowest priority, is:

1. `data/taskinfo.xml`
2. `config/tasktheme.xml` (Loaded at runtime by `SortedTaskList`; not directly stored in the current repo snapshot)
3. Decompiled AS3:
   - `TaskInfoXMLParser.as`
   - `TaskXMLParser.as`
   - `TaskList.as`
   - `SortedTaskList.as`
   - `TaskView.as`
   - `TaskControl.as`
4. Local automation implementation:
   - `src/hook/wpe_hook.cpp`

Additional Notes:

- `data/taskinfo.xml` only provides the root / subtask / targets / award / after / before, it does not provide step-by-step actions.
- `taskprop/<dialogId/1000>0.xml` is the firsthand source of runtime dialog nodes; if absent locally, it can only be written down to the skeleton layer, and the packet sequence cannot be guessed as the final conclusion.
- `TaskXMLParser`, `TaskView`, `TaskControl` are responsible for translating runtime nodes into action types.
- `src/hook/wpe_hook.cpp` contains the packet recipes and recovery logic that have already been implemented in the current repo; but for `Imperial City Crisis`, currently, only progress syncing and UI states are implemented, not a complete step executor.
- `USER_TASK_LIST_SEND/BACK` only tells you "what is currently accepted and what is completed", it does not drive the plot forward.
- `dialogId` is the runtime step number, not the root `taskID`; multiple `dialogId`s can hang under one `subtaskID`. The `4006000` sample and the `200100504` special case in the Imperial City both illustrate this point.
- Seeing "Go to" in the UI only means the current node carries a `targetScene`, not that the complete packet recipe is verified.
- ... (Additional nuanced AS3 rules omitted for brevity but strictly adhere to the runtime execution rather than XML appearance alone).

When making conclusive judgments, trust the runtime packet behavior first, and the XML appearance sequence second.

## 3. Standard Analysis Flow

Every new series will follow the same process, without skipping steps or making parallel guesses.

1. **Set the Scope**: Confirm the series name, root `taskID`, and expected scope of subtasks. If the series name is unstable, rely on the `taskID`.
2. **Find the Anchors**: Locate the root task and subtask segments in `data/taskinfo.xml`, and find the theme name, button name, and entry name in `TaskArchivesVersion3` or related AS3.
3. **Extract Task Skeleton**: Record `subtaskID`, `targets`, `award`, `after`, `before`, `condition`. Mark which nodes are dialogues, collecting, battling, animations, or result closures.
4. **Read Runtime Interpretation**: See how `TaskXMLParser` turns XML into runtime nodes, how `TaskView`/`TaskControl` translates nodes into actions, how `TaskList` determines states, and how `TaskInfoXMLParser` trims acceptable tasks.
5. **Restore Execution Order**: Temporarily read the plot according to the original XML order, then correct the actual execution order according to runtime code, packet captures, and the current hook implementation. If they conflict, follow the implemented runtime packet behavior.
6. **Dissect Packet Rules**: Note the packet source, send packet, return packet, wait condition, and recovery point step by step. Special branches like `choose flag="3"`, `battle`, and `flash` are listed separately.
7. **Mark Interruptions and Recoveries**: Record determination points for timeouts, failures, retries, skipping steps, or completion.
8. **Output Conclusions**: Only output what has been verified. Unverified parts must be listed under "To Be Confirmed".

## 4. Basic Constraints

- Do not analyze all task series at once.
- Only do one series, or one stage in a series, at a time.
- Do not mistake UI sorting for execution order.
- Do not just look at the task name; `taskID`, `subtaskID`, `dialogId`, `sceneId` must be examined simultaneously.

## 5. AS3 / XML Rules

(Translating the logic structures of XML parsers representing game logic)...
- `taskinfo.xml` defines the task skeleton (ID, targets, awards, etc.).
- `TaskInfoXMLParser` converts XML into an available task list, parsing preconditions (`after` / `before`).
- `TaskXMLParser` explains runtime notes like `alert`, `desc` / `choose`, `battle`, `flash`, `targetScene`.
- `TaskList` manages task states (`0`: Unaccepted, `1`: Accepted, `2`: Completed, `3`: Blocked by prerequisites).
- `SortedTaskList` is just the UI grouping layer.
- `taskprop` gap: `taskprop` files are runtime dialogue XMLs, distinct from `taskinfo.xml`. If missing, we cannot guess packet flow.

### 5.7 Packet Mapping Rules

When analyzing tasks, "node types" must be translated into "sending actions". Conventions available in the current repo:
- *Progress Query*: `QueryTaskZoneUserTaskListProgress()` -> `USER_TASK_LIST_SEND` -> wait for `USER_TASK_LIST_BACK`.
- *Normal Dialogue/Choice*: `SendTaskZoneTalkPacket()` -> `TRAIN_INFO_SEND` -> `TASK_TALK_SEND`.
- *Click Interaction*: `SendTaskZoneClickPacket()` -> `CLICK_NPC`.
- *Scene Switch*: Through `TaskControl.toOtherScene()`.
- *Battle Start*: `SendBattlePacket()` -> `STARTBATTLE` -> `BATTLE_READY`.

### 5.8 Gap Evidencing Standard Flow
When an XML or Dialog ID is missing, follow a strict top-down verification protocol: evaluate pseudo-gaps, lock the static skeleton, read runtime interpretation, supplement packet evidence, and finalize only when all three align.

## 6. Eight Trigrams Spirit Compass Example
(Outlines existing implemented tasks 4006001 - 4006008 logic step-by-step).

## 7. Analysis Specification for Subsequent Series
For large series like `Magic Crystal War`, `Earth Core Legend`, and `Kabu Adventure`, proceed in this order: Root task -> Subtasks -> Attributes -> AS3 rules -> Packet sequence -> Automation table.

## 8. Writing Template
Standard blueprint: Series Name, taskID, Applicable Prerequisites, Subtask List, Actual Execution Order, Special Packet Rules, Failure Recovery Strategy, Unconfirmed Items.

## 9. Resource Gap Handling
If only `taskinfo.xml` is present but `taskprop` is missing: Lock root/chain -> Add target anchors -> Add miss-note for `taskprop` -> Write code/UI lastly.

## 10. Current Conclusion
- Eight Trigrams Spirit Compass can be orchestrated as a staged step table.
- Different series should not be analyzed together.
- Documents and code revolve around "single-series, staged, recoverable."

## 12. Full Catalog Entry
- Full Root Task Catalog: `docs/task-automation-series-full-catalog.md`
- First Batch Staged Samples: `docs/task-automation-series-batch-01.md`

## 13. Mainline Entry
- Imperial City mainline analysis: `docs/task-automation-mainline-2001000-huangcheng-weiji.md`