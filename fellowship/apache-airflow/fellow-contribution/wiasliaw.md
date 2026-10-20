# wiasliaw

- GitHub: https://github.com/wiasliaw

---

Month: September 2026
Summary: PR: 3 (1 merged, 2 open), Code Review: 0, Issue Discussion: 1, Dev List: 0

PR:
- [Add time range filtering to the Gantt view](https://github.com/apache/airflow/pull/73555) (open, milestone Airflow 3.4.0, 15 files, +558/-24): add start/end date range filters to the Gantt view (API + UI), so clearing a run long after it first ran no longer stretches the time axis over every earlier try and crushes short tasks; closes [#63715](https://github.com/apache/airflow/issues/63715)
- [Keep the URL scheme out of path masking in airflow info --anonymize](https://github.com/apache/airflow/pull/73599) (merged, Airflow 3.3.3, 2 files, +41/-2): fix `airflow info --anonymize` corrupting its report when the OS username is a substring of the URL scheme; only mask what follows the scheme, with a parametrized test
- [Normalize empty project id in credential config file branch](https://github.com/apache/airflow/pull/73886) (open, 2 files, +15): in the Google provider's credential config file branch, normalize a `None` project id to `""` to match the ADC / IdP branches and the declared return type, with a unit test

Issue Discussion:
- [#63715 Add a time range selector for the Gantt view](https://github.com/apache/airflow/issues/63715): laid out the trade-offs between a single time window and per-field range filters, and how the x-axis should clamp, to confirm the design with maintainers
