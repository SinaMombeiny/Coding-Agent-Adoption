# Coding-Agent Trace Validation

**Status:** Ongoing independent research project — preliminary results only  
**Started:** October 2026

## Research objective

This project investigates the validity and robustness of heuristics that infer coding-agent adoption from public GitHub repository traces. It builds on the published methodology and implementation of Robbes et al., *Agentic Much? Adoption of Coding Agents on GitHub* (ACM TOSEM, 2026).

The long-term objective is to examine when file-based and commit-based signals disagree, audit the public evidence behind selected classifications, and test how detection outcomes change when trace markers are absent or altered in controlled repository copies or test fixtures.

## Research questions

1. How often do file-based and commit-based detection signals disagree in this self-selected repository sample?
2. What public evidence supports the classifications in selected repositories, and where does that evidence remain ambiguous?
3. How sensitive are the detection heuristics to missing or modified trace markers under controlled tests?

Questions 2 and 3 are planned work; they have not yet been completed.

## Phase 1: Initial pipeline run — completed

I selected 59 public repositories from my GitHub starred repositories using the GitHub API and supplied the list to the published `agent-impact` pipeline. The original implementation was run from `code/run.py`; this project did not reimplement the detection pipeline.

The command used was:

```bash
python run.py ../data/sina-starred.csv ../data/sina-starred.csv sina-starred-59 --max-workers 2 --output-dir ../temp
```

No `--analysis-date` argument was supplied. The local `code/run.py` parser sets its default to `2025-01-01`, so that was the configured analysis start date for this run. The analysis end is the runtime timestamp in UTC. The run took 1 hour, 54 minutes, and 33 seconds.

The pipeline wrote 56 rows to `analysis_report_56.csv` and 3 records to `retry_list.csv`. It also wrote separate pull-request and ratio files; their row counts differ and should not be interpreted as the number of successful repository reports.

## Preliminary descriptive results

All counts below are from the 56 rows in `analysis_report_56.csv`.

| Pipeline signal | True | False |
| --- | ---: | ---: |
| `tool_use` | 19 | 37 |
| `commit_use` | 38 | 18 |
| `ignored_tools` | 15 | 41 |

The joint signal combinations were:

| `tool_use` | `commit_use` | `ignored_tools` | Repositories |
| --- | --- | --- | ---: |
| false | false | false | 16 |
| false | true | false | 14 |
| true | true | false | 9 |
| true | true | true | 8 |
| false | true | true | 7 |
| true | false | false | 2 |

The `tool_use` and `commit_use` values disagreed in 23 of the 56 rows: 21 had `tool_use=false` and `commit_use=true`, while 2 had `tool_use=true` and `commit_use=false`.

The `ratio` field had a value in 38 rows. Its median was 11.818%, its minimum was 0.081%, and its maximum was 100%. In the upstream code, this ratio is the percentage of commits classified as AI among commits classified as AI or Human; BOT-classified commits are excluded. It is not a percentage of AI-generated lines or a direct measurement of actual agent use. The source also only assigns the report-level `ratio` when the overall computed value is positive, so a blank value must not be treated automatically as zero.

The pipeline's time windows are defined as follows:

- `ratio`: from the configured analysis start date (`2025-01-01` in this run) to the analysis runtime.
- `adoption_ratio`: from the earliest adoption date inferred by the pipeline to the analysis runtime.
- `recent_ratio`: from the later of the inferred adoption date and 60 days before the runtime to the analysis runtime.

The inferred adoption date is the earliest date detected from available traces, not necessarily the actual date on which a developer first used an agent.

## Interpretation and limitations

These are preliminary descriptions of pipeline outputs, not validated counts of actual coding-agent use. The sample is a convenience sample drawn from my starred repositories and is not representative of GitHub as a whole. Repository traces can be absent even when a coding agent was used, and a trace can appear without proving that the agent generated code. No systematic manual evidence audit has been completed yet.

Three repository analyses failed. The recorded categories include a Git branch-reference check failure for the original `agent-impact` repository, an HTTP 502 response from the GitHub API for `OmniRoute`, and a repository file-list retrieval failure for my personal website repository. These failures are retained as limitations rather than silently excluded.

The disagreement between two signals does not, by itself, establish which signal is more accurate or indicate a false positive. That requires manual review against an explicit evidence protocol.

## Next phase

1. Define an evidence-review protocol and audit the 23 repositories where file-based and commit-based signals disagree, then review a small comparison sample from agreement and no-signal groups.
2. Record the source and strength of the public evidence for each case, marking ambiguous cases instead of forcing a binary judgement.
3. Run controlled trace-resilience tests using repository copies or test fixtures in which selected markers are removed or altered, and record whether the pipeline output changes.
4. Report the manual review outcomes and controlled-test results, including uncertainty and cases that cannot be resolved from public evidence.

This project is ongoing. These next-phase activities have not yet been completed.

## Repository contents

- `data/sample_repositories.csv`: the 59 selected repository URLs.
- `scripts/summarize_results.py`: a helper script that summarizes the upstream pipeline's CSV output. It does not perform coding-agent detection.
- `results/repository_summary.csv`: selected fields from the 56 successful report rows.
- `results/summary.json`: descriptive counts and ratio statistics.
- `results/failed_repositories.csv`: the three failed repository URLs and sanitized error categories.

The summary script was drafted with AI assistance and run against the pipeline output. The original detection code is maintained by the paper's authors and is not authored by this project.

## Summarizing existing results

After running the upstream pipeline, the report can be summarized with:

```bash
python3 scripts/summarize_results.py \
  /path/to/analysis_report_56.csv \
  /path/to/retry_list.csv \
  results
```

This step processes existing CSV output; it does not rerun repository mining or implement detection heuristics.

## Attribution and source code

Robbes, R., Matricon, T., Degueule, T., Hora, A., & Zacchiroli, S. *Agentic Much? Adoption of Coding Agents on GitHub*.

- Paper: https://dl.acm.org/doi/10.1145/3822180
- Original implementation: https://github.com/labri-progress/agent-impact
- Published dataset: https://zenodo.org/records/19256968
- DOI: https://doi.org/10.1145/3822180

The detection methodology and upstream pipeline are credited to the original authors. This repository documents an independent exploratory analysis and the next planned validation steps; it does not claim to introduce a new detection algorithm.
