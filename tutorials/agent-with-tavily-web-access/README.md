![](https://europe-west1-atp-views-tracker.cloudfunctions.net/working-analytics?notebook=tutorials--agent-with-tavily-web-access--readme)

# Tavilyを使用したウェブアクセスでエージェントを強化


## 概要

このチュートリアルシリーズは、AIエージェントにリアルタイムウェブアクセスを提供し、エージェントが最新情報をコンテキストとして活用できるようにしたいPython開発者向けに設計されています。ライブウェブ情報は、リサーチの実行、正確な質問への回答、トレンドの監視、最新の推奨事項の提供を任されたAIエージェントにとって重要です。ウェブを検索し、価値あるコンテンツを抽出し、ウェブサイトをインテリジェントにナビゲートし、リアルタイムウェブ情報をプライベートナレッジベースに統合するAIエージェントの構築方法を学びます。 

## アジェンダ

このチュートリアルシリーズは、3つの独立したチュートリアルで構成されたステップバイステップの学習パスに従います：
1. [チュートリアル#1](./search-extract-crawl.ipynb)では、**ウェブアクセスの基本**をカバーします。

2. [チュートリアル#2](./web-agent-tutorial.ipynb)では、ウェブを検索、スクレイプ、クロールできる**ウェブエージェントを構築**します。

3. 最後に、[チュートリアル#3](./hybrid-agent-tutorial.ipynb)では、**リアルタイムウェブ情報とプライベートナレッジベースデータを組み合わせた**システムを開発します。


## ディレクトリ構造

```
📁 agent-with-tavily-web-access/
├── 📓 search-extract-crawl.ipynb  # Tutorial notebook 1
├── 📓 web-agent-tutorial.ipynb    # Tutorial notebook 2
├── 📓 hybrid-agent-tutorial.ipynb # Tutorial notebook 3
├── 📁 assets/                     # Diagrams and screenshots
│   ├── 🖼️ web-agent.svg
|   ├── 🖼️ hybrid.svg
|   ├── 🖼️ api-key.png
│   └── 🖼️ sign-up.png
├── 📁 supplemental/                # Supplemental materials
│   ├── 📓 vectorize_tutorial.ipynb # Vectorize your own documents
│   ├── 📁 docs/                    # Sample CRM documents
│   └── 📁 db/                      # Pre-built Chroma vector database
└── 📄 README.md                    # This file
```