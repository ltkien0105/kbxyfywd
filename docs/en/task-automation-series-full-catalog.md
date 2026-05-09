# Task Automation Series: Full Catalog

## 1. Introduction

This document contains the full categorical list of task series extracted from `data/taskinfo.xml` and corresponding AS3 parsers.
Its purpose is to provide a master index for all orchestrable task series, allowing analyzers to pick a series, locate its root `taskID`, and establish the baseline for automated scripts.

## 2. Mainline / Core Story Tasks

These tasks represent the main storyline progression. Their complexity is usually high, involving extensive scene switches, NPC dialogues, and battles.

- **Imperial City Crisis (Root ID: 2001000)**
  - Detailed analysis available in: `docs/task-automation-mainline-2001000-huangcheng-weiji.md`
  - Expected Subtask Range: 2001001 ~ 200100X
- **Crystal War (Root ID: TBD)**
  - Status: To be analyzed.
- **Kabu Adventure (Root ID: TBD)**
  - Status: To be analyzed.

## 3. Side & Activity Tasks (Batch 01)

These are standalone or semi-standalone activity series. They have fewer prerequisites and are excellent targets for modular botting/automation.

- **Eight Trigrams Spirit Compass (Root ID: 4006000)**
  - Detailed analysis: Incorporated as the prime example in `docs/task-automation-analysis-guide.md`
- **Guardian Dream (Root ID: 805)**
  - Detailed analysis: `docs/act805-guardian-dream-analysis.md` (Already evaluated for its specific mini-games and item drops)
- **Act 666 KongKong (Root ID: 666)**
  - Detailed analysis: `docs/act666-kongkong-analysis.md`

## 4. Pending / Unmapped Activity Series

A repository of tasks that have been identified in the XML but have not yet undergone standard flow analysis.

- **Earth Core Legend**
- **Spirit Beast Collect**
- **Daily Check-ins and Minigames**
  - Note: These are partially implemented via `activity_minigames.h` but need formal XML-to-Packet mapping verification.
- **Shuangtai Events**
  - Status: Automation existing in `shuangtai.h` but not thoroughly back-documented against XML roots.

## 5. Catalog Maintenance Protocol

When a new task series is requested for automation:
1. Locate its root ID in `taskinfo.xml` and register it in this catalog.
2. Create a dedicated analysis markdown file for that series using the template from `docs/task-automation-analysis-guide.md`.
3. Link the new markdown file back to this master catalog under "Processed".
4. Do NOT attempt to guess the packet flow without running AS3 checks and packet proofs.