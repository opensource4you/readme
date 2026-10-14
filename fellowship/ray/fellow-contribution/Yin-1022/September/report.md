2026-10-11
- Continue fixing the suggestion from PR(#66878)
- Help review a Ray Data PR (#66879) about .union()

2026-10-10
- Continue fixing the suggestion from PR(#66878)
- Help review a Ray Data PR (#66907) about write_deltalake()
- Help review and identity the mistake in one gemini bot's comment on Ray Core PR (#66818)

2026-10-09
- Open an issue(#66877) about an improvement of JSON error parsing
- Open an PR(#66878) for the issue
- Fix the suggestion from Code Review Bots and reviewers

2026-10-07 ~ 2026-10-08
- Found an issue about an improvement of JSON error parsing, which I might be able to contributed to
- Ask if the issue worths contributing to and get permitted.

2026-10-05 ~ 2026-10-06
- Look into file reading sysyem in Ray Data and attempt some comparsion

2026-09-20 ~ 
- Try to find any suitable good-first-issue about Ray Data, but there isn't much.
- Try to read more files in the RAY repo

2026-09-19
- Forked and cloned ray-project/ray
- Configured upstream remote
- Read CONTRIBUTING.rst
- Read AGENTS.md
- Started exploring Ray Data / Serve / RLlib

====
## Total
- 1 Issue/PR
    - **Improve JSON read error context #66878**  
    Improves error messages for malformed JSON and JSONL inputs in ray.data.read_json() by including the offending input file path, which wasn't identified before, while preserving the original parsing diagnostics and adding regression tests for the adjustment.  
    PR: https://github.com/ray-project/ray/pull/66878  
    Issue: https://github.com/ray-project/ray/issues/66877

- 3 Review
    - **<u>[Core] Honor per-task retry policy during actor restart #66818</u>**  
    This PR improves actor task handling during restarts by respecting per-task retry policies, allowing non-retryable tasks to fail immediately while preserving retryable tasks.
    
        I Identified an incorrect code review suggestion regarding absl::flat_hash_map::erase().and verified that the method returns void, confirming that the existing iterator handling was correct and preventing a potential compilation error.

        PR: https://github.com/ray-project/ray/pull/66818  
        Review: https://github.com/ray-project/ray/pull/66818#discussion_r4237514814

    - **<u>[Data] Write Delta timestamps as microseconds in write_delta #66907</u>**  
    This PR improves Delta Lake compatibility by converting timestamps, including nested types, to microsecond precision.

        I tested timestamp conversion overflow handling and suggested regression tests for out-of-range timestamps and timestamp-based map keys. Both suggestions were subsequently addressed by the PR author.

        PR: https://github.com/ray-project/ray/pull/66907  
        Review: https://github.com/ray-project/ray/pull/66907#issuecomment-6095431852

    - **<u>[Data] Push column selections through Union #66879</u>**  
    This PR optimizes Ray Data by pushing column selections through Union operators, enabling earlier column pruning in individual data sources.
    
        I locally verified the optimization using two Parquet inputs and suggested an additional regression test to ensure unused columns are pruned at the reader level, providing a working test example.

        PR: https://github.com/ray-project/ray/pull/66879  
        Review: https://github.com/ray-project/ray/pull/66879#issuecomment-6105377231