# Work On Tasks Plugin

A task orchestrator skill that reviews a GitHub project board, selects the next unblocked high-priority issues, spawns coding agents to execute them (in parallel where safe), and opens PRs that trigger automated code review.

## What it does

- Lists open issues, open PRs, and recently closed issues via `gh`
- Filters to the current milestone, skips blocked tasks, sorts by priority (P0 > P1 > P2 > P3)
- Identifies parallelizable tasks (no overlapping files, no mutual dependencies)
- Spawns coding agents in isolated worktrees with a full context package (CLAUDE.md, ADRs, PRD, glossary, `.memory/`)
- Creates PRs that reference `Closes #N` and posts a `@claude` review comment to trigger the review workflow

## Usage

Invoke by saying `/work-on-tasks`, "work on the next tasks", "pick up work", or "continue building".

## Project-specific notes

This skill is currently hard-coded to the `yuchida-tamu/basketball-sim-game` repository and references project conventions (`docs/PRD.md`, `docs/glossary.md`, `docs/adr/`, `.memory/`, `npm run check`, the `claude.yml` GitHub Action). If you install it for a different project, edit `skills/work-on-tasks/SKILL.md` to point at your repo and your own conventions.

---

# Work On Tasks プラグイン (日本語)

GitHub プロジェクトボードをレビューし、優先度と依存関係に基づいて次のブロックされていないタスクを選び、コーディングエージェントを（安全な場合は並列で）起動して実行し、自動コードレビューをトリガーする PR を作成するタスクオーケストレーターです。

## 機能

- `gh` コマンドでオープンな issue・PR・最近クローズされた issue を取得
- 現在のマイルストーンに絞り込み、ブロック済みタスクをスキップし、優先度 (P0 > P1 > P2 > P3) でソート
- 並列実行可能なタスク（ファイル重複なし・相互依存なし）を特定
- 完全なコンテキストパッケージ（CLAUDE.md・ADR・PRD・用語集・`.memory/`）を持たせてエージェントを分離ワークツリーで起動
- `Closes #N` を含む PR を作成し、`@claude` レビューコメントを投稿してレビューワークフローを起動

## 使い方

`/work-on-tasks`、「次のタスクを進めて」、「作業を引き継いで」などと話しかけると起動します。

## プロジェクト固有の注意

このスキルは現状 `yuchida-tamu/basketball-sim-game` リポジトリにハードコードされており、プロジェクト固有のファイル (`docs/PRD.md`・`docs/glossary.md`・`docs/adr/`・`.memory/`・`npm run check`・`claude.yml` GitHub Action) を参照しています。別プロジェクトで使う場合は `skills/work-on-tasks/SKILL.md` を自分のリポジトリ名と規約に合わせて編集してください。
