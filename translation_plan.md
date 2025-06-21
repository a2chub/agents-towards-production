# エージェント向けプロダクション - ドキュメント日本語化実施手順書

## 概要
このプロジェクトのすべてのドキュメントを日本語に翻訳します。ソースコードおよびファイル名はオリジナルを保持し、ドキュメント内容のみを翻訳します。

## 翻訳対象ファイル一覧

### 1. ルートレベルドキュメント（優先度：高）
- [ ] README.md
- [ ] CONTRIBUTING.md
- [ ] LICENSE

### 2. チュートリアルREADMEファイル（優先度：中）
- [ ] tutorials/agent-memory-with-redis/README.md
- [ ] tutorials/agent-observability-with-qualifire/README.md
- [ ] tutorials/agent-security-apex/README.md
- [ ] tutorials/agent-security-with-llamafirewall/README.md
- [ ] tutorials/agent-security-with-qualifire/README.md
- [ ] tutorials/agent-with-tavily-web-access/README.md
- [ ] tutorials/agentic-applications-by-xpander.ai/README.md
- [ ] tutorials/agentic-applications-by-xpander.ai/meeting-recorder-agent/full-app/README.md
- [ ] tutorials/docker-intro/README.md
- [ ] tutorials/runpod-gpu-deploy/README.md
- [ ] tutorials/runpod-gpu-deploy/crew-ai-ollama-runpod-tutorial/README.md

### 3. システムプロンプトファイル（優先度：中）
- [ ] tutorials/agent-security-apex/system_prompt.txt

### 4. Jupyterノートブック（優先度：低）
- [ ] tutorials/LangGraph-agent/langgraph_tutorial.ipynb
- [ ] tutorials/a2a/a2a_tutorial.ipynb
- [ ] tutorials/agent-evaluation-intellagent/intellagent-evaluation-tutorial.ipynb
- [ ] tutorials/agent-memory-with-redis/agent_memory_tutorial.ipynb
- [ ] tutorials/agent-observability-with-qualifire/agent-observability-with-qualifire.ipynb
- [ ] tutorials/agent-security-apex/agent-security-evaluation-tutorial.ipynb
- [ ] tutorials/agent-security-with-llamafirewall/hello-llama.ipynb
- [ ] tutorials/agent-security-with-llamafirewall/input-guardrail.ipynb
- [ ] tutorials/agent-security-with-llamafirewall/output-guardrail.ipynb
- [ ] tutorials/agent-security-with-llamafirewall/tools-security.ipynb
- [ ] tutorials/agent-security-with-qualifire/1-agent-security-with-qualifire.ipynb
- [ ] tutorials/agent-security-with-qualifire/2-agent-security-with-qualifire-ui.ipynb
- [ ] tutorials/agent-with-mcp/mcp-tutorial.ipynb
- [ ] tutorials/agent-with-streamlit-ui/building-chatbot-notebook.ipynb
- [ ] tutorials/agent-with-tavily-web-access/hybrid-agent-tutorial.ipynb
- [ ] tutorials/agent-with-tavily-web-access/search-extract-crawl.ipynb
- [ ] tutorials/agent-with-tavily-web-access/supplemental/vectorize_tutorial.ipynb
- [ ] tutorials/agent-with-tavily-web-access/web-agent-tutorial.ipynb
- [ ] tutorials/agentic-applications-by-xpander.ai/meeting-recorder-agent/creating_multi_step_ai_agents_with_xpander_tutorial.ipynb
- [ ] tutorials/fastapi-agent/fastapi-agent-tutorial.ipynb
- [ ] tutorials/fine-tuning-agents/fine_tuning_agents_guide.ipynb
- [ ] tutorials/fine-tunning-agents/fine_tuning_agents_guide.ipynb
- [ ] tutorials/on-prem-llm-ollama/ollama_tutorial.ipynb
- [ ] tutorials/on-prem-llm-ollama/scripts/basic_usage.ipynb
- [ ] tutorials/on-prem-llm-ollama/scripts/langchain_agent.ipynb
- [ ] tutorials/tracing-with-langsmith/langsmith_basics.ipynb

## 翻訳ガイドライン

### 1. 基本原則
- オリジナルの体裁を可能な限り尊重する
- コードブロック、コマンド、URLはそのまま保持
- 技術用語は適切に日本語化または併記
- 意味的な誤解が生じないよう正確に翻訳

### 2. 翻訳時の注意事項
- ファイル名、フォルダ名は変更しない
- コード例、環境変数名、コマンドは翻訳しない
- 技術的な固有名詞（フレームワーク名、ツール名など）は原則そのまま
- 見出し構造（#, ##, ###）を維持
- リンクのURLは変更しない

### 3. 作業手順
1. 進捗管理ファイル（translation_progress.md）を作成
2. 優先度の高いファイルから順次翻訳
3. 各ファイルの翻訳完了後、進捗管理ファイルを更新
4. 翻訳完了後、コメント付きでコミット

### 4. コミットメッセージ形式
```
日本語化: [ファイル名] - [簡潔な説明]

例: 日本語化: README.md - メインドキュメントの翻訳完了
```

## 統計情報
- 総ファイル数: 41ファイル
  - Markdownファイル: 13ファイル
  - ライセンスファイル: 1ファイル
  - システムプロンプト: 1ファイル
  - Jupyterノートブック: 26ファイル

## 推定作業時間
- ルートレベル文書: 2-3時間
- チュートリアルREADME: 4-6時間
- Jupyterノートブック: 8-12時間
- 合計推定時間: 14-21時間