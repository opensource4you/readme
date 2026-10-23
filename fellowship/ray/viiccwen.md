# viiccwen

- GitHub: https://github.com/viiccwen
- Slack: https://opensource4you.slack.com/team/U099N3SPB34

## 2026-10-19

Nominator: dancingactor, Suggested level: 30k

- [Contribution](https://github.com/opensource4you/readme/blob/main/fellowship/ray/fellow-contribution/viiccwen/September/report.md)
- Ray Data: [merged PRs](https://github.com/ray-project/ray/pulls?q=is%3Apr+author%3Aviiccwen+is%3Amerged+merged%3A%3C%3D2026-10-19), [filed issues](https://github.com/ray-project/ray/issues?q=is%3Aissue+author%3Aviiccwen+created%3A%3C%3D2026-10-19)
- Made shuffle tasks up to 12.84x faster by pinning the fused map function, so large UDF closures are no longer serialized again for every shuffle task ([#65480](https://github.com/ray-project/ray/pull/65480))
- Exported task peak USS and logical memory metrics with Grafana charts to help users avoid OOM ([#65666](https://github.com/ray-project/ray/pull/65666), [#65750](https://github.com/ray-project/ray/pull/65750), [#66044](https://github.com/ray-project/ray/pull/66044))
- Added an OOM warning with a suggested `memory` value when the configured memory is below 1.25x max USS ([#65512](https://github.com/ray-project/ray/pull/65512)). During review, proposed a forced detection pass at execution end so short jobs are not missed, while keeping issue indicators from earlier passes.
- Made OOM warning messages easier to read and removed unused issue detector metrics ([#66040](https://github.com/ray-project/ray/pull/66040), [#65716](https://github.com/ray-project/ray/pull/65716))
- Updated the OOM prevention docs ([#65509](https://github.com/ray-project/ray/pull/65509))
- Found, reported and fixed silent data loss in hash-shuffle (V2) anti joins, where rows were dropped when the other side was empty and had an unknown schema ([#66126](https://github.com/ray-project/ray/pull/66126))
- Propagated labels to SHUFFLE_V2 map/reduce tasks ([#66936](https://github.com/ray-project/ray/pull/66936))
- Reviewed other contributors' PRs: pointed out that the JSON fallback path misses `UnicodeDecodeError` ([#66878](https://github.com/ray-project/ray/pull/66878)), and suggested doc accuracy fixes on USS metrics ([#66909](https://github.com/ray-project/ray/pull/66909))

### Bonus reason

Within two months of first contributing, found and fixed a silent data loss bug and delivered the memory observability series end to end.