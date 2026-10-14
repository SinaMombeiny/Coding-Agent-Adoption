# Empirical Validation of AI Coding Agent Trace Heuristics on GitHub

An empirical replication and trace-resilience study evaluating file- and commit-level detection heuristics from Robbes et al. ([*Agentic Much? Adoption of Coding Agents on GitHub*, ACM TOSEM 2026](https://doi.org/10.1145/3822180)) against active Q4 2026 open-source repositories.

## Overview & Motivation

Autonomous AI coding agents (e.g., Cursor, Claude Code, Codex) leave distinct artifacts in software repositories, such as configuration files and commit trailers. However, developers increasingly adopt obfuscation practices—such as adding agent rules to `.gitignore`, disabling commit attribution, or cleaning up repository logs.

This project investigates the **precision, recall, and resilience** of existing automated detection heuristics under real-world conditions in **Q4 2026**. By running an open-source evaluation pipeline against recent GitHub repositories, we assess whether agent trace markers remain reliable or if developer obfuscation is degrading automated detection accuracy.

> **RQ:** *How resilient and accurate are commit- and file-level heuristics in detecting AI coding agent adoption in active Q4 2026 GitHub repositories, and to what extent do developer obfuscation practices degrade detection recall?*

## Evaluated Heuristics

Building upon the open replication package by Robbes et al. (2026), this project evaluates two primary trace categories:

### 1. File-Level Heuristics

- **Configuration and instruction artifacts:** e.g., `.cursorrules`, `[suspicious link removed]`, `[suspicious link removed]`, `.clauderc`.
- **Obfuscation signals:** Checking `.gitignore` for hidden agent configuration patterns.

### 2. Commit & Authorship Heuristics

- **Git commit trailers:** e.g., `Co-authored-by: Claude <...>`.
- **Commit metadata:** Author and committer identity patterns.
