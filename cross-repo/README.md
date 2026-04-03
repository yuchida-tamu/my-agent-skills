# cross-repo

Work on sibling repositories while staying in your home repo — compounding accumulated context into every task.

## What it does

This plugin spawns an agent to work on a sibling repository, injecting your home repo's accumulated knowledge (CLAUDE.md, memory files, conventions) into the agent's context. This means work on the sibling repo benefits from everything you've built up in your home repo.

**Why this matters:** Your home repo accumulates institutional knowledge over time — project conventions, user preferences, architectural decisions, and memory files. Without this plugin, switching to a sibling repo means starting from scratch. With it, all that context compounds across repos.

## Usage

```
/cross-repo ../other-repo fix the login bug
```

```
/cross-repo refactor the auth module in ../my-app
```

```
/cross-repo add error handling to all API routes in /Users/me/code/backend
```

The command accepts natural language. Just mention the repo path and describe the task — they can appear in any order.

## How it works

1. Parses your natural language input to extract the repo path and task
2. Validates the sibling repo path exists
3. Reads your home repo's CLAUDE.md and all memory files
4. Spawns an agent with the task + your home repo context injected
5. The agent works on the sibling repo directly (reads, edits, creates files)
6. Reports back a summary of everything it changed

## Installation

```
/plugin marketplace add yuchida-tamu/my-agent-skills
/plugin install cross-repo@yuchida-agent-skills
```

---

# cross-repo (日本語)

ホームリポジトリに留まりながら、兄弟リポジトリで作業できるプラグインです。蓄積されたコンテキストをすべてのタスクに活用します。

## 概要

このプラグインは、兄弟リポジトリで作業するためのエージェントを起動し、ホームリポジトリの蓄積された知識（CLAUDE.md、メモリファイル、コーディング規約など）をエージェントのコンテキストに注入します。

**なぜ重要か：** ホームリポジトリには、プロジェクトの規約、ユーザーの好み、設計上の決定、メモリファイルなど、時間とともに蓄積された知識があります。このプラグインがなければ、兄弟リポジトリに切り替えるたびにゼロからのスタートになります。このプラグインがあれば、すべてのコンテキストがリポジトリを横断して複利的に効いてきます。

## 使い方

```
/cross-repo ../other-repo ログインのバグを修正して
```

```
/cross-repo ../my-app の認証モジュールをリファクタリングして
```

自然言語で入力できます。リポジトリのパスとタスクの説明を含めるだけで、順序は問いません。

## 仕組み

1. 自然言語の入力からリポジトリパスとタスクを抽出
2. 兄弟リポジトリのパスが存在するか検証
3. ホームリポジトリのCLAUDE.mdとすべてのメモリファイルを読み込み
4. タスク + ホームリポジトリのコンテキストを注入したエージェントを起動
5. エージェントが兄弟リポジトリで直接作業（読み取り、編集、ファイル作成）
6. 変更内容のサマリーを報告

## インストール

```
/plugin marketplace add yuchida-tamu/my-agent-skills
/plugin install cross-repo@yuchida-agent-skills
```
