# Exploratory Study of Coding-Agent Traces in GitHub Repositories

**Status:** Independent exploratory project, October 2026

## Research question

How do file-based and commit-based heuristics differ when identifying public traces associated with coding agents in GitHub repositories?

## Method

I selected 59 public repositories from my GitHub starred repositories and ran the published `agent-impact` analysis pipeline on that sample. The run took 1 hour, 54 minutes, and 33 seconds. It produced 56 rows in the main analysis report; 3 repository analyses failed.

This is a small convenience sample selected from my interests, not a random or representative sample of GitHub. The project reuses a published methodology and does not claim to introduce a new detection algorithm.

## Preliminary descriptive results

Among the 56 repositories with rows in the main report:

| Pipeline signal | Repositories with signal set to `true` |
|---|---:|
| `tool_use` | 19/56 (33.9%) |
| `commit_use` | 38/56 (67.9%) |
| `ignored_tools` | 15/56 (26.8%) |
| All three signals set to `false` | 16/56 (28.6%) |
| File-based (`tool_use`) and commit-based signals disagree | 23/56 (41.1%) |

These signals overlap and their counts must not be added. They are heuristic indicators, not independently verified evidence of actual agent use.

A `ratio` value was available for 38 repositories. The median was 11.818%, the minimum was 0.081%, and the maximum was 100%. In the source implementation, commit ratio is computed as AI-classified commits divided by AI- plus Human-classified commits; bot commits are excluded. The reported percentage depends on the pipeline's classification rules and is not a measure of the percentage of code written by AI.

## Limitations

- The sample is small and self-selected.
- Public repository traces are imperfect proxies for actual coding-agent use.
- Absence of a detected trace does not prove that a repository did not use an agent.
- A detected trace does not by itself prove that an agent generated code.
- Three repositories failed during analysis and are listed in `results/failed_repositories.csv`.
- The results have not undergone systematic manual validation.

## Repository contents

- `data/sample_repositories.csv`: selected repository URLs.
- `results/repository_summary.csv`: selected columns from the 56 successful report rows.
- `results/summary.json`: descriptive counts and ratio statistics.
- `results/failed_repositories.csv`: repository URLs and sanitized failure categories.
- `scripts/summarize_results.py`: Python standard-library script to summarize the original pipeline's CSV outputs.

The summary script processes existing output files. It does not rerun collection or independently detect coding agents.

## Reproducing the summary

After running the published pipeline, pass its report and retry-list files to the script:

```bash
python3 scripts/summarize_results.py \
  /path/to/analysis_report_56.csv \
  /path/to/retry_list.csv \
  results
```

## Attribution

This project builds on the methodology and code of:

Robbes, R., Matricon, T., Degueule, T., Hora, A., & Zacchiroli, S. *Agentic Much? Adoption of Coding Agents on GitHub.*

- Paper: https://arxiv.org/abs/2601.18341
- Original implementation: https://github.com/labri-progress/agent-impact
- Published dataset: https://zenodo.org/records/19256968
- DOI: https://doi.org/10.1145/3822180

The original authors' research and code are distinct from this exploratory analysis. The original authors have permitted modification and redistribution for research purposes with appropriate attribution; third-party components and data licenses should still be checked separately.
