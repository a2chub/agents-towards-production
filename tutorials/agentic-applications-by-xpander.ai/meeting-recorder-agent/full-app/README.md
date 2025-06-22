# ミーティングレコーダーエージェント

このディレクトリには、xpander.ai SDKを使用したプロダクション対応のミーティングレコーダーエージェント実装が含まれており、ミーティングの録画と要約を管理できる自律エージェントの構築方法を示しています。

## 概要

ミーティングレコーダーエージェントは、以下によってミーティングワークフローを自動化するよう設計されています：
- Googleカレンダーに接続して今後のミーティングを検索
- ミーティング録画のスケジューリングと開始
- レコーダーのステータス確認とミーティング後のアセット（動画とトランスクリプト）の取得
- ミーティング要約とPDFアジェンダの生成
- ミーティングアセットと要約を参加者にメール送信
- 複数セッション間でのメモリとコンテキストの維持

## ディレクトリ構造

```
meeting-recorder-agent/
├── app.py                      # エージェントのCLIエントリーポイント
├── meeting_recorder_agent.py   # メインエージェント実装
├── xpander_handler.py          # プラットフォームイベントのイベントハンドラー
├── agent_instructions.json     # エージェントペルソナ設定
├── xpander_config.json         # API認証情報設定
├── Dockerfile                  # デプロイ用コンテナ定義
├── providers/
│   ├── ai_frameworks/          # フレームワーク統合
│   └── llms/                   # LLMプロバイダー実装
│       └── openai/             # OpenAI固有の実装
└── tools/
    ├── local_tools.py          # カスタムツール実装
    └── async_function_caller.py # 非同期関数呼び出しユーティリティ
```

## はじめに

### 前提条件

