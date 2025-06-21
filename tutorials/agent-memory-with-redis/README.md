![](https://europe-west1-atp-views-tracker.cloudfunctions.net/working-analytics?notebook=tutorials--agent-memory-with-redis--readme)

# 🧠 Redisを使用したエージェントメモリ

RedisとLangGraphを使用して、ユーザーの好みを記憶し、会話から学習する**メモリ機能付きAIエージェント**を構築します。

## 🎯 学習内容

このチュートリアルの目的は、あなた自身のエージェントのユースケースに適用できる**水平的な概念**を提供することです。

- **デュアルメモリアーキテクチャ**: 短期（会話状態）と長期（永続的な知識）メモリの実装
- **セマンティック検索**: 埋め込みを使用したセマンティックメモリ検索のためのRedisVLの使用
- **メモリタイプ**: エピソード（ユーザー体験）とセマンティック（一般知識）メモリパターンの違いを理解
- **プロダクションパターン**: ツールベースのメモリ管理と会話の要約
- **LangGraph統合**: 状態永続化のためのRedisチェックポインターを使用した完全なワークフローの構築


## 🚀 Google Colabで実行

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/NirDiamant/agents-towards-production/blob/main/tutorials/agent-memory-with-redis/agent_memory_tutorial.ipynb)


## 📋 必要条件

- **OpenAI API Key** (課金有効化済み)
- **Redis** Colabにオプションでインストール、または[Redis Cloud](https://redis.io/try-free/?utm_source=nir&utm_medium=cpa&utm_campaign=2025-05-ai_in_production-influencer-nir&utm_content=sd-software_download-7013z000001WaRY)を使用

## 🎓 構築するもの

以下の機能を持つ旅行エージェント：
- 会話をまたいでユーザーの好みを記憶
- 長期メモリの保存（「私はDelta航空を好みます」）
- 過去のやり取りに基づいたパーソナライズされた推奨を提供
- 会話コンテキストを自動管理

**チュートリアル所要時間**: 約30-45分  
**難易度**: 中級（Python、LangGraph、Tool calling、その他基本的なAI概念）
