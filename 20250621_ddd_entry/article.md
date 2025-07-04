# 概要

ドメイン駆動設計（DDD）の入門書を読み終えました。基本的な知識は網羅されており、DDDを学ぶための入門書としては非常に良い内容でした。特に既存のDDDベースのプロジェクトに参加する際の補助としては非常に強力な一冊だと感じました。

# 本書のアプローチについて

ドメイン駆動設計は大別すると以下の2つを学習することになります：

1. ソフトウェアにとって重要なドメインを抽出し、モデリングすること
2. モデリングと概念を実装に落とし込むためのパターン

本書ではあえて後者から入り、実装例と共にDDDの概念に慣れていき、ドメイン駆動設計を理解していくアプローチが取られています。C#で書かれていますが、説明としては理解できるレベルなので問題なく読めました。

開発者として抽象的な話よりも、実装例を見ながらDDDの概念を学んでいく方が理解しやすいという感覚はありました。この点で本書のアプローチは非常に実践的で良かったと思います。

# 実際の効果

本質的なドメイン駆動設計ができるようになるわけではありません。ただ、既存のDDDベースのプロジェクトに参加する際の強力な補助になるのは間違いありません。

これを理解するだけで、チーム内での会話には困らなくなりました。完全に理解できていなくても、質問もしやすくなりましたし、「このロジックはドメイン層にあるべきではないか」とか「インフラストラクチャ層にこれを置くべきではない」などの会話もできるようになりました。

一方で0からDDDで設計をできるかというと、ちょっと物足りないです。15章「ドメイン駆動設計のとびらを開こう」で本質的なことが書かれているので入門としては十分ですが、実践的な内容は少ないです。

# 印象に残った点

本質的な表現が多いため、汎用性が高いです。作者の綺麗な言葉づかいが非常に印象的でとても良かったです。一番印象に残ったのは以下の言葉です：

> 価値あるソフトウェアを構築するためには利用者の問題を見極め、解決するための最善手を常に考えていく必要があります。ドメイン駆動設計はそういった洞察をくりかしながら設計を行い、ソフトウェアの利用者を取り巻く世界と実装を結びつけることを目的としています。

あくまでもモデリングとそれを実装に紐づけるためのパターンであり、真の目的は価値あるソフトウェアを構築し、利用者の問題を継続的に解決し続けることにあります。DDDはそのための手段であり、目的ではありません。このことが冒頭で語られているのはとても好印象でした。

# まとめ

DDD入門書として、既存プロジェクトへの参加を控えている人や、DDDの基礎概念を実装と共に学びたい人には非常におすすめできる一冊でした。完全にマスターするには追加の学習が必要ですが、チーム開発でのコミュニケーションを円滑にし、設計に関する議論に参加できるレベルまでは確実に引き上げてくれます。