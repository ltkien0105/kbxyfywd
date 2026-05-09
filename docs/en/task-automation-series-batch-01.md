# Task Automation Series Batch Record (Batch 1)

## 1. Batch Principles

This batch only records what can currently be directly confirmed from `data/taskinfo.xml`, `TaskInfoXMLParser`, `TaskXMLParser`, and the existing AS3/Hook.

- Group strictly by "one series block" at a time.
- Only write validated `taskID` / `subtaskID` / `after` / `before` / `targets`.
- When the theme name and the task root name differ, both should be recorded.
- Parts without evidence are uniformly marked as `To Be Confirmed`.

## 2. Current Batch Analysis Flow

This batch is not just "copying task names", but broken down using the same flow:

1. First locate the target `taskID` in `data/taskinfo.xml`.
2. Read its `subtaskID` chain and `describe` to confirm if it's a single step, short chain, or long chain.
3. Check `targets` and `after` to determine if it's a mainline, branch, plot block, or theme placeholder.
4. Go back to `TaskArchivesVersion3` and related AS3 to confirm which theme it falls under in the UI.
5. Use `TaskXMLParser` to determine if there are dialogs, choices, battles, scene switches, or animation closures.
   - And record the corresponding packet source, send order, and return packet point.
6. If obvious special packets or special recovery points appear, split them into a separate rule.

In principle, this batch only does three things:

- Confirm task block boundaries
- Explicitly mark execution order
- Mark unconfirmed parts

## 3. Magic Crystal Series

The "Magic Crystal Series" here refers to the associated chains categorized under the `Magic Crystal War` theme in `TaskArchivesVersion3`, as well as task blocks in `taskinfo.xml` that revolve around Magic Crystal Stones, Magic Crystal Energy, and the Magic Crystal Compass.

### Validated

- `2002000 Landslide and Earth Crack`, starting at `2002001 Terrible Stone`, `2002002 Magic Crystal Ray`, `2002003 Lost Scripture`
- `2009000 Demon Child of a Mixed World`, confirmed subtasks include `2009001`, `2009002 Demon Child Appears`, `2009003`, `2009004`, `2009005 Bodhisattva's Truth`
- `2014000 Legend of the Soul Stone (Part 1)`, confirmed subtasks include `2014001`, `2014002 Purple Bamboo Forest Puzzle`, `2014003`, `2014004`
- `2015000 Legend of the Soul Stone (Part 2)`, confirmed subtasks include `2015001` through `2015007`
- `3072000 Dark Starfall`, belongs to the follow-up task block related to Magic Crystal Energy
- `7004000 Magic Crystal Compass`, a single-step task block derived from Magic Crystal Energy

### Analysis Path

- First read the starting point of the "Magic Crystal Stone Discovery" from `2002000`, then see if its description and subsequent magic crystal tasks share the same storyline.
- `2009000` continues to unfold around magic crystal stones, pseudo-magic crystals, and different scenes, showing it's not simply a dialogue task.
- `2014000` and `2015000` are typical two-part storylines: early foreshadowing and later resolution, which must be viewed as upper and lower chapters.
- `7004000` has only one subtask, so it's suitable to be recorded as a single-step closure task, rather than being forced into a long chain.

### Execution Constraints

- These types of tasks should not be merged just because they "look like magic crystals".
- You must first check if the `taskID` is continuous, then check if the `subtaskID`s can form a practical chain.
- Whenever cross-scene movements, item deliveries, battles, or closure animations appear, you cannot force the same fixed order.
- Nodes like `choose flag="3"`, `battle`, and `flash` in `TaskXMLParser` must be handled individually.

### Current Judgment

The Magic Crystal series is not a single task chain, but multiple task blocks strung into a storyline. In the future, if we continue filling out this series, it is recommended to output them in blocks by `taskID`, rather than trying to write all magic crystal-related tasks at once.

## 4. Earth Core Series

In the current snapshot, the identifiable representative block is:

- `3236000 Earth Core Visitor`
- The subtask is only `3236001`
- `after="2235"`

### Current Status

In the current repo snapshot, this chain appears as a short chain, and `taskinfo.xml` currently only exposes a single subtask segment that can be directly confirmed.

### Analysis Path

- First confirm its root task is `3236000 Earth Core Visitor`.
- Read that it currently only has one subtask `3236001`.
- Check if "Research Institute / Dr. Dingding" in the description shares the same entry point with other upcoming task blocks.
- Since we only see a single-step block right now, treat it as a plot intro and don't prematurely flesh it out into a complete series.

### Writing Rules

- Don't directly write "Earth Core Legend" as the confirmed task name; the current snapshot lacks this exact string.
- Use `Earth Core Visitor` as the validated root block first.
- If we obtain a new `tasktheme.xml` or more `taskinfo.xml` fragments later, we will supplement the theme mapping.

## 5. Kabu Series

In the current snapshot, the directly confirmable Kabu-related blocks are:

- `2016000 Purple Moon Red Spirit`
- `3256000 Code Name: Action Kabu!`
- `7011000 True and False Little Kabu`

### Validated

- `2016000` is not a single-step task. We can at least currently see `2016001 Start Kabu Special Training`, `2016002 Test of Wisdom`, `2016003 Test of Courage`, `2016004 Obtain Purple Moon Red Spirit`.
- `3256000` is currently a short chain, its subtask `3256001` points directly to the Research Institute.
- `7011000` is currently also a single-step block, revolving around the Archives and Bella.

### Analysis Path

- `2016000` needs to be understood in four phases: "opening -> dual tests -> finally get reward". Don't treat it as a simple straight line.
- `3256000` is more like a plot relay point. The focus of the analysis should be on how it connects with previous and subsequent task blocks, rather than how many subtasks it has.
- `7011000` is a standalone short chain, suitable as a "single task sample from a UI theme block".
- Here, the exact wording "Kabu Adventure" (卡布历险) does not directly appear, so it can only be categorized as an unconfirmed higher-level theme.

### Current Status

- The exact phrase "Kabu Adventure" does not appear in the current snapshot.
- Treat it as a higher-level theme placeholder for now, don't force it as the authenticated root name.
- These task blocks should still be split by "plot block", not merged based on verbal impressions.

## 6. How to fill the next batch

The next batch should only cover one series direction. The recommended order is:

1. First complete one `taskID` block.
2. Then fill its prerequisite and successor blocks.
3. Then fill the packet source, send order, and return packet point.
4. Finally, fill the UI/automation closure rules.

## 7. Unified Template

In the future, each series block should be written according to this template:

- Series Name
- Confirmed Root Task
- Subtask Sequence
- Key Scenes
- Key Packets (Source / Send / Return / Recovery Point)
- Interrupt Points
- Recovery Points
- Items to Be Confirmed
