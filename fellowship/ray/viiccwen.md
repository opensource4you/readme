# viiccwen

- GitHub: https://github.com/viiccwen
- Slack: https://opensource4you.slack.com/team/U099N3SPB34

## 2026-10-19

Nominator: dancingactor, Suggested level: 30k, 

- [Contribution](https://github.com/opensource4you/readme/blob/main/fellowship/ray/fellow-contribution/viiccwen/September/report.md)
- Merged 10 Ray Data PRs and filed 8 issues in 6 weeks
- Made shuffle tasks up to 12.84x faster by pinning the fused map function, so large UDF closures are no longer serialized again for every shuffle task ([#65480](https://github.com/ray-project/ray/pull/65480))
- Built a memory observability series to help users avoid OOM: exported task peak USS and logical memory metrics with Grafana charts ([#65666](https://github.com/ray-project/ray/pull/65666), [#66044](https://github.com/ray-project/ray/pull/66044)), added a warning with a suggested memory value when it is set below 1.25x max USS ([#65512](https://github.com/ray-project/ray/pull/65512)), and updated the OOM prevention docs ([#65509](https://github.com/ray-project/ray/pull/65509))
- Found, reported and fixed silent data loss in hash-shuffle (V2) anti joins, where rows were dropped when the other side was empty and had an unknown schema ([#66126](https://github.com/ray-project/ray/pull/66126))
- Reviewed other fellows' PRs with suggestions ([#66878](https://github.com/ray-project/ray/pull/66878))
- Merged the most PRs and filed the most issues of all Ray Fellowship participants
- Solid work for an early contributor