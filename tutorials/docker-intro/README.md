![](https://europe-west1-atp-views-tracker.cloudfunctions.net/working-analytics?notebook=tutorials--docker-intro--readme)

# Docker入門
## 概要

DockerはコアなCI/CDツールであり、コンテナ化の業界標準です。DevOps、MLOps、そして本番環境でコードやアプリケーションをデプロイする開発者に広く使用されています。Dockerには急峻な学習曲線があり、このチュートリアルはDockerとは何か、そして開発ワークフローでの使用における利点を理解できるようにすることを目的としています。このチュートリアルでは、Dockerの基礎について深く掘り下げます。Dockerの概要から始め、イメージの構築やコンテナとしての実行などの基本的な概念と操作について学びます。


## AIエージェント開発においてDockerが重要な理由

本番環境向けのAIエージェントを構築している場合、Dockerは「あったらいいもの」ではなく、必須です。現代のAIエージェントは複雑な依存関係のエコシステムに依存しています：特定のPythonバージョン、機械学習ライブラリ、ベクターデータベース、APIクライアント、そして多くの場合GPUドライバーなど。コンテナ化なしでは、開発環境で完璧に動作するものが、バージョンの競合、依存関係の欠落、または環境の違いにより本番環境で大失敗する可能性があります。Dockerは、AIエージェントがラップトップ、クラウドサーバー、またはKubernetesクラスター上で一貫して動作することを保証します。さらに重要なことに、エージェントがプロトタイプから本番環境にスケールする際、Dockerはシームレスなデプロイメント、自動スケーリング、信頼性の高い更新を可能にし、実験的なAIコードを企業が依存できる堅牢でプロダクション対応のアプリケーションに変えます。


> 「一枚の絵は千の言葉に値する」と言われますが、[reddit](https://www.reddit.com/r/ProgrammerHumor/comments/cw58z7/it_works_on_my_machine/)からの以下の画像は、Dockerの背後にある動機について自明です：


<br>
<br /><figure>
 <img src="assets/docker-meme.jpg" width="70%" align="center"/></a>
<figcaption> Dockerミーム（出典：reddit）</figcaption>
</figure>

<br>
<br />


## 範囲

このチュートリアルでは以下のトピックをカバーします：
- Dockerとは何か？
- Dockerfile
- イメージの構築
- コンテナの実行
- ボリュームのマウント
- AIエージェントのDocker化

## 前提条件

これは初心者レベルのチュートリアルであり、事前知識は必要ありません。ただし、コマンドラインコマンドの基本的な理解があることが推奨されます。

このチュートリアルの例を実行するには以下が必要です：
- Docker Desktop（または同等のもの）がコンピューターにインストールされていること
- Docker Hubアカウント

### Docker Desktopのインストール
 
DockerはLinux OS上で動作するように構築されているため、macOSやWindowsなどの他のOSではネイティブに実行できません。Docker Desktopは、Linux OS以外でDockerコンテナを実行できる必要な仮想環境を提供します。さらに、割り当てられたリソースとコンテナを管理するためのGUIインターフェースを提供します。

Docker Desktopをインストールするには、[Dockerのウェブサイト](https://www.docker.com/products/docker-desktop)にアクセスし、お使いのオペレーティングシステム用の指示に従ってください：

<br>
<br /><figure>
 <img src="assets/docker-desktop.png" width="100%" align="center"/></a>
<figcaption> Docker Desktop</figcaption>
</figure>

<br>
<br />

Docker Desktopをインストールすると、自動的に起動するはずです。自動的に起動しない場合は、画面の右上隅にあるアイコンをクリックしてください（macOSの場合）。

ターミナルから`docker --version`と入力して、Dockerが実行されているかどうかを確認できます：
```shell
>docker --version                                                          
Docker version 28.0.4, build b8034c0
```


### Docker Hubアカウントの設定

Docker Hubは、コンテナを保存、共有、実行できる公開リポジトリです（GitHubがコードの保存と保守を可能にするのと同様）。Docker Hubアカウントを設定するには、[Docker Hub](https://hub.docker.com/)にアクセスして指示に従ってください。


<br>
<br /><figure>
 <img src="assets/docker-hub.png" width="100%" align="center"/></a>
<figcaption> Docker Hub</figcaption>
</figure>

<br>
<br />


## Dockerとは何か？

DockerはCI/CDツールであり、異なる環境（ローカルからリモートなど）へのシームレスなコード開発とデプロイメントを可能にします。OSレベルの仮想化を作成することで、アプリケーションとその依存関係を仮想コンテナにパッケージ化し、異なる環境間で転送できます。

開発環境内でDockerを使用する主な利点は：

- **再現性** - Dockerを使用すると、コードとその依存関係を単一のコンテナにシームレスにパッケージ化し、高い一貫性レベルでコードを実行、テスト、共有、デプロイできます
- **コラボレーション** - Dockerは、開発者チームが特定のプロジェクトで一緒に作業する際の依存関係の混乱を解決します。統一された環境を持つことで、開発段階で大量の時間を節約できます。例えば、ある開発者がエラーを受け取った場合、他の開発者がそのエラーを再現してデバッグを支援することが簡単になります
- **デプロイメント** - Dockerは開発環境から本番環境へのコードの移送を簡素化します


## 再現性の問題

Dockerは一般的なDevOpsの問題を解決するために構築されました - 異なる環境（例：開発者のローカル環境と本番環境）間でコードを転送する際の再現性の欠如です。再現性はDevOpsに限定されず、データサイエンスとAIにおいて重要な役割を果たします。

>再現性を、コードが実行されるユーザーやマシンに関係なく、同じコードを実行したときに正確に同じ結果を生成する能力として定義できます。

私が再現性という用語を初めて聞いたのは学士号取得中で、再現性はシード番号を設定して乱数をロックダウンすることで始まり終わると学びました。私のお気に入りのシード番号は12345です。データサイエンティストとして働き始めたとき、再現性はシード番号の設定を超えるものだと気づきました。コードの再現性に影響を与える可能性のある主な要素は次のとおりです：

- **バージョン管理** — 何よりもまず、同じ結果を再現することは、コードの変更を追跡する能力から始まります
- **ランダム化** — シード番号を設定することによる乱数生成の制御
- **ソフトウェアバージョン** — Python、R（または他のプログラミング言語）のバージョンとその依存関係（ライブラリなど）がコードの結果に影響を与えます。例えば、pandas v1.0で構築されたコードは、関数の非推奨化によりv2.0では動作しない可能性があります
- **オペレーティングシステム（OS）** — ほとんどのプログラミング言語、特にRとPythonは、異なるコンパイラ（C、C++など）や他の組み込みOSコンポーネントを使用します。OSの種類とそのバージョンがコードの結果に影響を与える可能性があります
- **ハードウェア** — 最後に、ハードウェア（またはインフラストラクチャ）の種類が結果に影響を与える可能性があります（ARM/Intel/Appleプロセッサなど）

通常のデータサイエンスワークフローでは、再現性はいくつかの要因に依存します。再現性の最も基本的で些細な要素の1つはコードのバージョニングです。バージョン管理されていないコードを使用すると、コードの変更を追跡したり、同じコードが異なる環境で実行されることを確認したりすることが不可能になり、それがGitが生まれた理由です。

もう1つの重要な要因はパッケージのバージョニングです。時間の経過とともに、パッケージとその依存関係は変化し進化し、新機能の追加、バグ修正、古い関数の置換と非推奨化が行われる傾向があります。場合によっては、特定のパッケージバージョンを使用してコードを実行すると、古いバージョンや新しいバージョンのパッケージと同じ結果が得られなかったり、動作しなかったりすることがあります。例えば、Pandas 1.xで書かれたコードはPandas 2.xでは動作しない可能性があり、その逆も同様です。

同様に、プログラミング言語は時間とともに変化する傾向があり、古いバージョンで構築されたコードを使用すると、最新のバージョンでは動作しなかったりサポートされなかったりする可能性があります。さらに、異なるオペレーティングシステムは異なるソフトウェアアーキテクチャを使用したり、バックエンドで異なるタイプのコンパイラ（c、c++など）を実行したりします。この違いがコードの再現性に影響を与える可能性があります。

最後に、CPUアーキテクチャのタイプ（ARM、Intel、Apple Siliconなど）が基礎となるソフトウェアに影響を与える可能性があります。一部のパッケージやソフトウェアは別のビルドが必要であったり、サポートされていなかったりする可能性があり、最終的にコードの再現性に影響を与える可能性があります。

以下の図1は、異なる環境間で転送する際のコードの再現性に影響を与える要因を示しています


<br>
<br /><figure>
 <img src="assets/reproducibility without Docker.png" width="100%" align="center"/></a>
<figcaption> 図1：Docker（およびGit）なしの再現性</figcaption>
</figure>

<br>
<br />

Gitはコードのバージョニングと監視のソリューションを提供し、適切に使用されている限り、実行されるユーザーやマシンに関係なく正確に再現できることを保証します。Dockerや同様のソリューションは、コンテナ内に分離された環境を作成することで環境の不一致の問題に対処し、その環境をコードとともにデスクトップ、ラップトップ、サーバーなどの任意のリモートマシンに転送でき、プロセスをシームレスに再現できます。

Apple SiliconやIntelベースのマシンなど、異なるハードウェアアーキテクチャ上でソフトウェアを開発する場合、環境に潜在的な違いがある可能性があります。Dockerは各CPUアーキテクチャ用の専用イメージを作成することで、この問題に部分的に対処できます。ただし、すべてのコンテナがまったく同じ特性を持つことを確認するために追加のテストが必要となるため、このアプローチは時間がかかり、コストがかかる可能性があります。

図2は、DockerとGitを使用した一般的なワークフローを示しています。GitとGitHub/Gitlab/Bitbucket（または同様のサービス）を使用してコードのバージョン管理を行います。Dockerを使用して、コードの開発とテストに使用されるコンテナ化された環境を設定します。次に、コンテナと一緒にコードを、コンテナをサポートする任意のリモート環境（GitHub Actions、AWS、GCPなど）に転送します。

<br>
<br /><figure>
 <img src="assets/reproducibility with Docker.png" width="100%" align="center"/></a>
<figcaption> 図2：DockerとGitによる再現性</figcaption>
</figure>

<br>
<br />

このワークフローは高レベルの再現性を提供しますが、異なるハードウェア設定により発生する可能性のある再現性の問題はカバーしていません。各ハードウェアアーキテクチャ用の専用環境を構築するなど、この問題に対処する様々な方法があります（詳細は[こちら](https://docs.docker.com/build/building/multi-platform/)で利用可能）。

注：仮想環境の使用はDockerの代替ではありません。実際、一緒にうまく機能します。VEはこのチュートリアルの一部ではありませんが、VEとDockerの違いについては以下の[記事](https://medium.com/@rami.krispin/running-python-r-with-docker-vs-virtual-environment-4a62ed36900f)で詳しく読むことができます。


## Dockerワークフロー

このセクションでは、イメージを構築してコンテナ内で実行できるようにする基本的なDockerワークフローと要件に焦点を当てます。基本的なDockerワークフローには以下のステップが含まれます：
- Dockerfileを使用した環境要件の定義
- Dockerfileからのイメージの構築
- イメージからコンテナとしての実行
- コンテナ内でのコードの開発とテスト
- コンテナ内でのコードの転送

> 人々はよく「イメージ」と「コンテナ」という用語を混同します。イメージは、環境仕様を含むビルドプロセスによって作成されるアーティファクトを指します。コンテナは、イメージをインスタンスとして実行するプロセスを指します。

 
図3は、イメージを構築してコンテナとして実行するプロセスを示しています。

<br>
<br /><figure>
 <img src="assets/dockerfile to container.png" width="100%" align="center"/></a>
<figcaption> 図3：Dockerワークフロー</figcaption>
</figure>

<br>
<br />


このワークフローの主要コンポーネントは：

- **Dockerfile** - イメージのレシピまたはブループリントにより、開発環境の要件に応じてコンポーネントを追加し、依存関係をカスタマイズできます
- **Docker CLI** - イメージを構築してコンテナ化された環境として実行するためのコアコマンド


以下のセクションでは、プロセスの各ステップをより詳細に説明します。

## Dockerfile

`Dockerfile`は、イメージを構築する方法についてDockerエンジンに指示を提供します。これをイメージのレシピと考えることができます。以下の構造を使用した独自の直感的な構文があります：

``` Dockerfile
COMMAND some instructions
```

例えば、以下の`Dockerfile`は、公式のPython（バージョン3.10）イメージをベースイメージとしてインポートし、`apt-get update`と`apt-get install`を使用して`curl`ライブラリをインストールします：


`./examples/ex1/Dockerfile`
``` Dockerfile
FROM python:3.10

LABEL example=1

ENV PYTHON_VER=3.10

RUN apt-get update && apt-get install -y --no-install-recommends curl
```

簡単に言えば、`FROM`コマンドを使用してDockerレジストリからインポートするイメージを指定しました（イメージを構築する前に、使用しているDockerレジストリサービスにログインすることを忘れないでください！）。`LABEL`コマンドはラベルやコメントを設定するために使用され、`ENV`コマンドは環境変数を設定するために使用されます。最後に、`RUN`コマンドはコマンドラインでコマンドを実行するために使用され、この場合は`curl`ライブラリをインストールします。

それでは、Dockerfileのコアコマンドを確認しましょう：
- `FROM` - イメージのビルドに使用するベースイメージを定義します。ほとんどの場合、ゼロからイメージを構築する場合を除き、事前にインストールされたOSといくつかの依存関係を持つベースイメージを使用します。例えば、このチュートリアルでは、ベースイメージとして公式の[Pythonイメージ](https://hub.docker.com/_/python)をインポートします
- `LABEL` - イメージのメタデータに著者、メンテナー、ライセンスなどの情報を追加できます
- `ENV` - 環境変数を設定するために使用します
- `ARG` - ビルド時にパラメータを設定できます
- `RUN` - ビルド時にCLIコマンド（例：`pip install ...`、`apt-get ...`、`apt-install...`、`wget...`など）を実行して、ベースイメージに追加のコンポーネントを追加できます
- `COPY` - ローカルシステムからイメージにオブジェクト（ファイルやフォルダなど）をコピーできます
- `WORKDIR` - イメージ内の作業ディレクトリを設定します
- `EXPOSE` - 実行時にイメージを公開するポート番号を定義します
- `CMD` - イメージの実行時に実行するデフォルトコマンドを設定します
- `ENDPOINT` - 実行可能ファイルとして実行されるコンテナを設定できます

この時点で、これらのコマンドのいくつかの使用例を完全に理解していなくても心配しないでください。次のセクションでイメージの構築を開始すると、より理解しやすくなります。

## Docker Build

`Dockerfile`の準備ができたら、次のステップはコマンドラインから`docker build`コマンドを使用してイメージを構築することです。例えば、このリポジトリのルートフォルダから`build`コマンドを使用して上記の`Dockerfile`を構築してみましょう：

``` shell
docker build . -f ./examples/ex-1/Dockerfile -t rkrispin/vscode-python:ex1 
```

`build`コマンドで使用した引数は次のとおりです：
- `-f`タグは`Dockerfile`のパスを定義します。この引数はオプションであり、`Dockerfile`とは異なるフォルダから`build`関数を呼び出す場合に使用する必要があります
- `.`記号は、ファイルシステムのコンテキストフォルダを`Dockerfile`のフォルダとして定義します。この場合、ファイルシステムを使用しませんでしたが、他の場合では、ビルド時にローカルフォルダからイメージにファイルを呼び出してコピーできます
- `-t`はイメージの名前とタグ（例：バージョン）を設定するために使用されます。この場合、イメージ名は`rkrispin/vscode-python`で、タグは`ex1`です。


以下の出力が予想されます：

``` shell
[+] Building 94.2s (6/6) FINISHED                                                                                                                                                                                                  
 => [internal] load build definition from Dockerfile                                                                                                                                                                          0.0s
 => => transferring dockerfile: 162B                                                                                                                                                                                          0.0s
 => [internal] load .dockerignore                                                                                                                                                                                             0.0s
 => => transferring context: 2B                                                                                                                                                                                               0.0s
 => [internal] load metadata for docker.io/library/python:3.10                                                                                                                                                                6.0s
 => [1/2] FROM docker.io/library/python:3.10@sha256:a8462db480ec3a74499a297b1f8e074944283407b7a417f22f20d8e2e1619782                                                                                                         82.1s
 => => resolve docker.io/library/python:3.10@sha256:a8462db480ec3a74499a297b1f8e074944283407b7a417f22f20d8e2e1619782                                                                                                          0.0s
 => => sha256:a8462db480ec3a74499a297b1f8e074944283407b7a417f22f20d8e2e1619782 1.65kB / 1.65kB                                                                                                                                0.0s
 => => sha256:4a1aacea636cab6af8f99f037d1e56a4de97de6025da8eff90b3315591ae3617 2.01kB / 2.01kB                                                                                                                                0.0s
 => => sha256:23e11cf6844c334b2970fd265fb09cfe88ec250e1e80db7db973d69d757bdac4 7.53kB / 7.53kB                                                                                                                                0.0s
 => => sha256:bba7bb10d5baebcaad1d68ab3cbfd37390c646b2a688529b1d118a47991116f4 49.55MB / 49.55MB                                                                                                                             26.1s
 => => sha256:ec2b820b8e87758dde67c29b25d4cbf88377601a4355cc5d556a9beebc80da00 24.03MB / 24.03MB                                                                                                                             11.0s
 => => sha256:284f2345db055020282f6e80a646f1111fb2d5dfc6f7ee871f89bc50919a51bf 64.11MB / 64.11MB                                                                                                                             26.4s
 => => sha256:fea23129f080a6e28ebff8124f9dc585b412b1a358bba566802e5441d2667639 211.00MB / 211.00MB                                                                                                                           74.5s
 => => sha256:7c62c924b8a6474ab5462996f6663e07a515fab7f3fcdd605cae690a64aa01c7 6.39MB / 6.39MB                                                                                                                               28.2s
 => => extracting sha256:bba7bb10d5baebcaad1d68ab3cbfd37390c646b2a688529b1d118a47991116f4                                                                                                                                     1.6s
 => => sha256:c48db0ed1df2d2df2dccd680323097bafb5decd0b8a08f02684b1a81b339f39b 17.15MB / 17.15MB                                                                                                                             31.9s
 => => extracting sha256:ec2b820b8e87758dde67c29b25d4cbf88377601a4355cc5d556a9beebc80da00                                                                                                                                     0.6s
 => => sha256:f614a567a40341ac461c855d309737ebccf10a342d9643e94a2cf0e5ff29b6cd 243B / 243B                                                                                                                                   28.4s
 => => sha256:00c5a00c6bc24a1c23f2127a05cfddd90865628124100404f9bf56d68caf17f4 3.08MB / 3.08MB                                                                                                                               29.4s
 => => extracting sha256:284f2345db055020282f6e80a646f1111fb2d5dfc6f7ee871f89bc50919a51bf                                                                                                                                     2.5s
 => => extracting sha256:fea23129f080a6e28ebff8124f9dc585b412b1a358bba566802e5441d2667639                                                                                                                                     6.2s
 => => extracting sha256:7c62c924b8a6474ab5462996f6663e07a515fab7f3fcdd605cae690a64aa01c7                                                                                                                                     0.3s
 => => extracting sha256:c48db0ed1df2d2df2dccd680323097bafb5decd0b8a08f02684b1a81b339f39b                                                                                                                                     0.5s
 => => extracting sha256:f614a567a40341ac461c855d309737ebccf10a342d9643e94a2cf0e5ff29b6cd                                                                                                                                     0.0s
 => => extracting sha256:00c5a00c6bc24a1c23f2127a05cfddd90865628124100404f9bf56d68caf17f4                                                                                                                                     0.2s
 => [2/2] RUN apt-get update && apt-get install -y --no-install-recommends curl                                                                                                                                               5.9s
 => exporting to image                                                                                                                                                                                                        0.1s
 => => exporting layers                                                                                                                                                                                                       0.1s
 => => writing image sha256:a8e4c6d06c97e9a331a10128d1ea1fa83f3a525e67c7040c2410940312e946f5                                                                                                                                  0.0s
 => => naming to docker.io/rkrispin/vscode-python:ex1  

 ```

**注：** 上記のビルドの出力は、イメージの異なるレイヤーを説明しています。この時点で、それが意味不明に見えても心配しないでください。次のセクションでイメージレイヤーに焦点を当てると、このタイプの出力を読むのが簡単になります。


`docker images`コマンドを使用して、イメージが正常に作成されたことを確認できます：

``` shell
>docker images
REPOSITORY                             TAG       IMAGE ID       CREATED        SIZE
rkrispin/vscode-python                 ex1       a8e4c6d06c97   43 hours ago   1.02GB
```

次のセクションでは、イメージレイヤーとキャッシングプロセスに焦点を当てます。


### イメージレイヤー

Dockerのイメージのビルドプロセスはレイヤーに基づいています。コンテキストに応じて、dockerエンジンはビルド時に`Dockerfile`の各コマンドを取得し、レイヤーまたはメタデータに変換します。`FROM`や`RUN`などの`Dockerfile`コマンドはレイヤーに変換され、`LABEL`、`ARG`、`ENV`、`CMD`などのコマンドはメタデータに変換されます。例えば、上記の`rkrispin/vscode-python`イメージのビルドの出力で、2つのレイヤーがあることがわかります：
- 最初のレイヤーは`[1/2] FROM...`で始まり、`Dockerfile`の`FROM python:3.10`行に対応し、Python 3.10公式イメージをインポートします
- 2番目のレイヤーは`[2/2] RUN apt-get...`で始まり、`Dockerfile`の`RUN`コマンドに対応します
<br>
<br /><figure>
<img src="assets/docker-layers.png" width="100%" align="center"/></a>
<figcaption> 図4 - Dockerfileに関するビルド出力の例</figcaption>
</figure>

<br>
<br />

`docker inspect`コマンドは、イメージのメタデータの詳細をJSON形式で返します。これには、環境変数、ラベル、レイヤー、一般的なメタデータが含まれます。以下の例では、[jq](https://jqlang.github.io/jq/)を使用してメタデータJSONファイルからレイヤー情報を抽出します：

``` shell
> docker inspect rkrispin/vscode-python:ex1 | jq '.[] | .RootFS'
{
  "Type": "layers",
  "Layers": [
    "sha256:332b199f36eb054386cd2931c0824c97c6603903ca252835cc296bacde2913e1",
    "sha256:2f98f42985b15cbe098d2979fa9273e562e79177b652f1208ae39f97ff0424d3",
    "sha256:964529c819bb33d3368962458c1603ca45b933487b03b4fb2754aa55cc467010",
    "sha256:e67fb4bad8f42cca08769ee21bbe15aca61ab97d4a46b181e05fefe3a03ee06d",
    "sha256:037f26f869124174b0d6b6d97b95a5f8bdff983131d5a1da6bc28ddbc73531a5",
    "sha256:737cec5220379f795b727e6c164e36e8e79a51ac66a85b3e91c3f25394d99224",
    "sha256:65f4e45c2715f03ed2547e1a5bdfac7baaa41883450d87d96f877fbe634f41a9",
    "sha256:baef981f26963b264913e79bd0a1472bae389441022d71f559e9d186600d2629",
    "sha256:88e1d36ff4812423afc93d5f6208f2783df314d5ecf6f961325c65e1dbf891da"
  ]
}

```

上記のイメージのレイヤー出力からわかるように、`rkrispin/vscode-python:ex1`イメージには9つのレイヤーがあります。各レイヤーはハッシュキー（例：`sha256:...`）で表され、バックエンドにキャッシュされています。ビルド出力では、dockerエンジンが`FROM`と`RUN`コマンドから2つのプロセスをトリガーしたことがわかりましたが、2つではなく9つのレイヤーになりました。その主な理由は、ベースラインイメージをインポートしたときに、レイヤーを含むインポートされたイメージの特性を継承したためです。この場合、`FROM`を使用して8つのレイヤーを含む公式のPythonイメージをインポートし、`RUN`コマンドを実行して9番目のレイヤーを追加しました。ベースラインイメージをプルして、inspectコマンドを使用してそのレイヤーを確認することでテストできます：

``` shell
> docker pull python:3.10
3.10: Pulling from library/python
bba7bb10d5ba: Already exists 
ec2b820b8e87: Already exists 
284f2345db05: Already exists 
fea23129f080: Already exists 
7c62c924b8a6: Already exists 
c48db0ed1df2: Already exists 
f614a567a403: Already exists 
00c5a00c6bc2: Already exists 
Digest: sha256:a8462db480ec3a74499a297b1f8e074944283407b7a417f22f20d8e2e1619782
Status: Downloaded newer image for python:3.10
docker.io/library/python:3.10

> docker inspect python:3.10 | jq '.[] | .RootFS'
{
  "Type": "layers",
  "Layers": [
    "sha256:332b199f36eb054386cd2931c0824c97c6603903ca252835cc296bacde2913e1",
    "sha256:2f98f42985b15cbe098d2979fa9273e562e79177b652f1208ae39f97ff0424d3",
    "sha256:964529c819bb33d3368962458c1603ca45b933487b03b4fb2754aa55cc467010",
    "sha256:e67fb4bad8f42cca08769ee21bbe15aca61ab97d4a46b181e05fefe3a03ee06d",
    "sha256:037f26f869124174b0d6b6d97b95a5f8bdff983131d5a1da6bc28ddbc73531a5",
    "sha256:737cec5220379f795b727e6c164e36e8e79a51ac66a85b3e91c3f25394d99224",
    "sha256:65f4e45c2715f03ed2547e1a5bdfac7baaa41883450d87d96f877fbe634f41a9",
    "sha256:baef981f26963b264913e79bd0a1472bae389441022d71f559e9d186600d2629"
  ]
}
```

### レイヤーキャッシング

Dockerの欠点の1つは、イメージのビルド時間です。Dockerfileの複雑さのレベルが高いほど（例：大量の依存関係）、ビルド時間は長くなります。時々、最初の試みでビルドが期待どおりに実行されないことがあります。いくつかの要件が不足しているか、ビルド時に何かが壊れることがあります。ここでキャッシュの使用がイメージの再構築時間の短縮に役立ちます。Dockerには、各レイヤーをゼロから構築すべきか、キャッシュされたレイヤーを活用して時間を節約できるかを識別するスマートなメカニズムがあります。例えば、前の例に別のコマンドを追加して`vim`エディターをインストールしてみましょう。一般的に、`curl`パッケージをインストールするために使用している同じapt-getに追加できます（そして、すべきです）が、レイヤーキャッシング機能を示すために、別々に実行します：


`./examples/ex2/Dockerfile`
``` Dockerfile
FROM python:3.10

LABEL example=1

ENV PYTHON_VER=3.10

RUN apt-get update && apt-get install -y --no-install-recommends curl

RUN apt-get update && apt-get install -y --no-install-recommends vim
```

以下のコマンドを使用してこのイメージを構築し、`rkrispin/vscode-python:ex2`としてタグ付けします：

``` shell
docker build . -f ./examples/ex-2/Dockerfile -t rkrispin/vscode-python:ex2 --progress=plain
```
（前のビルドを実行した場合）以下の出力が期待されます：

``` shell
 => [internal] load build definition from Dockerfile                                                                                                                                     0.0s
 => => transferring dockerfile: 234B                                                                                                                                                     0.0s
 => [internal] load .dockerignore                                                                                                                                                        0.0s
 => => transferring context: 2B                                                                                                                                                          0.0s
 => [internal] load metadata for docker.io/library/python:3.10                                                                                                                           0.0s
 => [1/3] FROM docker.io/library/python:3.10                                                                                                                                             0.0s
 => CACHED [2/3] RUN apt-get update && apt-get install -y --no-install-recommends curl                                                                                                   0.0s
 => [3/3] RUN apt-get update && apt-get install -y --no-install-recommends vim                                                                                                          34.3s
 => exporting to image                                                                                                                                                                   0.4s 
 => => exporting layers                                                                                                                                                                  0.4s 
 => => writing image sha256:be39eb0eb986f083a02974c2315258377321a683d8472bac15e8d5694008df35                                                                                             0.0s 
 => => naming to docker.io/rkrispin/vscode-python:ex2   
 ```


上記のビルド出力からわかるように、最初と2番目のレイヤーは前のビルドからすでに存在しています。したがって、dockerエンジンはそれらのキャッシュされたレイヤーをイメージに追加し（ゼロから構築するのではなく）、3番目のレイヤーだけを構築してvimエディターをインストールします。

**注：** デフォルトでは、ビルド出力は簡潔で短いです。`progress`引数を追加して`plain`に設定することで、ビルド時により詳細な出力を取得できます：

``` shell
> docker build . -f ./examples/ex-2/Dockerfile -t rkrispin/vscode-python:ex2 --progress=plain
#1 [internal] load .dockerignore
#1 transferring context: 2B done
#1 DONE 0.0s

#2 [internal] load build definition from Dockerfile
#2 transferring dockerfile: 234B done
#2 DONE 0.0s

#3 [internal] load metadata for docker.io/library/python:3.10
#3 DONE 0.0s

#4 [1/3] FROM docker.io/library/python:3.10
#4 DONE 0.0s

#5 [2/3] RUN apt-get update && apt-get install -y --no-install-recommends curl
#5 CACHED

#6 [3/3] RUN apt-get update && apt-get install -y --no-install-recommends vim
#6 CACHED

#7 exporting to image
#7 exporting layers done
#7 writing image sha256:be39eb0eb986f083a02974c2315258377321a683d8472bac15e8d5694008df35 0.0s done
#7 naming to docker.io/rkrispin/vscode-python:ex2 done
#7 DONE 0.0s
```

前のビルドですでに3番目のレイヤーをキャッシュしているため、上記の出力のすべてのレイヤーがキャッシュされ、実行時間は1秒未満です。

Dockerfileを設定する際は、レイヤーのキャッシングプロセスについて注意深く、戦略的である必要があります。レイヤーの順序は重要です！以下の画像は、dockerエンジンがキャッシュされたレイヤーを使用する場合と、それらを再構築する場合を示しています。最初の画像は初期ビルドを示しています：

<br>
<br /><figure>
<img src="assets/docker layers 1.png" width="100%" align="center"/></a>
<figcaption> 図5 - イメージの初期ビルドの図解。左側はDockerfileのコマンドを表し、右側は対応するレイヤーを表します</figcaption>
</figure>

<br>
<br />

この場合、ビルド時に4つのレイヤーに変換される4つのコマンドを持つDockerfileがあります。5番目のコマンドを追加して3番目のコマンドの直後に配置すると何が起こるでしょうか？dockerエンジンは、Dockerfileの最初と2番目のコマンドが変更されていないことを識別し、対応するキャッシュされたレイヤー（1と2）を使用し、残りのレイヤーをゼロから再構築します：

<br>
<br /><figure>
<img src="assets/docker layers 2.png" width="100%" align="center"/></a>
<figcaption> 図6 - イメージの再構築中のキャッシングプロセスの図解</figcaption>
</figure>
<br>
<br />

Dockerfileを計画する際、該当する場合は、ほとんど変わらないコマンドを配置し、新しい更新を可能な限りファイルの最後に保つことがグッドプラクティスです。

これはDockerの氷山の一角にすぎず、学ぶべきことはまだたくさんあります。次のセクションでは、コンテナ内でPythonを実行するさまざまな方法を探ります。

## Docker Run

前のセクションでは、`Dockerfile`でイメージ要件を定義し、`build`コマンドでビルドする方法を見ました。このセクションでは、`docker run`コマンドを使用してコンテナ内でPythonを実行することに焦点を当てます。

`docker run`または`run`コマンドを使用すると、イメージから新しいコンテナを作成して実行できます。通常、`run`コマンドは、以下の構文に従って、dockerized アプリケーションやサーバーを起動したり、コードを実行したりするために使用されます：

``` shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

例えば、公式のPython 3.10イメージで`run`コマンドを使用できます：

``` shell
docker run python:3.10 
```

驚くべきことに（または驚くべきではないことに）、何も起こりませんでした。これをより良く理解するには、`Dockerfile`に戻る必要があります。一般的に、イメージは以下を実行するために使用できます：
- サーバー
- アプリケーション

どちらの場合も、`Dockerfile`を使用して実行時にそれらを起動できるように設定します。サーバーの場合、`Dockerfile`の`PORT`と`CMD`コマンドを使用して、それぞれサーバーのポートを設定し、サーバーを起動します。次に、`run`コマンドを使用して`-p`（または`--publish list`）オプションを追加し、サーバーのポートをローカルポートにマップします。同様に、アプリケーションを起動するには、`Dockerfile`の`CMD`コマンドを使用して実行時の起動コマンドを定義し、`--interactive`と`--tty`オプションを使用してコンテナを対話モードで起動し、アプリケーションにアクセスできるようにします。

`python:3.10`イメージに戻り、`inspect`コマンドを使用して`CMD`コマンドが定義されているかどうかを確認しましょう：

``` shell
> docker inspect python:3.10 | jq '.[] | .Config.Cmd'
[
  "python3"
]
```

**注：** 再び`jq`ライブラリを使用して、JSON出力からCMDメタデータを解析しました

ご覧のとおり、`python:3.10`イメージの`CMD`は、デフォルトのPython起動コマンド - `python3`を実行するように設定されており、実行時にPythonを起動します。それでは、`--interactive`と`--tty`オプションを追加して、コンテナを対話モードで実行してみましょう：

```shell
 docker run --interactive --tty python:3.10 
 ```
これにより、イメージ上のデフォルトのPythonバージョンが起動されます。次に、`print`コマンドを使用して`Hello World!`を出力してテストできます：

```python
Python 3.10.12 (main, Jun 14 2023, 18:40:54) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print("Hello World!")
Hello World!
>>> 
```

## ボリュームマウント

上記の出力でわかるように、`docker run`コマンドはbashターミナル内でコンテナを起動しました。デフォルトでは、コンテナは一時的な環境で実行されます。つまり、イメージ内で作成したコードはエクスポートできず、コンテナの実行を停止すると失われます。


簡単な解決策は、volume引数でボリュームをマウントすることです。簡単にするために、以下のPythonスクリプトがあるローカルフォルダー - `ex3`をマウントします：
`hello-world.py`
``` python
print("Hello World!")
```

コンテナを起動する前に、イメージを再構築し、`CMD`命令を追加して、Pythonの代わりにコンテナ内でbashターミナルを起動するようにしましょう。以下の`Dockerfile`を使用します：

`./examples/ex3/Dockerfile`
``` Dockerfile
FROM python:3.10

LABEL example=3

ENV PYTHON_VER=3.10

RUN apt-get update && apt-get install -y --no-install-recommends curl

RUN apt-get update && apt-get install -y --no-install-recommends vim

CMD ["/bin/sh", "-c", "bash"]
```

これにより、ファイルシステムにアクセスし、bashターミナルからスクリプトを実行できるようになります。

イメージを再度構築しますが、今回は作成したDockerfileを使用します：

```shell
docker build . -f ./examples/ex3/Dockerfile -t rkrispin/vscode-python:ex3
```

そして、以下のコマンドで起動します：

```
docker run -v .:/ex3  --interactive --tty rkrispin/vscode-python:ex3
```



コマンドを実行し、volume引数を使用してローカルフォルダーex3をコンテナ内のボリュームとしてマップします。以下のコマンドを実行してこれを行うことができます：

``` shell
docker run -v ./examples/ex3:/ex3  --interactive --tty python:3.10 
```
ここで、`-v`引数はローカルフォルダー`./examples/ex3`をコンテナ内の`/my_python_scipts`にマップします。これによりコンテナが起動し、コンテナ内でbashターミナルが開きます。以下に示すように、`ls`コマンドの出力は、ルートフォルダーへのファイルとフォルダーを返します。これには、`-v`引数でマウントしたローカルフォルダー - `my_python_scripts`が含まれます：

```shell
root@8c94db531eb4:/# ls
bin  boot  dev	etc  home  lib	media  mnt  my_python_scipts  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@8c94db531eb4:/# cd my_python_scipts/
root@8c94db531eb4:/my_python_scipts# ls
Dockerfile  hello-world.py
root@8c94db531eb4:/my_python_scipts# python hello-world.py
Hello World!
root@8c94db531eb4:/my_python_scipts#
```

この技術を使用すると、コンテナ内でコードを実行しながら、コードをローカルに保持できます。

## AIエージェントのDocker化

これまで、Dockerfileの設定方法、イメージの構築、コンテナとしての実行方法を確認しました。このセクションでは、点を結び、簡単なAIエージェントをDocker化してコンテナ内で実行する方法を示します。

まず、OpenAI APIにリクエストを送信し、結果を出力する簡単なPythonスクリプトを定義しましょう：

`./examples/ex4/simple_agent.py`
``` python
from openai import OpenAI

client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "What is the capital of France?"}],
)

print(response.choices[0].message.content)
```

このPythonスクリプトを以下のDockerfileでラップします：
`./examples/ex4/Dockerfile`

``` Dockerfile
FROM python:3.10-slim

RUN pip install --no-cache-dir openai

COPY ./examples/ex4/simple_agent.py /app/simple_agent.py

CMD ["python", "/app/simple_agent.py"]
```
`FROM`コマンドを使用して公式のPythonイメージ - バージョン3.10スリムバージョン（軽量サイズ）をプルします。次に、`RUN`コマンドを使用して`openai` Pythonライブラリをインストールし、`COPY`コマンドを使用してPythonスクリプトをローカルフォルダーからイメージファイルシステムにコピーします。最後に、`CMD`コマンドを使用して、コンテナの実行時にPythonスクリプトを実行します。

次に、以下のコマンドを使用してイメージを構築します：

``` shell
docker build . -f ./examples/ex4/Dockerfile -t rkrispin/simple_agent:ex4
```

イメージの実行時に、OpenAI APIキーを提供する必要があります。ベストプラクティスとして、ビルド時に認証情報やシークレットをイメージに保存してはいけません。代わりに、`--env`（または`-e`）引数を使用してAPIキーを環境変数として設定します：

``` shell
docker run --env OPENAI_API_KEY=$OPENAI_API_KEY rkrispin/simple_agent:ex4
```
**注：** `OPENAI_API_KEY`をAPIキーを保持する環境変数の名前に置き換える必要があります。

そして、以下の出力が期待されます：

``` shell
The capital of France is Paris.
```

### コンテナの機能化

前の例は限定的であまり有用ではありません。1つの質問にしか答えられないからです。小さな調整で動的にし、ユーザーが質問できるようにすることができます。Pythonスクリプトから始めましょう：

`./examples/ex5/dynamic_agent.py`
```python
import os
from openai import OpenAI

client = OpenAI()

question = os.getenv("QUESTION", "What is the capital of France?")

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role": "user",  "content": question}],
)

print(f"Question: {question}")
print(f"Answer: {response.choices[0].message.content}")
```

ご覧のとおり、`QUESTION`という環境変数を追加しました。これはエージェントの入力として使用されます。スクリプトは質問と答えを出力します。

同様に、Dockerfileを変更します：
``` Dockerfile
FROM python:3.10-slim

RUN pip install --no-cache-dir openai

COPY ./examples/ex5/dynamic_agent.py /app/dynamic_agent.py

ENV QUESTION="What is the capital of France?"

CMD ["python", "/app/dynamic_agent.py"]

```

**注：** 必須ではありませんが、環境変数QUESTIONのデフォルト値を設定することは良い習慣です。これにより、ユーザーがこの変数の値を提供しない場合、デフォルト値が使用されます。

コンテナを構築しましょう：

``` shell
docker build . -f ./examples/ex5/Dockerfile -t rkrispin/dynamic_agent:ex5
```

そして、コンテナを実行し、質問を環境変数として設定します：
```shell
docker run --env OPENAI_API_KEY=$OPENAI_API_KEY --env QUESTION="What is the capital of United Kingdom"  rkrispin/dynamic_agent:ex5
```

これにより以下の出力が返されます：

```shell
Question: What is the capital of United Kingdom
Answer: The capital of the United Kingdom is London.
```

## 結論

このチュートリアルでは、Dockerの基礎を確認しました。Dockerはソフトウェア開発のコアプログラムである再現性を解決します。`Dockerfile`を使用してイメージを構築するプロセスに深く入り込み、コンテナ内でコードを実行する簡単な例を見ました。最後に、簡単なAIエージェントをコンテナ内にコンテナ化し、実行時にOpenAI APIを呼び出す方法を示しました。

これはDockerが行うことの氷山の一角であり、うまくいけば、Dockerで何ができるか、どのように始めるかのアイデアを提供します。


---
このチュートリアルは[Rami Krispin](https://www.linkedin.com/in/rami-krispin/)によって書かれました
