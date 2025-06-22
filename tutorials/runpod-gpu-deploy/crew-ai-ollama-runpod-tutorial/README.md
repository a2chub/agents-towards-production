![](https://europe-west1-atp-views-tracker.cloudfunctions.net/working-analytics?notebook=tutorials--runpod-gpu-deploy--crew-ai-ollama-runpod-tutorial--readme)

# RunPod上のCrewAI + Ollama：サーバーレスAIエージェント

## 概要

このリポジトリでは、[RunPod](https://get.runpod.io/nirdiamant)のサーバーレスインフラストラクチャ上にAIエージェントシステムをデプロイする方法を実演します。サンプルアプリケーションは、AIエージェントワークフローを通じて任意のトピックに関するブログ投稿を生成するAPIエンドポイントを作成します。

主要コンポーネント：
- **CrewAI**：ロールプレイングAIエージェントを調整するフレームワーク
- **Ollama**：オープンソースLLMをローカルで実行するツール
- **RunPod**：AIデプロイメント用のサーバーレスGPUインフラストラクチャ

*参照元：[RunPod Worker Template](https://github.com/runpod-workers/worker-template)*

## RunPodテンプレート

事前設定済みのRunPodテンプレートを使用してすぐに開始：

[<img src="https://img.shields.io/badge/RunPod-Deploy%20Now-blue?style=for-the-badge&logo=none" alt="Deploy on RunPod" width="200"/>](https://get.runpod.io/nirdiamant)

## 仕組み

このアプリケーションは：
1. RunPodを通じてAPIエンドポイントを公開
2. 入力としてトピックを受け入れる
3. CrewAIを使用して以下を行うAIエージェントを管理：
   - トピックを調査（この例では模擬調査ツールを使用）
   - 構造化されたブログ投稿を執筆
4. 生成されたブログ投稿をJSONとして返す

## アーキテクチャ

- **ハンドラー関数**：リクエストを処理するエントリポイント（handler.py）
- **ブログライターエージェント**：コンテンツ作成用に設定されたAIエージェント
- **模擬調査ツール**：事前定義された事実を提供することで調査をシミュレート
- **Dockerコンテナ**：サーバーレスデプロイメント用にOllamaと共にすべてをパッケージ化

## はじめに

### RunPodへのデプロイ

#### オプション1：RunPodテンプレートを使用（推奨）

1. 上記の「Deploy on RunPod」ボタンをクリック
2. エンドポイント設定を構成
3. デプロイしてすぐに使用開始

#### オプション2：カスタムイメージをビルドしてデプロイ

1. Dockerイメージをビルド：
   ```bash
   docker build -t yourusername/crew-ai-ollama:latest . --platform linux/amd64
   docker push yourusername/crew-ai-ollama:latest
   ```

2. RunPodで新しいServerless Endpointを作成
3. ソースとして「Docker Image」を選択
4. イメージURLを入力
5. ハードウェア設定を構成（推奨：24GB以上のVRAMを持つGPU）
6. ワーカー数を設定（最小：0、最大：予想されるトラフィックに基づく）
7. エンドポイントをデプロイ

## 使用方法

### APIエンドポイント

デプロイ後、RunPodエンドポイントは以下の形式でURLを提供します：
```
https://api.runpod.ai/v2/[ENDPOINT_ID]/run
```

### リクエスト例

```bash
curl --request POST \
     --url https://api.runpod.ai/v2/[ENDPOINT_ID]/run \
     --header "accept: application/json" \
     --header "authorization: [YOUR_API_KEY]" \
     --header "content-type: application/json" \
     --data '
{
  "input": {
    "topic": "artificial intelligence"
  }
}
'
```

### レスポンス例

```json
{
  "delayTime": 14836,
  "executionTime": 10799,
  "id": "4c04615b-540f-4d82-917d-4eb4256acd96-u1",
  "output": {
    "blog_post": "タイトル：技術の未来：期待される地平線\n\n導入：\n技術は人類の進歩の原動力であり、時代の課題に対応するために絶えず進化してきました。近年、技術進歩のペースは加速し、私たちはエキサイティングな新時代の瀬戸際に立っています。このブログ投稿では、技術の現状を探り、最も有望なトレンドを強調し、将来に何が待っているかを展望します。\n\n主要ポイント：\n1. 技術の絶え間ない重要性：最近の調査によると、業界リーダーの82％が技術を重要な焦点分野と考えており、私たちの世界を形作る上でのその重要性を示しています。医療や教育からコミュニケーションやエンターテイメントまで、技術は私たちの日常生活を変革し続けています。\n2. 研究投資の増加：昨年だけで、技術関連の研究への支出は印象的な34％増加しました。この投資の急増は、技術革新が経済成長と社会進歩の主要な触媒になるという信念の高まりを強調しています。\n3. 消費者の関心の急上昇：2022年以来、技術に対する消費者の関心は急上昇し、驚異的な56％の増加が記録されました。この急速な成長は、私たちの生活における技術の役割の増大と、世界中の個人の進化するニーズと欲求に対処する能力の証です。\n4. 期待される地平線：専門家は、今後5年間で技術に大きな進歩がもたらされると予測しています。人工知能と量子コンピューティングからバイオテクノロジーのブレークスルーまで、未来は無限の可能性と探求すべき新しいフロンティアの世界を約束しています。\n\n結論：\n技術の未来は間違いなく明るく、イノベーションが私たちをより接続され、効率的で、繁栄した社会へと導いています。このエキサイティングな新時代に向けて前進するにつれて、研究への投資を継続し、協力を促進し、技術の変革的な可能性を受け入れることが重要です。その力を活用するという私たちの集団的なコミットメントが、グローバルな課題を克服し、すべての人にとってより良い明日を形作るために技術を活用できる程度を決定します。\n\n行動喚起：\n#FutureOfTechを使用してソーシャルメディアで会話に参加し、技術が私たちの世界をどのように変えているかについてのあなたの考えを共有してください。さらに明るい未来に向けて、理解、インスピレーション、集団行動を促進する対話に参加しましょう。",
    "status": "success"
  },
  "status": "COMPLETED",
  "workerId": "fy17taoa4pyz2c"
}
```

## 主要ファイル

### handler.py

リクエストを処理するメインエントリポイント：

```python
import runpod
from crewai import Agent, Task, Crew, LLM
from crewai.tools import tool
import os

# Ollama LLMを設定
llm = LLM(model="ollama/openhermes", base_url="http://localhost:11434")

# 偽の情報を返す疑似調査ツールを作成
@tool("Research Tool")
def fake_research(topic: str) -> str:
    """
    トピックに関する情報を検索するふりをします。
    常に事前定義された偽のコンテンツを返します。
    """
    # ... 偽の調査データを返す
    
# ブログライターエージェントを作成
blog_writer = Agent(
    role="ブログライター",
    goal="さまざまなトピックについて魅力的で有益なブログ投稿を書く",
    backstory="あなたはプロのブログライターです...",
    tools=[fake_research],
    verbose=True,
    llm=llm
)

def create_blog_post(topic):
    """CrewAIを使用して指定されたトピックのブログ投稿を作成"""
    # ... タスクを作成して実行

def handler(job):
    """ジョブを処理するために使用されるハンドラー関数。"""
    job_input = job["input"]
    topic = job_input.get("topic", "technology")
    
    try:
        blog_post = create_blog_post(topic)
        return {
            "status": "success",
            "blog_post": blog_post
        }
    except Exception as e:
        return {
            "status": "error",
            "message": str(e)
        }

runpod.serverless.start({"handler": handler})
```

### Dockerfile

アプリケーションのコンテナ化バージョンを作成するための設定を含みます：

```Dockerfile
FROM runpod/pytorch:2.0.1-py3.10-cuda11.8.0-devel-ubuntu22.04

# Python依存関係をインストール
COPY requirements.txt /requirements.txt
RUN pip install --upgrade pip && \
    pip install uv && \
    uv pip install --upgrade -r /requirements.txt --no-cache-dir && \
    uv pip install "langchain-community>=0.0.34" --no-cache-dir

# ビルド中にモデルをダウンロード
RUN ollama serve > /dev/null 2>&1 & \
    sleep 25 && \
    ollama pull openhermes && \
    sleep 10 && \
    pkill ollama

# 起動スクリプト
CMD ["/start.sh"]
```

## パフォーマンスの考慮事項

- 最初のリクエストは、コンテナが初期化されるときにコールドスタートの遅延が発生する可能性があります
- DockerイメージにOllamaモデルを事前にロードすると、応答時間が大幅に改善されます
- 応答時間は通常、トピックの複雑さに応じて5〜15秒の範囲です

## このプロジェクトの拡張

このテンプレートはいくつかの方法で拡張できます：
- 模擬調査ツールを実際のウェブ検索機能に置き換える
- コンテンツ作成のさまざまな側面に特化したエージェントを追加
- 複数のエージェントが協力するより複雑なワークフローを実装
- 特定のユースケース用にLLMをファインチューニング

## 謝辞

- [CrewAI](https://github.com/crewAIInc/crewAI)
- [Ollama](https://github.com/ollama/ollama)
- [RunPod](https://get.runpod.io/nirdiamant)
- [RunPod Worker Template](https://github.com/runpod-workers/worker-template)