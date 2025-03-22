# GitHub ActionsのGoogle Cloud本番権限を制限する

# 権限を制限しない場合に考えられうるリスク
- GitHub Actionsの権限を制限しない場合、リポジトリ内のコードを書き換えるだけで、本番環境にアクセスできるようになる
- カスタムGitHub Actionの検証などで、開発者が意図せず悪意のあるコードを実行してしまった場合に、本番環境にアクセスされる

# 対策
- production EnvironmentにのみGoogle Cloudの本番の権限を付与する <- 本記事の対象
- production Environmentの操作のレビューを必須にする
  - dispatch_workflowの承認を利用する方法
    - [GitHub Actions の environments を使ってデプロイ時に承認プロセスを導入する](https://zenn.dev/ore88ore/articles/github-actions-approval-flow)
  - protected branchを併用する方法
    - Environmentの機能でmainブランチのみでしか利用できないようにする
        - [[GitHub Actions] ブランチごとにジョブの実行を制御できる Environments を試してみた | DevelopersIO](https://dev.classmethod.jp/articles/github-actions-environment-secrets-and-environment-variables/)
    - mianマージに厳格なレビューが必須となる仕組みを作る
    - production Environmentをmainブランチのみで利用できるようにする

# サンプルでやること
- サンプルリポジトリを作成
- Google Cloudのプロジェクトを２つ作成
  - dev
  - prod
- Environmentsを作成
  - dev
  - prod
    - mainのみで利用可能に設定
- GitHub Actionsで両方のプロジェクトにdeployするコードを書く（時間かかるから擬似で良いかも）
  - トリガーはpush
- developブランチではdevのみが成功し、prodが失敗する
- PRマージした際にはprodも成功する