- Python 3.10以上（3.10.0以上）
- xpander CLI用のNode.js
- xpander-sdkとxpander-utils（requirements.txt経由でインストール）
- [xpander.ai](https://xpander.ai/)アカウント
- Googleカレンダー API認証情報（カレンダー統合用）
- OpenAI APIキー（LLM機能用）

### インストール

1. Python仮想環境を作成して有効化：

```bash
# macOS/Linux
python3 -m venv .venv
source .venv/bin/activate

# Windows
python -m venv .venv
.venv\Scripts\activate
```

2. 依存関係をインストール：

```bash
pip install -r requirements.txt
```

### エージェントのセットアップ

xpander.aiでエージェントをセットアップするには2つのオプションがあります：

#### オプション1：テンプレートを使用（推奨）

1. [app.xpander.ai](https://app.xpander.ai)アカウントにログイン
2. 「Agents」内でTemplatesをクリックし、「Meeting Recorder Template」を選択
3. 「Import Template」をクリックしてワークスペースに追加
4. インポート後、AI Agent WorkbenchからSDK Triggerをクリック
5. **Agent ID**と**API Key**をコピー

xpanderワークベンチの使い方について詳しくは[公式ドキュメント](https://docs.xpander.ai/docs/01-get-started/02-getting-started-01-workbench)をご覧ください。

#### オプション2：手動セットアップ

エージェントを手動で構築したい場合：

1. [app.xpander.ai](https://xpander.ai)アカウントにログイン
2. ダッシュボードから「Create New Agent」をクリックし、Plannerステップをスキップ
3. Built-inアクションメニューから以下のツールをエージェントに追加：
   - **Check Recorder Status**ツール
   - **Create Meeting Recording Bot**ツール
   - **Send Email with Content**ツール
4. Google Calendarアプリから以下のツールをエージェントに追加：
   - **Get Calendar Events by ID**ツール
5. エージェントを保存し、SDK Triggerから**Agent ID**と**API Key**をコピー

エージェントにツールを追加する詳細な手順については、[エージェントへのツール追加](https://docs.xpander.ai/docs/02-agent-builder/02-add-tools-to-agents)ドキュメントを参照してください。

3. 環境を設定：
プロジェクトルートに必要なキーと設定を含む`.env`ファイルを作成：

```
OPENAI_API_KEY=your_openai_key
XPANDER_API_KEY=your_xpander_key 
XPANDER_AGENT_ID=your_agent_id
```

## 📚 仕組み

エージェントは2つの主要コンポーネントを使用します：

1. **メインアプリ（`app.py`）**：すべてを調整
2. **ミーティングエージェント（`meeting_agent.py`）**：xpander.aiに接続してエージェントを実行

エージェントは、xpander.aiの組み込みスレッドベースメモリシステムを活用して、会話のコンテキストを維持し、セッション間でミーティングの詳細を記憶します。

### エージェントツール

<table>
<tr>
  <td width="25%" align="center">
    <h4>🔎<br>レコーダーステータス確認</h4>
  </td>
  <td>
    録画ボットのステータスを照会し、録画に関する情報を取得：
    <ul>
      <li>録画が進行中か完了しているかを表示</li>
      <li>動画、音声、トランスクリプトのダウンロードリンクを提供</li>
      <li>期間や参加者などのメタデータを表示</li>
    </ul>
  </td>
</tr>
<tr>
  <td width="25%" align="center">
    <h4>🤖<br>録画ボット作成</h4>
  </td>
  <td>
    Google Meetセッションを録画する新しいボットを作成およびデプロイ：
    <ul>
      <li>任意の形式のGoogle Meet URLを受け入れ</li>
      <li>指定された認証情報を使用して自動的にミーティングに参加</li>
      <li>追跡用の専用レコーダーIDを作成</li>
    </ul>
  </td>
</tr>
<tr>
  <td width="25%" align="center">
    <h4>📧<br>メールコンテンツ送信</h4>
  </td>
  <td>
    ミーティングの要約と録画をメールで送信：
    <ul>
      <li>トランスクリプトの要約をミーティング参加者に送信</li>
      <li>録画ファイルを添付またはリンク</li>
      <li>カスタマイズされたメールテンプレートをサポート</li>
    </ul>
  </td>
</tr>
<tr>
  <td width="25%" align="center">
    <h4>📅<br>カレンダーイベント取得</h4>
  </td>
  <td>
    Googleカレンダーと接続：
    <ul>
      <li>今後および過去のカレンダーイベントを取得</li>
      <li>カレンダーイベントをミーティング録画にリンク</li>
      <li>エージェントのスケジューリング情報を提供</li>
    </ul>
  </td>
</tr>
</table>

### エージェントの実行

#### CLIモード

まずxpander.aiにログイン
```bash
xpander login
```

対話型コマンドラインモードでエージェントを実行：

```bash
python app.py
```

これにより、エージェントと直接対話できる会話が開始されます。

出力例：
```
2025-05-27 21:15:18.436 | INFO     | meeting_recorder_agent:chat:80 - 🧠 新しいスレッドにタスクを追加
2025-05-27 21:15:21.590 | INFO     | meeting_recorder_agent:_agent_loop:115 - 🪄 エージェントループ開始
2025-05-27 21:15:25.275 | INFO     | meeting_recorder_agent:_agent_loop:121 - --------------------------------------------------------------------------------
2025-05-27 21:15:25.276 | INFO     | meeting_recorder_agent:_agent_loop:122 - 🔍 ステップ 1
2025-05-27 21:15:29.686 | INFO     | providers.llms.openai.async_client:invoke_model:87 - 🔄 モデル応答を3.78秒で受信
2025-05-27 21:15:29.687 | INFO     | providers.llms.openai.async_client:invoke_model:93 - 🔄 ツール呼び出し関数名: xpfinish-agent-execution-finished
2025-05-27 21:15:34.801 | INFO     | meeting_recorder_agent:_agent_loop:179 - ✅ xpfinish-agent-execution-finished
2025-05-27 21:15:34.802 | INFO     | meeting_recorder_agent:_agent_loop:181 - 🔢 ステップ1使用トークン: 2436 (出力: 144, 入力: 2292)
2025-05-27 21:15:36.571 | INFO     | meeting_recorder_agent:_agent_loop:187 - ✨ 実行時間: 14.98秒
2025-05-27 21:15:36.573 | INFO     | meeting_recorder_agent:_agent_loop:190 - 🔢 総使用トークン: 2436 (出力: 144, 入力: 2292)
2025-05-27 21:15:37.113 | INFO     | meeting_recorder_agent:chat:84 - --------------------------------------------------------------------------------
2025-05-27 21:15:37.114 | INFO     | meeting_recorder_agent:chat:85 - 🤖 エージェント応答: こんにちは！私ができることをいくつか紹介します：

- ミーティングレコーダーボットを作成してビデオミーティングを録画し、録画ステータスとアセットを提供します。
- カレンダーからイベントを取得・管理し、メールで通知や要約を送信することも含みます。
- ミーティングリストから週次ミーティングアジェンダをPDFとして生成・エクスポートします。
- カスタムコンテンツを含むメール通知やアラートを送信します。

特定のタスクがある場合は、お知らせください。お手伝いします！
あなた: 
```

#### イベント駆動モード

xpander.aiプラットフォームからのイベントを処理するイベント駆動モードでエージェントを実行：

```bash
python xpander_handler.py
```

正しく実行されると、エージェントが開始され、xpander.aiプラットフォームからの着信イベントを待機します。イベントが受信されない限り、すぐには出力されません。

注意：システムがpythonコマンドを認識しない場合は、python3を使用してください：

```bash
python3 xpander_handler.py
```

## 使用例

### カレンダー統合の追加

このエージェントは、接続されたGoogleカレンダーから今後のミーティングを取得するよう設定されています。スケジュールを調べて、各ミーティングの主要な詳細を含めるよう依頼できます。

エージェントとの会話中に、次のようなメッセージを送信してみてください：
```
<日付>とその後の3日間の今後のミーティングをリストし、各ミーティングについて以下を含めてください：タイトル、説明（利用可能な場合）、場所、時間、参加者
```
エージェントはリクエストを処理し、カレンダーツールを呼び出し、リクエストしたすべての詳細を含む整形されたミーティングリストを返します。

出力例：
```
2025-05-27 21:28:04.970 | INFO     | meeting_recorder_agent:chat:77 - 🧠 既存スレッドにタスクを追加: fb6b4ed0-d39e-4129-ad59-d2f508f29db1
2025-05-27 21:28:09.065 | INFO     | meeting_recorder_agent:_agent_loop:115 - 🪄 エージェントループ開始
2025-05-27 21:28:10.209 | INFO     | meeting_recorder_agent:_agent_loop:121 - --------------------------------------------------------------------------------
2025-05-27 21:28:10.209 | INFO     | meeting_recorder_agent:_agent_loop:122 - 🔍 ステップ 1
2025-05-27 21:28:12.225 | INFO     | providers.llms.openai.async_client:invoke_model:87 - 🔄 モデル応答を1.40秒で受信
2025-05-27 21:28:12.225 | INFO     | providers.llms.openai.async_client:invoke_model:93 - 🔄 ツール呼び出し関数名: CalendarEventManagementGetCalendarEventsById
2025-05-27 21:28:20.334 | INFO     | meeting_recorder_agent:_agent_loop:179 - ✅ CalendarEventManagementGetCalendarEventsById
2025-05-27 21:28:20.334 | INFO     | meeting_recorder_agent:_agent_loop:181 - 🔢 ステップ1使用トークン: 2400 (出力: 67, 入力: 2333)
2025-05-27 21:28:21.421 | INFO     | meeting_recorder_agent:_agent_loop:121 - --------------------------------------------------------------------------------
2025-05-27 21:28:21.422 | INFO     | meeting_recorder_agent:_agent_loop:122 - 🔍 ステップ 2
2025-05-27 21:28:25.683 | INFO     | providers.llms.openai.async_client:invoke_model:87 - 🔄 モデル応答を3.68秒で受信
2025-05-27 21:28:25.684 | INFO     | providers.llms.openai.async_client:invoke_model:93 - 🔄 ツール呼び出し関数名: xpfinish-agent-execution-finished
2025-05-27 21:28:32.740 | INFO     | meeting_recorder_agent:_agent_loop:179 - ✅ xpfinish-agent-execution-finished
2025-05-27 21:28:32.741 | INFO     | meeting_recorder_agent:_agent_loop:181 - 🔢 ステップ2使用トークン: 6013 (出力: 337, 入力: 5676)
2025-05-27 21:28:34.478 | INFO     | meeting_recorder_agent:_agent_loop:187 - ✨ 実行時間: 25.41秒
2025-05-27 21:28:34.480 | INFO     | meeting_recorder_agent:_agent_loop:190 - 🔢 総使用トークン: 8413 (出力: 404, 入力: 8009)
2025-05-27 21:28:35.020 | INFO     | meeting_recorder_agent:chat:84 - --------------------------------------------------------------------------------
2025-05-27 21:28:35.021 | INFO     | meeting_recorder_agent:chat:85 - 🤖 エージェント応答: Googleカレンダーから次の3日間（2025年5月27日〜29日）のミーティングをお知らせします：

---

**1. xpanderへのオンボーディング**
- **日付:** 2025年5月27日
- **時間:** 15:30–16:00 (Asia/Jerusalem)
- **場所:** Google Meetリンク (https://meet.google.com/ ....)
- **参加者:** Daniel、Or、David

**2. AWS Summit Tel Aviv**
- **日付:** 2025年5月28日
- **時間:** 08:00–17:30 (Asia/Jerusalem)
- **場所:** EXPO Tel Aviv、パビリオン1、101 Rokach Blvd
---

詳細が必要な場合やPDFとして必要な場合はお知らせください！
```

### ミーティングレコーダーボットの作成

このエージェントは、指定されたGoogle Meet URLのミーティングレコーダーボットを作成するよう設定されています。特定のミーティングのレコーダーを作成するよう依頼でき、それを実行します。

エージェントとの会話中に、次のようなメッセージを送信してみてください：
```
以下のGoogle Meet URLのミーティングレコーダーボットを作成してください：https://meet.google.com/okf-ntry-xtg
```
または：
```
<ミーティングタイトル>のレコーダーを作成してください。
```

レコーダーのステータスを確認：

```
レコーダーのステータスを確認し、完了していればアセットのリンクを教えてください。
```

### ミーティング要約の生成とメール送信

```
動画とトランスクリプトを要約とともに<あなたのメールアドレス>にメールしてください
```

## エージェント機能

ミーティングレコーダーエージェントは、いくつかの主要な機能を示しています：

- **フレームワーク非依存設計**：特定のAIフレームワークに密結合せずに構築
- **非同期処理**：非ブロッキング操作のためにPythonのasyncioを活用
- **ツール統合**：ローカルツールとクラウドベースのツールの両方を使用
- **メモリ管理**：対話間で会話コンテキストを維持
- **可観測性**：詳細な実行メトリクスとトークン使用状況をログ記録
- **マルチステップ推論**：複雑な推論チェーンを調整

## ローカルツール

エージェントにはローカルツールが含まれています：

1. `export_meeting_schedule_pdf`：フォーマット済みのPDFアジェンダを生成

## カスタマイズ

### 指示の変更

`agent_instructions.json`ファイルを変更してエージェントの動作をカスタマイズ：
```json
{
    "role": "ミーティングレコーダーアシスタント",
    "goal": "ミーティングの録画と要約ワークフローを自動化",
    "general": "ミーティング処理のための詳細な指示..."
}
```

### LLMプロバイダーの切り替え

デフォルトでは、エージェントはOpenAIを使用します。別のプロバイダーに切り替えるには：

```python
# my_agent.py内
llm_provider = LLMProvider.ANTHROPIC  # または他のサポートされているプロバイダー

# 初期化時
self.agent.select_llm_provider(llm_provider)
```

### カスタムツールの追加

`local_tools.py`を拡張して、追加の関数とツール宣言を含む新しいツールを追加します。

## デプロイ

xpander.aiのマネージドインフラストラクチャにエージェントをデプロイ：

```bash
xpander deploy
```

デプロイされたエージェントのログを監視：

```bash
xpander logs
```

## トラブルシューティング

- **カレンダー統合の問題**：Google Calendar API認証を確認
- **依存関係の不足**：すべての要件がインストールされていることを確認
- **ツール実行エラー**：詳細なエラーメッセージについてログを確認

## その他のリソース

- [xpander.aiドキュメント](https://docs.xpander.ai)
- [SDK APIリファレンス](https://docs.xpander.ai/api-reference/07-sdk)
- [サンプルライブラリ](https://github.com/xpander-ai/xpander.ai/tree/main/examples) 

## コードの探索

エージェントの動作をよりよく理解するために、調べるべき主要なファイルは次のとおりです：

1. **meeting_recorder_agent.py**：以下を処理するコアエージェント実装：
   - xpander.ai SDKでの初期化
   - `_agent_loop()`でのエージェント推論ループ
   - ツール実行フロー
   - トークン使用状況の追跡とメトリクス

2. **xpander_handler.py**：以下を示すイベント駆動アーキテクチャ実装：
   - xpanderプラットフォームにイベントハンドラーを登録する方法
   - 着信実行リクエストの処理
   - 構造化された結果の返却

3. **tools/local_tools.py**：以下を含むサンプルツール実装：
   - 関数定義
   - ツールスキーマ宣言
   - ツール登録のヘルパーユーティリティ

コードは、xpander.aiでAIエージェントを構築するためのベストプラクティスを示すよう構造化されています：

- 関心事の明確な分離
- 非同期処理
- 構造化されたエラーハンドリング
- 詳細なロギング
- モジュール式ツール実装

エージェントを変更する場合は、変更を加える前にこれらのファイルを調べて実行フローを理解することから始めてください。 