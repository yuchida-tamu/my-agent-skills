# My Agent Skills

A Claude Code plugin marketplace — a collection of plugins and skills for AI-powered automation.

## Installation

Add this marketplace to Claude Code:

```
/plugin marketplace add yuchida-tamu/my-agent-skills
```

Then install any plugin:

```
/plugin install tech-trend-digest@yuchida-agent-skills
/plugin install english-coach@yuchida-agent-skills
/plugin install claude-office-visualizer@yuchida-agent-skills
```

## Plugins

### [tech-trend-digest](./tech-trend-digest/)

Curates personalized tech news digests from authoritative sources with bilingual support (EN/JP). Topic filtering, freshness validation, and deduplication across runs.

```
/plugin install tech-trend-digest@yuchida-agent-skills
```

### [english-coach](./english-coach/)

Passive English language coaching that runs in every conversation. Corrects grammar, suggests natural phrasing, expands vocabulary, and improves business English — all appended at the end of each response.

```
/plugin install english-coach@yuchida-agent-skills
```

### [claude-office-visualizer](https://github.com/yuchida-tamu/claude-office-visualizer)

Real-time 3D visualization of Claude Code agent orchestration. Watch your AI agents work in an interactive office scene powered by Three.js.

This plugin requires the server to be installed separately via npm:

```bash
# 1. Install the server + CLI
npm install -g claude-office-visualizer

# 2. Install the plugin hooks
/plugin install claude-office-visualizer@yuchida-agent-skills

# 3. Start the visualizer and use Claude Code
claude-visualizer start
```

See the [claude-office-visualizer repo](https://github.com/yuchida-tamu/claude-office-visualizer) for full documentation.

---

# My Agent Skills (日本語)

Claude Codeプラグインマーケットプレイス — AI自動化のためのプラグイン・スキル集です。

## インストール

Claude Codeにマーケットプレイスを追加：

```
/plugin marketplace add yuchida-tamu/my-agent-skills
```

プラグインをインストール：

```
/plugin install tech-trend-digest@yuchida-agent-skills
/plugin install english-coach@yuchida-agent-skills
/plugin install claude-office-visualizer@yuchida-agent-skills
```

## プラグイン

### [tech-trend-digest](./tech-trend-digest/)

信頼性の高いソースからパーソナライズされたテックニュースダイジェストを作成。バイリンガル対応（EN/JP）、トピックフィルタリング、鮮度検証、重複排除機能付き。

```
/plugin install tech-trend-digest@yuchida-agent-skills
```

### [english-coach](./english-coach/)

会話ごとにパッシブに動作する英語コーチング。文法修正、自然な言い回しの提案、語彙拡張、ビジネス英語の改善をレスポンスの末尾に追加します。

```
/plugin install english-coach@yuchida-agent-skills
```

### [claude-office-visualizer](https://github.com/yuchida-tamu/claude-office-visualizer)

Claude Codeエージェントオーケストレーションのリアルタイム3D可視化。Three.jsを使ったインタラクティブなオフィスシーンでAIエージェントの動作を観察できます。

このプラグインはnpmでサーバーを別途インストールする必要があります：

```bash
# 1. サーバー + CLIをインストール
npm install -g claude-office-visualizer

# 2. プラグインフックをインストール
/plugin install claude-office-visualizer@yuchida-agent-skills

# 3. ビジュアライザーを起動してClaude Codeを使用
claude-visualizer start
```

詳細は[claude-office-visualizerリポジトリ](https://github.com/yuchida-tamu/claude-office-visualizer)を参照してください。
