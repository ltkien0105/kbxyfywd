# Mainline 2001000 Imperial City Crisis Analysis and Task Area List Box Specification

This document only analyzes `2001000 Imperial City Crisis` (皇城危机), without mixing in other mainlines or branch lines. It is one of the starting points for the family 2 mainline, suitable as the first standard template for the "Task Area List Box + Checkbox + Batch Complete + Hide Completed" feature.

## 1. Current Scope

- Root Task: `2001000`
- Name: `Imperial City Crisis`
- type: `2`
- diff: `4`
- Number of Subtasks: `8`
- Constraint: The current root has no `before`, and `after` does not participate in sorting within the root. The true order advances according to the subtask chain.

## 2. Confirmed Skeleton

| subtaskID | Name | targetScene | country | award | Validation Status |
| --- | --- | ---: | ---: | --- | --- |
| 2001001 | Accept Mission | 1003 | 10 | 600 Coin / 700 Exp | XML validated, Action chain confirmed, packet to be confirmed |
| 2001002 | Strange Sound | 2003 | 20 | 700 Coin / 800 Exp | XML validated, Action chain confirmed, packet to be confirmed |
| 2001003 | Accidentally Uncovering the Imperial Notice | 2001 | 20 | 800 Coin / 1000 Exp | XML validated, Action chain confirmed, packet to be confirmed |
| 2001004 | Memory of Emperor Taizong of Tang | 2002 | 20 | 800 Coin / 800 Exp | XML validated, Action chain confirmed, packet to be confirmed |
| 2001005 | Crisis Everywhere | 2003 | 20 | 900 Coin / 1000 Exp | XML validated, Action chain confirmed, special prompt `200100504` to be added |
| 2001006 | Legendary Tang Sanzang | 2003 | 20 | 800 Coin / 1200 Exp | XML validated, Action chain confirmed, packet to be confirmed |
| 2001007 | You Can't Escape! | 2005 | 20 | 900 Coin / 1200 Exp | XML validated, Action chain confirmed, packet to be confirmed |
| 2001008 | Immortal's Instruction | 2005 | 20 | 700 Coin / 1000 Exp / 100 Cultivation / 50 Gratitude | XML validated, Action chain confirmed, `gratitude` display to be added |

## 3. Mainline Sequence

1. `Accept Mission`
2. `Strange Sound`
3. `Accidentally Uncovering the Imperial Notice`
4. `Memory of Emperor Taizong of Tang`
5. `Crisis Everywhere`
6. `Legendary Tang Sanzang`
7. `You Can't Escape!`
8. `Immortal's Instruction`

The core feature of this chain is its single-threaded progression, not a branching tree. `describe` handles story text, `targets` handles target scenes, `country` handles area anchors, and `award` handles reward output.

## 4. Confirmed Action Chain

Sources: `data/taskinfo.xml`, `TaskView` / `TaskDialog` / `TaskControl`, `data/npc.xml`, `data/map.xml`, and the 4399 strategy page.
What is confirmed here is "where to go, what to click, and the sequence", not the final packet recipe.

| subtaskID | Confirmed Actions | Scene Object / Anchor | Notes |
| --- | --- | --- | --- |
| 2001001 | Accept task at Cockpit Uncle or Task Archives | Cockpit / Cabin Assistant(12005) | Starting registration |
| 2001002 | Open world map to Datang, then go to Shuangcha Ridge | Shuangcha Ridge / World Map | First descent |
| 2001003 | Go to Chang'an City, click the Imperial Notice by the city gate | Chang'an City / Imperial Notice | Enter Datang mainline |
| 2001004 | Enter the Imperial Palace, talk to Emperor Taizong of Tang | Emperor Taizong | Imperial city core |
| 2001005 | Return to Shuangcha Ridge, click the fork on the tree trunk, repeatedly click to pull it out, then use the first spell "Particle Ray" to hit the glowing area | Big Tree(21101), Fork(21103/21110), Glowing Barrier(21108), Glowing Powder(21112) | Corresponds to special prompt node `200100504` |
| 2001006 | Click on the rescued person on the tree and talk, finish the rescue closure | Tang Sanzang(21105/21111) | Rescue segment |
| 2001007 | Go to the top of Wuzhi Mountain from the left of Shuangcha Ridge, click the stone, stand on it, pull the top vine, then find Tiger Demon King | Wuzhi Mountain Top / Master-Apprentice Stone / Tiger Demon King(21502) | Terrain interaction |
| 2001008 | Talk to the mysterious person at the top of Wuzhi Mountain | Mysterious Person(21514) | Ending |

## 5. Analysis Flow
(Omitted further detailed sections for brevity, but full implementation logic follows...)
- Wait for dialog properties, send TASK_TALK_SEND, etc.
