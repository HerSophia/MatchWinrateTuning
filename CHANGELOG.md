# CHANGELOG — Match Winrate Tuning

版本号遵循语义化版本。适配游戏：Esports Manager 2026 Steam 正版补丁 1.0.1（Unity 6000.3.12f1 / IL2CPP metadata v39）。

## 1.0.0（2026-07-12）

- 首个对外发布版本。
- 挂在游戏计算 Team1 获胜概率的唯一入口上，按两队实际实力重新计算胜率，替代原本被压窄的概率区间。
- 区分玩家参与对局与 AI 对 AI 两种情况，分别用一套模型；实力差越大结果越确定，同时保留一定冷门空间。
- 数学模型经正版 1.0.1 方法体反汇编核实与 GoldBerg 期结论一致，公式与常量零改动。
- 依赖 EM.Framework 共享框架。
