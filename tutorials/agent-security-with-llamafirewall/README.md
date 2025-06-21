![](https://europe-west1-atp-views-tracker.cloudfunctions.net/working-analytics?notebook=tutorials--agent-security-with-llamafirewall--readme)

# AIエージェントセキュリティのためのLlamaFirewallチュートリアル

LlamaFirewallを使用してAIエージェントのセキュリティ対策を実装する方法を示す包括的なチュートリアルです。

## 著者
このチュートリアルは[Matan Kotick](https://www.linkedin.com/in/matan-kotick-664735252)と[Amit Ziv](https://www.linkedin.com/in/amit-ziv-49690b120)によって作成されました。

## エージェントセキュリティリスクの理解

AIエージェントは、3つの主要な攻撃面にわたって重大なセキュリティ上の課題に直面しています：

### 入力セキュリティ
- **プロンプトインジェクション**: エージェントの命令を上書きしようとする試み
- **有害コンテンツ**: 不適切または危険な入力の検出
- **コンテキスト操作**: メモリ/コンテキストの改ざん防止

### 出力セキュリティ
- **動作アラインメント**: 応答が意図した目的に一致することを確保
- **コンテンツの安全性**: 有害または誤解を招く出力の防止
- **情報管理**: 機密データの開示管理

### ツールセキュリティ
- **アクセス制御**: 無許可のツール使用の防止
- **使用監視**: 悪意のあるツール操作の検出
- **リソース保護**: システム乱用の防止

これらの脆弱性は、セキュリティ違反、リソースの不正使用、ユーザーやシステムへの潜在的な害につながる可能性があります。このチュートリアルでは、LlamaFirewallを使用してこれらの課題に対処する方法を示します。

**注**: エージェントの実装とデプロイメントコンテキストに応じて、追加の攻撃面が存在する可能性があります。例えば、モデルソース、トレーニングデータ、サードパーティツールに関するサプライチェーンリスクを考慮する必要があります。モデルホスティングとAPIエンドポイントのインフラストラクチャセキュリティ、および監査ログとポリシー強制のコンプライアンス要件も、包括的なセキュリティ戦略の一部として対処すべき重要な懸念事項です。



## このチュートリアルについて

このチュートリアルでは、オープンソースセキュリティフレームワークLlamaFirewallを使用してセキュリティ対策を実装する方法を示します。例はプロダクション対応コードではありませんが、実世界のアプリケーションに適応できる主要なセキュリティ概念を示しています。

### LlamaFirewallとは？

LlamaFirewallは、AIエージェントを様々な脅威から保護するように設計された包括的なオープンソースセキュリティフレームワークです。3つの主要なガードレールを提供します：

1. **PromptGuard 2**: プロンプトインジェクション攻撃から保護
2. **Agent Alignment Checks**: エージェントの動作が意図した命令に一致することを確保
3. **CodeShield**: 有害または悪意のあるコードの生成を防止

## チュートリアル

このリポジトリには、LlamaFirewallを使用してAIエージェントのセキュリティ概念を実装する方法を示すいくつかのJupyterノートブックが含まれています：

- 悪意のあるプロンプトや有害コンテンツから保護するための入力検証
- エージェントの応答が意図した動作に一致することを確保するための出力検証
- 無許可または危険なアクションを防止するためのツール呼び出しセキュリティ

これらのチュートリアルはOpenAI Agents SDKを使用していますが、セキュリティ概念はどのAIエージェントプラットフォームにも適用できます。

1. [Hello Llama](hello-llama.ipynb) - 潜在的に有害なコンテンツを検出してブロックする基本的なメッセージスキャンを実演。
2. [Input Guardrail](input-guardrail.ipynb) - 悪意のあるプロンプトから保護するためのユーザー入力検証方法を示す。
3. [Output Guardrail](output-guardrail.ipynb) - AIエージェントの応答が意図した動作に一致し、有害コンテンツを含まないことを確保するための検証方法を説明。
4. [Tools Security](tools-security.ipynb) - 入力検証、出力スキャン、外部ツール使用からの保護を含むAIエージェントツールの包括的セキュリティをカバー。

## 前提条件

### システム要件
- Python 3.9+
- OpenAI APIキー
- 必要パッケージ（requirements.txt参照）
- モデルダウンロード用に約2GBのディスク容量

### モデル要件
LlamaFirewallはアクセス許可が必要なHuggingFaceモデルを使用します：
- モデルはデフォルトで`~/.cache/huggingface`にダウンロードされます
- モデルは初回ダウンロード後にローカルにキャッシュされます
- **重要**: Llama Prompt Guard 2モデルは明示的なアクセス許可が必要です

### APIキー
- **OpenAI APIキー** （エージェントに必要）
- **Together APIキー** （Alignment Checkerスキャナーに必要）
  - [Together AI](https://www.together.ai)から取得
  - `output_guardrail`に必要

## インストール

1. このリポジトリをクロン
2. 依存関係をインストール：
   ```bash
   pip install -r requirements.txt
   ```
3. APIキーを含む`.env`ファイルを作成：
   ```
   OPENAI_API_KEY=your_openai_api_key_here
   TOGETHER_API_KEY=your_together_api_key_here    # Alignment Checkerスキャナーに必要
   ```

4. HuggingFaceアクセスの設定：
   1. HuggingFaceアカウントを作成
   2. [Llama Prompt Guard 2](https://huggingface.co/meta-llama/Llama-Prompt-Guard-2-86M)へのアクセスをリクエスト：
      - "Access repository"をクリック
      - 法的名前、生年月日、組織を提供
      - Llama 4 Community Licenseを承認
   3. Settingsで読み取りアクセストークンを作成
   4. 初回実行時にプロンプトが表示されたらトークンを入力

5. `llamafirewall configure`を実行して：
    1. 必要なモデルがローカルに利用可能か確認
    2. 利用可能でない場合はHuggingFaceからモデルのダウンロードを支援
    3. 特定のスキャナーに必要なAPIキーが環境にあるか確認 

**注:** このチュートリアルはHuggingFaceモデルを使用し、`~/.cache/huggingface`にダウンロードされます。

## モニタリング

このチュートリアルはOpenAI Agents SDKを使用しており、OpenAIダッシュボードを通じて包括的なモニタリングとトレーシング機能を提供します。ガードレールの呼び出しを含むすべてのエージェントアクティビティが自動的に記録され、リアルタイムでモニタリングできます。

### ダッシュボードアクセス
- [OpenAIアカウントダッシュボード](https://platform.openai.com/)にログイン
- "Dashboard"セクションに移動
- エージェントインタラクションとガードレールチェックの詳細なトレースを表示

![OpenAI Dashboard showing LlamaFirewall guardrail invocation](assets/openai-trace.png)

このモニタリング機能は透明性を確保し、ガードレールが意図したとおりに機能していることを確認できるようにします。
