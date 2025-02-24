GitHub Actionsはワークフロー全体の実行時間や各ステップの詳細な可視化、変更による効果測定を行うには工夫が必要となります。この記事では、OpenTelemetryを活用してGitHub Actionsの実行状況をトレースとメトリクスの形で収集する[GitHub Actions OpenTelemetry](https://github.com/marketplace/actions/github-actions-opentelemetry)を紹介します。どのステップに時間がかかっているのか、改善施策の効果がどの程度あったのかを把握しやすくなるため、ワークフローの継続的な改善を目指す方に役立ちます。

<figure class="figure-image figure-image-fotolife" title="Trace Sample (Jaeger)">[f:id:paper2parasol:20250224140940p:plain]<figcaption>Trace Sample (Jaeger)</figcaption></figure>

[:contents]



## 1. GitHub Actionsにおける課題

### 1-1. ワークフローの詳細を可視化する方法が提供されていない

GitHub Actionsではサマリー機能により、ジョブの実行時間や依存関係などの概要を確認できます。

<figure class="figure-image figure-image-fotolife" title="GitHub Actions Summary">[f:id:paper2parasol:20250224141009p:plain]<figcaption>GitHub Actions Summary</figcaption></figure>

一方で、ジョブ内の各ステップの実行時間やエラー発生箇所などの詳細を把握するには、ジョブのログを手動で確認する必要があります。ステップ数が多い、あるいは複数のジョブから成るワークフローでは、これらを個別に確認するのは負担が大きく、状況によっては見落としも生じがちです。

### 1-2. ワークフローの変更による影響を分析しづらい

GitHubはActions Performance Metricsを提供しており、失敗率や平均実行時間を参照可能です。

<figure class="figure-image figure-image-fotolife" title="Actions Performance Metrics">[f:id:paper2parasol:20250224141039p:plain]<figcaption>Actions Performance Metrics</figcaption></figure>

しかし、ワークフローの変更がどのように影響したかを定量的に把握したい場合、施策導入前後の詳細な比較やパーセンタイルのような指標が必要になることもあります。これらを取得するにはGitHub ActionsのAPIを用いた独自集計が必要となるため、導入の敷居が高い側面があります。



## 2. GitHub Actions OpenTelemetryとは

### 2-1. 概要と主な機能

[GitHub Actions OpenTelemetry](https://github.com/marketplace/actions/github-actions-opentelemetry)は、GitHub Actionsのワークフローの結果を収集し、OpenTelemetry Protocol（OTLP）エンドポイントに送信する仕組みを提供するアクションです。主な機能は次のとおりです。

- GitHub Actionsのワークフロー・ジョブ実行時間のメトリクスを収集
- ワークフロー・ジョブ・ステップのトレースを収集
- OTLP互換のバックエンドにデータを送信
- 既存のワークフローに手を加えることなくテレメトリを収集

これによりワークフロー全体を可視化し、ボトルネックやエラー箇所を容易に特定できます。また、施策前後の効果測定も簡単に行え、優先度付けやROI分析に役立ちます。

### 2-2. OpenTelemetryとは

OpenTelemetryは、トレースやメトリクス、ログなどのテレメトリを生成・管理するオープンソースフレームワークです。ベンダーロックインを排除し、JaegerやPrometheusなどの様々なツールやサービスと柔軟に連携できます。

GitHub Actions OpenTelemetryを用いれば、自身が利用している監視基盤とシームレスに統合し、収集したトレースやメトリクスを活用できます。



## 3. 導入方法

### 3-1. OTLPエンドポイントの準備

JaegerやPrometheusなど、OTLPのバックエンドを準備し、テレメトリを受信できる状態にします。OTLPのバックエンドがない方は[Google Cloud上でのOTLPエンドポイントの構築](https://github.com/paper2/github-actions-opentelemetry/blob/main/examples/google-cloud/README.md)も参考にしてください。

### 3-2. GitHub Actions Workflowの作成

[workflow_run](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows#workflow_run)イベントを利用し、完了したワークフローからメトリクスやトレースを収集する専用のワークフローを作成します。以下はサンプルの最小構成例です。

```yaml
name: Send Telemetry after Other Workflow

on:
  workflow_run:
    # 監視対象にしたいワークフロー名を指定
    workflows:
      - Check Transpiled JavaScript
      - Continuous Integration
      - CodeQL
      - Lint Codebase
    # トレースとメトリクスを作成するため、完了したワークフローを使用
    types:
      - completed

permissions:
  # プライベートリポジトリの場合に必要
  actions: read

jobs:
  send-telemetry:
    name: Send CI Telemetry
    runs-on: ubuntu-latest
    steps:
      - name: Run
        id: run
        uses: paper2/github-actions-opentelemetry@main
        env:
          OTEL_SERVICE_NAME: github-actions-opentelemetry
          OTEL_EXPORTER_OTLP_ENDPOINT: https://collector-example.com
          # OTLPエンドポイントの認証情報などをヘッダーに設定可能
          # 例:
          # New Relic: api-key=YOUR_NEWRELIC_API_KEY
          # Google Cloud Run: Authorization=Bearer <token>
          OTEL_EXPORTER_OTLP_HEADERS:
            api-key=${{ secrets.API_KEY }},other-config=value
        with:
          # ワークフロー情報の収集時に使用
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

このように設定すると、指定したワークフローが完了したタイミングで、メトリクスやトレースの情報が自動的にOTLPエンドポイントへ送信されます。メトリクスのみ・トレースのみの送信など、詳細設定は[リポジトリ](https://github.com/paper2/github-actions-opentelemetry/tree/main?tab=readme-ov-file#configuration)を参照してください。

以下にトレースやメトリクスをGoogle Cloudに送った際のサンプル画像を示します。

<figure class="figure-image figure-image-fotolife" title="Trace Sample (Cloud Trace)">[f:id:paper2parasol:20250224141113p:plain]<figcaption>Trace Sample (Cloud Trace)</figcaption></figure>
<figure class="figure-image figure-image-fotolife" title="Metrics Sample (Cloud Monitoring)">[f:id:paper2parasol:20250224141133p:plain]<figcaption>Metrics Sample (Cloud Monitoring)</figcaption></figure>

もちろん、OTLPエンドポイントを受け付けることができれば他のツールやサービスにもデータを送信し、可視化や分析を行うことが可能です。



## 4. ユースケース

### 4-1. 改善点の特定

一般的に、GitHub Actionsのワークフローは複数のジョブやステップで構成されています。ワークフロー全体の実行時間を可視化することで、どのステップに時間がかかっているかを素早く把握できます。たとえば、ビルドやテストが遅いようであれば、キャッシュの導入や並列化など具体的な改善施策を検討できます。

また、日々の実行結果をメトリクスとして蓄積しておけば、ワークフローの実行時間の変化から異常やトラブルシュートのヒントを得やすくなります。

### 4-2. 改善における効果測定
改善を実施した場合に、実際の効果を定量的に把握することができます。

私は業務でビルドキャッシュの導入によるビルド時間の短縮効果を測定するためにGitHub Actions OpenTelemetryを利用しました。

職場のリポジトリはモノレポ構成のため、同じワークフローでマトリックスストラテジーにより、複数マイクロサービスのジョブが実行されます。

まずは小さく始めるため、一つのサービスにビルドキャッシュを導入し、その効果を測定したいと考えていました。しかし、特定のジョブの実行時間を把握するためには、ログを手動で確認する必要があり、手間がかかると感じていました。

そこで、GitHub Actions OpenTelemetryを導入し、ビルドキャッシュ導入前後の実行時間をメトリクスとして記録しました。その結果、リリース時の特定ジョブの実行時間を70%削減できることがわかりました。その結果を元に、現在他のサービスへのビルドキャッシュ導入を進めています。



## 5. 仕組み

GitHub Actions OpenTelemetryはGitHub APIを使い、完了したワークフローやジョブの情報を取得し、OTLP互換エンドポイントへバッチ的にテレメトリを送信します。

<figure class="figure-image figure-image-fotolife" title="Sequential Diagram">[f:id:paper2parasol:20250224141206p:plain]<figcaption>Sequential Diagram</figcaption></figure>

OpenTelemetryでは一般的にリアルタイムの計測が多いですが、この手法なら既存ワークフローを修正せずに導入できます。もし各ジョブでのリアルタイム計測を行う場合は、ワークフローの中に追加ステップを組み込む必要があるため、既存の仕組みに影響を与えやすくなります。独立したワークフローとして後処理でデータを生成する構成は、導入時の手間を減らすメリットがあります。



## 6. まとめ

- GitHub Actions OpenTelemetryは、ワークフロー・ジョブ・ステップの実行状況を収集し、OTLP互換のバックエンドへ送信する仕組みを提供
- ボトルネックを可視化して効率的な改善を実施
- 施策前後の効果測定が容易になり、ROIや優先度を明確にできる
- 既存のワークフローに手を加える必要がほぼないため、導入が容易

GitHub Actionsの可視化や分析に課題を感じている方は、ぜひGitHub Actions OpenTelemetryを検討してみてください。オブザーバビリティを高めることで、問題の早期発見や高速な改善が実現し、開発効率やリリース品質の向上に大きく寄与するはずです。

ワークフローの状態を可視化し、継続的に改善したい方の参考になれば幸いです。

