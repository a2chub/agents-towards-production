![](https://europe-west1-atp-views-tracker.cloudfunctions.net/working-analytics?notebook=tutorials--runpod-gpu-deploy--readme)

# RunPodサーバーレスを使用したAIエージェントのデプロイ

## 概要

このチュートリアルでは、RunPodのサーバーレスインフラストラクチャを使用してAIエージェントをデプロイする方法を実演します。Ollamaモデルを使用するCrewAIライティングエージェントを構築してデプロイし、ユーザーのトピックに基づいて記事を生成するスケーラブルなAPIエンドポイントを作成します。コンテナ化、サーバーレスデプロイメント、インフラストラクチャを管理せずに動的スケーリングを処理する方法を学びます。

## 詳細説明

### 動機

AIエージェントの従来のデプロイには、サーバーのセットアップ、オートスケーリングの設定、ロードバランサーの管理、コスト最適化の処理など、重要なインフラストラクチャ管理が必要です。これにより、インフラストラクチャの管理よりもインテリジェントエージェントの構築に集中したい開発者にとって障壁が生じます。

**サーバーレスデプロイメントは、インフラストラクチャの懸念を完全に取り除くことで、このパラダイムを根本的に変えます**。サーバーを管理する代わりに、開発者はリクエストが到着したときに自動的に実行されるコンテナにコードをパッケージ化するだけです。RunPodはすべてのスケーリング、ロードバランシング、リソース割り当てを自動的に処理します。

このアプローチは、予測不可能な使用パターンとリソース集約的な操作のため、AIエージェントにとって特に価値があります。RunPodのサーバーレスエンドポイントを使用すると、エージェントが実際に使用したコンピューティング時間に対してのみ支払います。

### RunPodとは

RunPodは、AI、ML、一般的なコンピューティングニーズ向けに構築されたクラウドコンピューティングプラットフォームです。RunPodを使用すると、リモートGPU/CPUサーバーを簡単に起動でき、ワークロードをデプロイおよびスケーリングするシームレスな方法を提供します。

### デプロイするアプリケーション

このチュートリアルで参照されるファイルについては、[このリポジトリのファイルを参照してください](./crew-ai-ollama-runpod-tutorial/README.md)。

目的は、ユーザーがトピックを送信できるAPIエンドポイントを作成し、エージェントが調査を行い（エージェントに疑似調査ツールが提供されています）、エージェントの調査に基づいて短い記事を返すことです。

### 主要コンポーネント

デプロイアーキテクチャは、連携して動作する3つの重要なコンポーネントで構成されています：

**CrewAIフレームワーク**：複雑なタスクを完了するためにAIワーカーを調整するマルチエージェントシステム。この場合、ブログコンテンツを作成するために協力する調査エージェントとライティングエージェントを管理します。

**Ollamaランタイム**：OpenHermesなどのモデルをコンテナ内で直接実行するローカル言語モデルサーバー。これにより、外部API依存関係が排除され、より高速で信頼性の高い推論が提供されます。

**RunPodサーバーレス**：AIワークロード用のコンテナライフサイクル、スケーリング、リソース割り当てを自動的に管理するGPU最適化サーバーレスプラットフォーム。

### メソッド概要

デプロイメント方法は以下のワークフローに従います：

1. **コンテナ化**：CrewAIアプリケーション、Ollamaランタイム、言語モデルをDockerコンテナにパッケージ化
2. **ハンドラー定義**：受信リクエストを処理し、AIエージェントを調整するPython関数を作成
3. **サーバーレスデプロイメント**：自動スケーリング設定でコンテナをRunPodのサーバーレスインフラストラクチャにデプロイ
4. **API統合**：トピックを受け入れて生成されたコンテンツを返すREST APIエンドポイントを通じてエージェントを公開

このアプローチは、複雑なマルチコンポーネントAIシステムを単純なAPI呼び出しに変換し、すべてのインフラストラクチャの懸念事項は自動的に処理されます。

### 主な利点

サーバーレスアプローチには以下のような利点があります：
- **自動スケーリング**：リクエスト量に基づいてコンテナが起動および停止
- **コスト効率**：アイドルリソースではなく、実際のコンピューティング時間に対してのみ支払い
- **インフラストラクチャ管理ゼロ**：サーバーの設定やスケーリングロジックの処理が不要
- **組み込みロードバランシング**：RunPodが利用可能なワーカー間でリクエストを分散

## RunPodへのサインアップ

### サインアップページ
[RunPodサインアップページ](https://get.runpod.io/nirdiamant)にアクセスしてアカウントを作成してください。

## RunPod用のアプリケーションの準備

### サーバーレスエンドポイントの理解

RunPodは、dockerizedアプリケーション用のクラウドコンピューティングプラットフォームを提供し、RunPodの`runpod SDK`を使用してRunPodエンドポイントがヒットされたときに実行されるPython関数をバインドできます。

必要な手順は以下のとおりです：
- ハンドラーの定義
- アプリケーションのコンテナ化
- RunPodへのアプリケーションのデプロイ
- アプリケーションのテスト
- 将来のメンテナンスの理解

### ハンドラーの定義

Python関数を実行にバインドするのは簡単です。SDKを使用して、次の行で単純にPython関数をバインドします：`runpod.serverless.start({"handler": handler})`。これにより、RunPodエンドポイントに送信されたリクエストに基づいてエージェントを実行するエントリポイントを定義できます。

エントリポイント関数は、以下のような構造のJSONリクエストを受け取ることが期待されます：

```python
{
    "input": {
        ...
    }
}
```

[サンプルコード](./crew-ai-ollama-runpod-tutorial/handler.py)の`handler.py`を参照すると、ユーザーのリクエストからトピックを受け取り、調査機能を実行するハンドラーを定義する最も重要なセクションを確認できます。

```python
import runpod
...

def create_blog_post(topic):
    """CrewAIを使用して指定されたトピックのブログ投稿を作成"""
    # トピック用のタスクを作成
    blog_task = Task(
        description=f"""
        {topic}についてのブログ投稿を書く。
        
        ブログには以下を含める：
        1. 注目を集めるタイトル
        2. 読者を引き込む簡潔な導入部
        3. 調査に裏付けられた3-4の主要ポイント
        4. 結論と潜在的な行動喚起で締めくくる
        ...

def handler(job):
    """ジョブを処理するために使用されるハンドラー関数。"""
    job_input = job["input"]
    
    # ジョブ入力からトピックを抽出、提供されていない場合はデフォルトで"technology"
    topic = job_input.get("topic", "technology")
    
    try:
        # CrewAIを使用してブログ投稿を生成
        blog_post = create_blog_post(topic)
        
        # 成功レスポンスを返す
        return {
            "status": "success",
            "blog_post": blog_post
        }
    except Exception as e:
        # エラーが発生した場合はエラーレスポンスを返す
        return {
            "status": "error",
            "message": str(e)
        }


runpod.serverless.start({"handler": handler})
```

### アプリケーションのコンテナ化

Dockerを使用して、すべての依存関係、コード、ランタイム環境をポータブルコンテナにパッケージ化することで、アプリケーションを「コンテナ化」できます。これにより、AIエージェントがローカルマシンからRunPodのサーバーまで、異なる環境で一貫して実行されることが保証されます。コンテナには必要なものがすべて含まれています：ベースオペレーティングシステム、Pythonパッケージ、Ollamaモデル、アプリケーションコードで、「私のマシンでは動作する」問題を排除します。

#### Dockerfileの重要な要点

FROM runpod/pytorch:2.0.1-py3.10-cuda11.8.0-devel-ubuntu22.04

...

# Python依存関係のインストール
COPY requirements.txt /requirements.txt
RUN pip install --upgrade pip && \
    pip install uv && \
    uv pip install --upgrade -r /requirements.txt --no-cache-dir && \
    uv pip install "langchain-community>=0.0.34" --no-cache-dir && \
    python -c "import crewai; print(f'\nCrewAI version: {crewai.__version__}')" && \
    python -c "import crewai_tools; print('CrewAI Tools import successful')"

...

# ビルド中にモデルをダウンロード（より良い処理で）
RUN ollama serve > /dev/null 2>&1 & \
    sleep 25 && \
    ollama pull openhermes && \
    sleep 10 && \
    pkill ollama

...

# 起動スクリプト
CMD ["/start.sh"]

#### 主要ポイント

アプリケーションをコンテナ化する際は、開始できる複製可能な環境を作成することを目指しています。RunPodは実際にさまざまなDockerイメージとテンプレートを提供しています。

![example](assets/docker_templates.png)

##### ベーステンプレート
この場合、`runpod/pytorch:2.0.1-py3.10-cuda11.8.0-devel-ubuntu22.04`がベーステンプレートとして機能しており、すでにインストールされているさまざまなパッケージがあり、その上にさらにパッケージを追加するための簡単なベースを提供します。

##### Python依存関係のインストール

次のステップでは、必要なPythonパッケージ（主にcrewAI）をダウンロードします。

```dockerfile
COPY requirements.txt /requirements.txt
RUN pip install --upgrade pip && \
    pip install uv && \
    uv pip install --upgrade -r /requirements.txt --no-cache-dir && \
    uv pip install "langchain-community>=0.0.34" --no-cache-dir && \
    python -c "import crewai; print(f'\nCrewAI version: {crewai.__version__}')" && \
    python -c "import crewai_tools; print('CrewAI Tools import successful')"
```

##### Ollamaのインストール
最後に、Ollamaモデルをダウンロードしてイメージに組み込みます。
```dockerfile
RUN ollama serve > /dev/null 2>&1 & \
    sleep 25 && \
    ollama pull openhermes && \
    sleep 10 && \
    pkill ollama
```

#### 考慮事項
このDockerfileを構築する際、サーバーレスの動作方法は、リクエストが行われたときにRunPodがDockerコンテナをダウンロード、キャッシュ、初期化することです。つまり、可能であれば、コンテナが起動したときに必要なリソースがすでに備わっているように、モデルをイメージ自体にダウンロードする方が良いということです。

実行時にOllamaモデルなどの追加リソースをダウンロードする必要がある場合、これは応答に遅延を追加する可能性があります。したがって、モデルをDockerイメージに組み込む方が良いのです。

## アプリケーションのデプロイ

サーバーレスタブに移動すると、「新しいエンドポイント」をデプロイする際に2つのオプションがあります：DockerイメージまたはGithubリポジトリ。

![example](assets/endpoint_creation_options.png)
![example](assets/endpoint_source_selection.png)

### Github統合

GithubリポジトリをConnectすることで、RunPodが自動的にイメージをビルドしてデプロイします。イメージはRunPodに直接プッシュされます。

### Dockerイメージ

独自のDockerイメージをビルドすることもできます。この場合、githubリポジトリ用に、以下のコマンドを使用してビルドし、Dockerhubにプッシュできます：

```bash
docker build -t justinrunpod/agents:1.0 . --push --platform linux/amd64
```

![example](assets/docker_image_selection.png)

### ハードウェアの選択

DockerイメージまたはGithubリポジトリの使用を選択した後、動的に割り当てたいハードウェアを選択するよう求められます。

RunPodが提供するGPUのプールがあり、RunPodは可用性に基づいてGPUの優先順位選択を自動的にローテーションします。

![example](assets/hardware_selection.png)
![example](assets/gpu_prioritization.png)

### 最小および最大ワーカー

ワーカーは、エンドポイントがリクエストを受信したときに初期化されるコンテナインスタンスです。通常、ワーカーは`idle`状態で表示され、リクエストが来るのを待機していることを意味します。

ワーカーがリクエストに対して起動して実行する実際のコンピューティング時間に対してのみ支払い、`idle`状態に対しては支払いません。

#### 注意：
![example](assets/worker_allocation.png)

ワーカーを作成するとき、`3`つの最大ワーカーを定義したとしても、RunPodは必要に応じて`throttled`（使用中）のワーカーを交換するのに役立つ追加のワーカーをいくつか割り当てようとします。これにより、アプリケーションは定義した`最大ワーカー`が常に`idle`で応答準備ができている状態を最適に維持できます。

### FlashBoot

アプリケーションのボリュームが多い場合、FlashBootはコールドスタートを削減するRunPodの機能です。コールドスタートとは、マシンを起動してメモリにリソースをロードする初期読み込み時間です。コールドスタートは、サーバーレス関数がゼロから初期化する必要があるときにユーザーが経験する遅延です - これには、コンテナの起動、メモリへのモデルのロード、接続の確立が含まれます。FlashBootにより、特にトラフィック需要が高いアプリケーションの場合、この起動時間を最小限に抑えることができます。

## アプリケーションのテスト

### エンドポイント
ダッシュボードでは、リクエストを行うことができるエンドポイントが表示されます。ダッシュボードで直接テストリクエストを実行するか、プログラムでリクエストを行うことができます。

### 組み込みダッシュボード
RunPod内のリクエストタブを使用すると、ブラウザで直接テストリクエストを実行できます。
![example](assets/test_request_dashboard.png)
![example](assets/test_request_input.png)
![example](assets/test_request_output.png)

### cURL / Python / Javascript
APIキーを作成した後、エンドポイントにプログラムでリクエストを行うことができます。

```bash
curl --request POST \
     --url https://api.runpod.ai/v2/[ENDPOINT_ID]/run \
     --header "accept: application/json" \
     --header "authorization: [YOUR_API_KEY]" \
     --header "content-type: application/json" \
     --data '
{
  "input": {
    "topic": "Technology"
  }
}
'
```

## 将来のメンテナンス

### Dockerイメージの新しいリリース
新しいリリースがある場合は、更新されたDockerイメージを提供できます。その際、準備ができたら新しいイメージにワーカーをシームレスに交換するローリングアップデートが行われます。

![example](assets/docker_image_update.png)

### Github統合
Githubに新しいコミットを行うと、新しいリリースが自動的にデプロイされ、ロールアウトされることがわかります。

![example](assets/github_integration_update.png)

## 結論

このチュートリアルでは、RunPodのサーバーレスインフラストラクチャを使用してAIエージェントをデプロイするプロセス全体を説明しました。達成したことの要約は以下のとおりです：

1. **RunPodアカウントを設定**し、プラットフォームを探索
2. **アプリケーションを準備**し、APIリクエストに応答するハンドラー関数を定義
3. **アプリケーションをコンテナ化**し、Dockerを使用してより高速な応答時間のためにOllamaモデルを組み込み
4. **アプリケーションをデプロイ**し、サーバーレスインフラストラクチャを使用してRunPodに展開
5. **アプリケーションをテスト**し、ダッシュボードインターフェースとプログラムによるAPI呼び出しの両方を通じて確認
6. Dockerイメージの更新とGitHub統合を通じて**アプリケーションの保守について学習**

このサーバーレスアプローチにより、基盤となるインフラストラクチャを管理することなく、需要に基づいて自動的にスケーリングできるAIエージェントをデプロイできます。実際に使用したコンピューティング時間に対してのみ支払うため、低ボリュームと高ボリュームの両方のアプリケーションに対してコスト効率的です。

このチュートリアルでカバーした基礎により、広範なDevOpsの専門知識やインフラストラクチャへの投資を必要とすることなく、プロトタイプから本番環境までスケーリングできる複雑なAIワークフローをデプロイできるようになります。