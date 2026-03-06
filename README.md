# workflows

orleans-house プロジェクト共通の GitHub Actions。

## discord-notify

CI 結果を Discord に Webhook で通知する Composite Action。

### セットアップ

1. Discord チャンネルで Webhook URL を作成
2. 対象リポジトリの Settings → Secrets and variables → Actions → New repository secret で `DISCORD_WEBHOOK` を登録

### 使い方

CI ワークフローの最後のステップに追加:

```yaml
      - uses: orleans-house/workflows/discord-notify@main
        env:
          DISCORD_WEBHOOK: ${{ secrets.DISCORD_WEBHOOK }}
```

### メッセージのカスタマイズ

`message` input でプレフィックスを変更できる:

```yaml
      - uses: orleans-house/workflows/discord-notify@main
        with:
          message: "🚀 Tests passed! @team Review:"
        env:
          DISCORD_WEBHOOK: ${{ secrets.DISCORD_WEBHOOK }}
```

デフォルト: `✅ CI Passed! @saya Please review:`

### 動作

- PR イベント時: メッセージ + PR URL を通知
- push イベント時: メッセージ + Actions URL を通知
- `DISCORD_WEBHOOK` が未設定の場合はスキップ（エラーにならない）

### 入力

| 名前 | 必須 | デフォルト | 説明 |
|------|------|-----------|------|
| `message` | No | `✅ CI Passed! @saya Please review:` | 通知メッセージのプレフィックス |

### 環境変数

| 名前 | 必須 | 説明 |
|------|------|------|
| `DISCORD_WEBHOOK` | Yes | Discord Webhook URL（リポジトリシークレットから渡す） |

### 完全な使用例

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm test
      - uses: orleans-house/workflows/discord-notify@main
        env:
          DISCORD_WEBHOOK: ${{ secrets.DISCORD_WEBHOOK }}
```
