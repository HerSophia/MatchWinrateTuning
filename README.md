# MatchWinrate Tuning —Fairer Quick-Sim Winrates / 更合理的快速模拟胜率

> Version 1.0.0 ｜ Target game / 适配游戏：Esports Manager 2026 Steam retail patch 1.0.1 (Unity 6000.3.12f1 / IL2CPP metadata v39) ｜ License: MIT

**Language / 语言**: [English](#english) ｜ [中文](#中文)

---

<a name="english"></a>

## English

### Table of Contents

- [What this mod does](#what-this-mod-does)
- [Prerequisite](#prerequisite)
- [How to install](#how-to-install)
- [Configuration](#configuration)
- [Compatibility](#compatibility)

Match Winrate Tuning fixes winrates in quick simulation and auto simulation: the game presses the probability computed from team power into a narrow range, so a top team playing a third-tier team still often loses an upset, forcing the player to run detailed simulation match by match.

### What this mod does

This mod attaches to the game's single entry point for computing Team1's win probability and recomputes the winrate from the two teams' actual power, instead of amplifying an already-narrowed probability afterward. The larger the power gap, the more decisive the result, while still leaving some room for upsets.

It distinguishes two cases, each with its own model:

- Matches the player is in: a top team versus a third-tier team can exceed the original roughly 80% ceiling, while keeping 2% of theoretical upset room.
- Pure AI versus AI matches: removes the original 75% hard ceiling so strength shows through in the league, while keeping 5% of upset room.

Classification follows the game's own criteria strictly (comparing the player team ID with the two match team IDs). If either ID is missing, the two IDs are the same, or a read fails, it keeps the game's original probability entirely, without misjudging or rewriting on its own.

Changing this probability affects both the pre-match display and the quick-sim result. It writes no save, does not change economy or constants, and does not rebuild the RNG.

### Prerequisite

This mod requires the **EM.Framework** shared framework to be installed first. Without it, this mod cannot load.

### How to install

1. Make sure BepInEx is installed and the game starts normally.
2. Install EM.Framework first: copy the whole `EM.Framework/` folder into the game's `BepInEx/plugins/`.
3. Copy this mod's `MatchWinrateTuning/` folder into `BepInEx/plugins/`. It contains only `MatchWinrateTuning.dll`.
4. Start the game. In `BepInEx/LogOutput.log` you should see EM.Framework load first, then this mod load and print a ready line.

After installation, `BepInEx/plugins/` looks roughly like this:

```text
plugins/
  EM.Framework/         the shared framework (prerequisite)
  MatchWinrateTuning/   this mod (only MatchWinrateTuning.dll)
```

### Configuration

The config file `BepInEx/config/com.esportsmanager.modkit.quicksimwinrate.cfg`is created afterthe first run. Edit it and restart the game to apply.

The config file keeps the old name on purpose, as a compatibility identifier, so that settings carry over when upgrading from an older version and no second config file is created.

| Section | Key | Default | Meaning |
| --- | --- | --- | --- |
| `General` | `Enabled` | `true` | Master switch. When false, keeps the game's original probability entirely. |
| `Model` | `Scale` | `0.1` | Power scale for player matches. Larger means the power gap decides more. |
| `Model` | `Floor` | `0.02` | Floor probability for the weaker side in player matches. |
| `Model` | `Ceil` | `0.98` | Ceiling probability for the stronger side in player matches. |
| `AI vs AI` | `Enabled` | `true` | Whether to take over pure AI versus AI. When false, such matches keep the original range. |
| `AI vs AI` | `Scale` | `0.1` | Power scale for the AI league. |
| `AI vs AI` | `Floor` | `0.05` | Floor probability for the weaker side in the AI league. |
| `AI vs AI` | `Ceil` | `0.95` | Ceiling probability for the stronger side in the AI league. |
| `Diagnostics` | `LogFirstN` | `30` | Log the classification and rewrite for the first N player matches after start. 0 disables. |
| `Diagnostics` | `LogFirstAiVsAiN` | `20` | Log the first N AI versus AI matches, for verification. 0 disables. |

Each model requires `Scale` to be a finite positive number no greater than 1, `Floor` between 0 and 0.5, and `Ceil` equal to 1 minus `Floor`. If one model's config is invalid, that model is disabled individually and keeps the original probability, without silently swapping the bounds.

Tuning tips:

- To make the AI league steadier: raise `AI vs AI/Ceil` or raise `Scale`.
- To allow more upsets in the AI league: lower `AI vs AI/Ceil` and raise `Floor` (keep them complementary).
- To change only your own quick sim and leave the league alone: set `AI vs AI/Enabled=false`.
- At any time, `General/Enabled=false` reverts to the original in one step.

### Compatibility

- This mod targets a specific game version: Unity 6000.3.12f1, IL2CPP metadata v39.
- On load it runs a compatibility self-check (Unity version + `global-metadata.dat` hash). On a mismatch it warns only and applies no patch.
- All hooks are resolved by name; after a game update, confirm compatibility before enabling.

---

<a name="中文"></a>

## 中文

### 目录

- [这个 Mod 做什么](#这个-mod-做什么)
- [前置依赖](#前置依赖)
- [如何安装](#如何安装)
- [配置](#配置)
- [兼容性](#兼容性)

Match Winrate Tuning 解决快速模拟和自动模拟里的胜率问题：游戏原本把按队伍实力算出的概率硬压在一个较窄的范围内，导致一线队打三线队仍然经常爆冷，玩家不得不进详细模拟一场场手动打。

### 这个 Mod 做什么

本 Mod 挂在游戏计算 Team1 获胜概率的唯一入口上，按两队实际实力重新计算胜率，而不是在已经被压窄的概率上事后放大。实力差越大，结果越确定，同时仍保留一定的冷门空间。

它区分两种情况，分别用一套模型：

- 玩家参与的比赛：让一线打三线能突破原版约 80% 的上限，同时保留 2% 的理论冷门空间。
- 纯 AI 对 AI 的比赛：解除原版 75% 的硬上限，让强弱在联赛里真实体现，同时保留 5% 的冷门空间。

分类严格按游戏自己的口径判断（比较玩家队 ID 与比赛双方 ID）。任何一方 ID 缺失、两边 ID 相同或读取异常时，一律完整保留游戏原始概率，不会误判、不会擅自改写。

改写这个概率会同时影响赛前显示和快速模拟结果。它不写存档，不改经济或常量，不重造随机数。

### 前置依赖

本 Mod需要先安装 **EM.Framework** 共享框架。没有它，本 Mod 无法加载。

### 如何安装

1. 确认已安装 BepInEx，且游戏能正常启动。
2. 先安装 EM.Framework：把 `EM.Framework/` 目录整体拷进游戏的 `BepInEx/plugins/`。
3. 把本 Mod 的 `MatchWinrateTuning/` 目录拷进 `BepInEx/plugins/`。这个目录里只有 `MatchWinrateTuning.dll` 一个文件。
4. 启动游戏。查看 `BepInEx/LogOutput.log`，应能看到 EM.Framework 先加载，本 Mod 随后加载并打出就绪日志。

安装后 `BepInEx/plugins/` 下的结构大致如此：

```text
plugins/
  EM.Framework/         共享框架（前置依赖）
  MatchWinrateTuning/   本 Mod（只含 MatchWinrateTuning.dll）
```

### 配置

首次运行后会生成配置文件 `BepInEx/config/com.esportsmanager.modkit.quicksimwinrate.cfg`。改完重启游戏生效。

配置文件名沿用旧名称是有意保留的兼容标识，这样从旧版本升级过来时已有设置会直接保留，不会生成第二份配置文件。

| 段 | 键 | 默认 | 含义 |
| --- | --- | ---: | --- |
| `General` | `Enabled` | `true` | 总开关。false 时完全保留游戏原始概率。 |
| `Model` | `Scale` | `0.1` | 玩家参与对局的实力尺度。越大，实力差越决定结果。 |
| `Model` | `Floor` | `0.02` | 玩家参与对局的弱队保底概率。 |
| `Model` | `Ceil` | `0.98` | 玩家参与对局的强队封顶概率。 |
| `AIvs AI` | `Enabled` | `true` | 是否接管纯 AI 对 AI。false 时该类比赛完整保留原版范围。 |
| `AI vs AI` | `Scale` | `0.1` | AI 联赛的实力尺度。 |
| `AI vs AI` | `Floor` | `0.05` | AI 联赛的弱队保底概率。 |
| `AI vs AI` | `Ceil` | `0.95` | AI 联赛的强队封顶概率。 |
| `Diagnostics` | `LogFirstN` | `30` | 记录启动后前 N 次玩家对局的分类与改写。0 关闭。 |
| `Diagnostics` | `LogFirstAiVsAiN` | `20` | 记录前 N 次 AI 对 AI 对局，用于验证。0 关闭。 |

每套模型要求 `Scale` 为有限正数且不超过 1、`Floor` 在 0 到 0.5 之间、`Ceil` 等于 1 减 `Floor`。某一套配置不合法时，该套会被单独禁用并原样保留原始概率，不会静默交换上下限。

调参建议：

- 想让 AI 联赛更稳：提高 `AI vs AI/Ceil` 或提高 `Scale`。
- 想让 AI 联赛更多冷门：降低 `AI vs AI/Ceil`、提高 `Floor`（保持两者互补）。
- 只想改自己队的快速模拟、不动联赛：设 `AI vs AI/Enabled=false`。
- 任何时候可用 `General/Enabled=false` 一键回退原版。

### 兼容性

- 本 Mod 只针对特定游戏版本：Unity 6000.3.12f1，IL2CPP metadata v39。
- 加载时会做兼容自检（Unity 版本 + `global-metadata.dat` 哈希）。不匹配时只警告、不打补丁。
- 所有钩子按名字解析，游戏更新后应先确认兼容再启用。
