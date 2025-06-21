![](https://europe-west1-atp-views-tracker.cloudfunctions.net/working-analytics?notebook=tutorials--agent-security-with-qualifire--readme)

# Qualifireを使用したエージェントセキュリティ

このチュートリアルでは、QualifireをAIエージェントワークフローに統合し、堅牢なガードレールを実装し、プロンプトインジェクション、安全でないコンテンツ、幻覚、ポリシー違反などの一般的なLLMの脆弱性に対して事前に対策を講じる方法を示します。エージェントを保護するためのGatewayとSDKの両方の使用方法を学びます。

<img src="./assets/freddie-shield.png" width="200px" alt="Qualifire Shield Logo">

## クイックスタート

1.  **Qualifireにサインアップ:** まだの場合は、[https://app.qualifire.ai](https://app.qualifire.ai)でサインアップしてAPIキーを取得してください。
2.  **環境をセットアップ:** Pythonがインストールされていることを確認してください。また、`requirements.txt`に記載されている必要なパッケージをインストールする必要があります（ノートブックの一部として作成されます）。
3.  **ノートブックを開く:** Jupyter Notebookを起動し、`1-agent-security-with-qualifire.ipynb`を開きます。
4.  **セルを実行:** ノートブックの指示に従い、コードセルを順番に実行します。

## チュートリアル構成

チュートリアルはJupyterノートブック（`1-agent-security-with-qualifire.ipynb`）として提供され、以下を含みます：

- OpenAI GPT-4.1を使用した基本的なStreamlitチャットボットアプリケーションのセットアップ。
- LLM呼び出しを保護するための**Gateway**を使用した統合。
- 評価のきめ細かい制御のための**SDK**を使用した統合。
- 以下の検出と処理方法のデモンストレーション：
  - プロンプトインジェクション
  - 安全でないコンテンツ
  - 幻覚
  - ポリシー違反
- プラットフォームでポリシーを設定し、結果を表示する方法の説明。

## チュートリアルファイル

📓 **[チュートリアル1: Qualifireを使用したエージェントセキュリティ](./1-agent-security-with-qualifire.ipynb)**  
Qualifire統合の基礎、Gatewayの使用方法、エージェントセキュリティのためのSDK実装をカバーするコアチュートリアル。

📓 **[チュートリアル2: Qualifire UIを使用したエージェントセキュリティ](./2-agent-security-with-qualifire-ui.ipynb)**  
ユーザーインターフェースの側面と高度なセキュリティ実装に焦点を当てた拡張チュートリアル。
