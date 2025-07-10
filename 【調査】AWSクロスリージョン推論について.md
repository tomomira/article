# AWS Bedrock クロスリージョン推論 調査レポート

## 1. はじめに

本レポートは、AWS Bedrockの「クロスリージョン推論（Cross-Region Inference）」機能について、その概要、メリット・デメリット、そして特にセキュリティやデータプライバシーに関する懸念点を調査し、わかりやすく解説することを目的とします。

近年、AWS Bedrockで提供される最新の高性能な生成AIモデルは、特定のリージョン（主に米国）でのみ利用可能となるケースが増えています。このような状況下で、日本国内からでも最新モデルを活用するためには、クロスリージョン推論の理解が不可欠です。本レポートが、海外リージョン利用の判断材料となることを目指します。

## 2. クロスリージョン推論とは

クロスリージョン推論は、Amazon Bedrockの標準機能の一つです。単一のAPIエンドポイント（推論プロファイルID）を利用するだけで、推論リクエストがAWSの判断に基づき、複数のAWSリージョンへ自動的にルーティングされる仕組みです。

主な目的は、特定のリージョンでトラフィックが急増したり、コンピューティングリソースが一時的に不足したりした場合でも、他のリージョンの利用可能なリソースを自動的に活用し、アプリケーション全体のスループット（処理能力）と可用性（安定稼働）を向上させることにあります。

利用者は、コード内でモデルIDを指定する箇所で、通常のモデルIDの代わりに「推論プロファイル（Inference Profile）」と呼ばれる専用のID（例：`us.anthropic.claude-3-5-sonnet-20240620-v1:0`）を指定するだけで、この機能の恩恵を受けることができます。

## 3. 推奨アーキテクチャ：国内データストアとの分離

クロスリージョン推論を安全かつ効果的に活用するための推奨アーキテクチャは、**「推論処理（コンピュート）のみをクロスリージョンとし、データの保管（ストレージ）は国内リージョンに固定する」**という考え方です。

-   **対象範囲を限定する**
    -   **クロスリージョン推論の対象：** LLMや埋め込みモデルの呼び出し（`InvokeModel` APIなど）
    -   **国内リージョンに固定するもの：**
        -   **データストレージ：** Amazon S3バケット、Amazon Aurora、DynamoDBなどのデータベース
        -   **アプリケーションロジック：** Amazon EC2、AWS Lambdaなどで動作するビジネスロジック
        -   **その他全てのAWSサービス**

この構成により、プロンプトや生成結果などの**一時的なデータ**は処理のために海外リージョンに転送される可能性がありますが、企業の機密情報や個人情報を含む**永続的なデータは、常に指定した国内リージョン（例：東京リージョン）から移動しません。**

これは、データがどこに保存されるかを顧客が完全にコントロールできるというAWSの基本原則に沿ったものであり、データレジデンシー（データ主権）の要件を満たしつつ、最新AIモデルの性能を享受するための非常に有効なベストプラクティスです。

## 4. メリット

- **スループットと可用性の向上**: 複数リージョンの潤沢なリソースを利用できるため、大規模なリクエストにも対応しやすく、システム全体の処理能力が向上します。AWSによると、単一リージョンのデフォルト上限の最大2倍のスループュットを実現可能です。
- **耐障害性の強化**: リクエスト先のリージョンで万が一障害が発生したり、容量が逼迫したりした場合でも、自動的に正常な別のリージョンにリクエストが転送（フェイルオーバー）されます。これにより、アプリケーションの安定性が大幅に向上します。
- **追加コスト不要**: クロスリージョン推論の利用自体に追加料金はかかりません。また、リージョン間で発生するデータ転送料金も不要で、APIを呼び出した元のリージョン（ソースリージョン）と同じ料金体系（トークン単価）が適用されます。
- **実装の容易さ**: 本来であれば複雑になりがちな、複数リージョンにまたがる負荷分散や障害対応のロジックを、開発者が自前で実装する必要がありません。

## 5. デメリットと考慮事項

- **レイテンシー（遅延）**: リクエストがAPIを呼び出したリージョンとは異なるリージョンで処理された場合、通信のためにわずかな遅延（AWSのテストでは数十ミリ秒程度）が追加で発生する可能性があります。リアルタイム性が極めて重要な処理に利用する際は注意が必要です。
- **対象モデル・リージョンの制約**: この機能は、全てのモデル、全てのリージョンで利用できるわけではありません。主にAnthropic社のClaudeシリーズのような最新モデルから対応が始まり、AWSが定義した地理的なグループ（例：米国、欧州、アジアパシフィック）内での利用に限られます。
  - **補足**: 日本の東京リージョン（`ap-northeast-1`）はアジアパシフィック（APAC）グループに含まれており、APAC対応の推論プロファイルを利用することが可能です。

## 6. セキュリティとデータプライバシー：AWSの責任範囲

海外リージョンを利用する上で最も懸念されるセキュリティとデータプライバシーについて、AWSが責任を持つ範囲（クラウド自体のセキュリティ）をQ&A形式で解説します。

### AWSのセキュリティレベルについて

AWSは、世界で最も厳しいセキュリティ要件を満たすように設計された堅牢なクラウドインフラを提供しています。そのセキュリティレベルは、多数の第三者機関による認証によって客観的に証明されています。

特に日本国内においては、政府がクラウドサービスのセキュリティレベルを評価する制度**「ISMAP（イスマップ：Information system Security Management and Assessment Program）」**に登録されています。これは、AWSが日本政府の定める厳しいセキュリティ要求事項を満たしていることを意味し、政府機関や重要インフラ企業などが安心して利用できる基準をクリアしていることの証明となります。

その他にも、国際的なセキュリティ認証である`ISO 27001`、`ISO 27017`（クラウドセキュリティ）、`ISO 27018`（クラウドにおける個人情報保護）など、数多くのコンプライアンス認証を取得しており、世界最高水準のセキュリティとプライバシー保護体制を維持しています。

---

### **Q1. プロンプトなどのデータが、海外リージョンに保存されてしまうのではないか？**

**回答：いいえ、データが海外のリージョンに永続的に保存されることはありません。データは原則として、APIを呼び出した国内リージョン（東京リージョンなど）に保存されます。**

**解説：**
推論リクエストは、APIを呼び出した**元のリージョン（ソースリージョン）**を起点とします。例えば、日本のアプリケーションから東京リージョン（`ap-northeast-1`）のエンドポイントを呼び出した場合、ここがソースリージョンとなります。

クロスリージョン推論では、処理のためにプロンプトや生成結果などのデータが、同じ地理的グループ内（例：東京リージョンからならAPAC内の別リージョン）に**一時的に転送され、処理される**ことがあります。しかし、これはあくまで計算処理のための一時的なものであり、CloudTrailの監査ログやBedrockのモデル呼び出しログなど、お客様のコンテンツに関連する**データが永続的に保存される場所は、常にソースリージョン**です。

したがって、東京リージョンから利用した場合に、データが米国や欧州のサーバーに意図せず保存され続ける、といったことはありません。お客様はデータの保存場所を自ら選択し、コントロールし続けることができます。

> **【引用元資料】**
> - **Getting started with cross-region inference in Amazon Bedrock** (セクション: Networking and data logging)
>   - *Log entries will continue to be made in the original source region for both Amazon CloudWatch and AWS CloudTrail, and there will be no additional logs in the re-routed region.*
> - **Increase throughput with cross-Region inference**
>   - *Although the data remains stored only in the source Region, your input prompts and output results might move outside of your source Region during cross-Region inference.*
> - **Japan Data Privacy - Amazon Web Services (AWS)** (セクション: FAQs > Where will customer content be stored?)
>   - *AWS customers choose the AWS Region(s) where their content will be stored... AWS does not move customer content outside of the customer’s chosen Region(s)*

---

### **Q2. 入力したプロンプトや機密情報が、AIモデルの学習に利用されてしまうのではないか？**

**回答：いいえ、お客様のデータがAIモデルの学習に利用されることは一切ありません。**

**解説：**
AWSは、Amazon Bedrockのサービス規約および[データ保護のページ](https://aws.amazon.com/bedrock/data-protection/)において、「お客様のコンテンツ（プロンプトや生成結果）を、基盤モデルの改善やトレーニングに使用することはない」と明確に約束しています。これは、お客様のデータの所有権と機密性を保護するための重要なポリシーです。

このポリシーは、クロスリージョン推論を利用するかどうかにかかわらず、Bedrockの全ての利用者に等しく適用されます。したがって、海外リージョンで処理された場合でも、データが学習に利用される心配はありません。

> **【引用元資料】**
> - **Getting started with cross-region inference in Amazon Bedrock** (セクション: Key Considerations > Regulations, Compliance, and Data Residency)
>   - *Although none of the customer data is stored in any region when using cross-region inference, it’s important to consider that your inference data can be processed and transmitted across multiple pre-configured regions as defined in the inference profile.* (これはデータが学習に使われるという意味ではなく、あくまで処理のために転送されることを示しています。学習への利用否定はBedrockの基本ポリシーに基づきます。)
> - **Amazon Bedrock FAQs** (Data Privacy and Securityセクション)
>   - AWSの公式FAQページでは、顧客データがモデル学習に利用されないことが明記されています。（直接の引用文は検索結果にありませんが、これはBedrockの基本的なデータ保護方針です）

---

### **Q3. 海外リージョンとのデータ通信は安全なのか？**

**回答：はい、通信経路はAWSの専用グローバルネットワーク上で暗号化され、安全に保護されています。**

**解説：**
リージョン間のデータ転送は、通常のインターネット公衆網を経由しません。通信はすべて、物理的に隔離され、高度に監視された**AWS専用のグローバルネットワークバックボーン**を通じて行われます。この通信は**常に暗号化**されており、盗聴や改ざんといった一般的なネットワーク攻撃のリスクから保護されています。

> **【引用元資料】**
> - **Getting started with cross-region inference in Amazon Bedrock** (セクション: Networking and data logging)
>   - *The AWS-to-AWS traffic flows, such as Region-to-Region... will always traverse AWS-owned and operated backbone paths... all cross-region inference requests will remain on the AWS backbone at all times.*
> - **Increase throughput with cross-Region inference**
>   - *All data will be transmitted encrypted across Amazon’s secure network.*

## 7. 利用者側で実施すべきセキュリティ対策とベストプラクティス

AWSがクラウド自体のセキュリティを確保する一方で、その上でアプリケーションを構築する利用者にも、適切なセキュリティ対策を講じる責任があります（**責任共有モデル**）。以下に、Amazon Bedrockを利用する上での、利用者側のセキュリティベストプラクティスを挙げます。

### a. IAMによる最小権限の原則の徹底

**概要：**
ユーザーやアプリケーション（IAMロール）には、タスクの実行に必要最小限の権限のみを付与します。これにより、万が一認証情報が漏洩した場合でも、被害を最小限に抑えることができます。

**ベストプラクティス：**
- **モデルの利用を制限する：** 全てのモデルを無条件に利用可能にするのではなく、アプリケーションで利用するモデル（例：`anthropic.claude-3-sonnet-v1:0`）や推論プロファイル（例：`us.anthropic.claude-3-sonnet-v1:0`）のみをIAMポリシーの`Resource`で明示的に許可します。
- **管理操作と利用操作を分離する：** モデルの有効化や管理を行うIAMユーザー・ロールと、モデルを呼び出して推論を実行するアプリケーション用のIAMロールの権限を分離します。
- **例：特定の推論プロファイルのみ呼び出しを許可するIAMポリシー**
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": "bedrock:InvokeModel",
        "Resource": "arn:aws:bedrock:us-east-1::inference-profile/us.anthropic.claude-3-5-sonnet-20240620-v1:0"
      }
    ]
  }
  ```

### b. VPCエンドポイントによるプライベート接続の利用

**概要：**
VPCエンドポイント（AWS PrivateLink）を利用することで、Amazon BedrockへのAPI呼び出しをインターネット経由ではなく、AWSネットワーク内で完結させることができます。

**ベストプラクティス：**
- **セキュアな通信経路の確保：** アプリケーションが動作するVPC（Virtual Private Cloud）内にBedrock用のVPCエンドポイントを作成します。これにより、全てのAPI通信がパブリックなインターネットに出ることがなくなり、セキュリティが大幅に向上します。
- **エンドポイントポリシーによるアクセス制御：** VPCエンドポイント自体にポリシーを設定し、特定のVPC内から、特定のIAMロールのみがBedrockを呼び出せるようにさらにアクセスを制限します。

### c. ログの有効化と監視

**概要：**
誰が、いつ、どのリソースに、何をしたか、を追跡・監査できるように、ログを適切に設定・管理します。

**ベストプラクティス：**
- **AWS CloudTrailを有効化する：** AWSアカウント内の全てのAPI呼び出し履歴を記録します。これにより、不正な操作の検知や、インシデント発生時の原因調査が可能になります。
- **Bedrockモデル呼び出しログを有効化する：** プロンプトや生成結果をS3バケットやCloudWatch Logsに保存し、監査や性能分析に役立てます。このログには機密情報が含まれる可能性があるため、保存先のS3バケットやロググループへのアクセス権限は最小限に絞り、暗号化を有効にすることが強く推奨されます。

> **【引用元資料】**
> - **Implementing least privilege access for Amazon Bedrock**
> - **Security in Amazon Bedrock**
> - **Identity and access management for Amazon Bedrock**

## 8. まとめ

AWS Bedrockのクロスリージョン推論は、最新モデルを高いパフォーマンスと安定性で利用するための、非常に強力かつ有効な機能です。

利用にあたってはレイテンシーなどの考慮点はあるものの、最大の懸念点であるセキュリティとデータプライバシーについては、**「推論はクロスリージョン、データは国内」というアーキテクチャ**を採用することで、AWSの堅牢な仕組みと合わせて万全の対策を講じることが可能です。

- **データは海外に永続保存されない（保存場所は常に国内のソースリージョン）**
- **データがAIの学習に利用されることはない**
- **リージョン間通信は暗号化された専用線で保護される**
- **AWSは日本の政府認定セキュリティ基準(ISMAP)に準拠している**

これらに加え、利用者側でもIAMによる最小権限化やVPCエンドポイントの利用といったベストプラクティスを実践することで、責任共有モデルに則った多層的なセキュリティを確保し、日本国内のプロジェクトにおいても安心してBedrockの機能を最大限に活用できます。

## 9. 引用元

- [Getting started with cross-region inference in Amazon Bedrock | AWS Machine Learning Blog](https://aws.amazon.com/blogs/machine-learning/getting-started-with-cross-region-inference-in-amazon-bedrock/)
- [Increase throughput with cross-Region inference - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html)
- [Supported Regions and models for inference profiles - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference-support.html)
- [Information System Security Management and Assessment Program (ISMAP) - AWS](https://aws.amazon.com/compliance/ismap/)
- [Japan Data Privacy - Amazon Web Services (AWS)](https://aws.amazon.com/compliance/japan-data-privacy/)
- [Implementing least privilege access for Amazon Bedrock | AWS Security Blog](https://aws.amazon.com/blogs/security/implementing-least-privilege-access-for-amazon-bedrock/)
- [Security in Amazon Bedrock - Amazon Bedrock User Guide](https://docs.aws.amazon.com/bedrock/latest/userguide/security.html)
- [Identity and access management for Amazon Bedrock - Amazon Bedrock User Guide](https://docs.aws.amazon.com/bedrock/latest/userguide/security-iam.html)
- [Enable Amazon Bedrock cross-Region inference in multi-account environments | AWS Machine Learning Blog](https://aws.amazon.com/blogs/machine-learning/enable-amazon-bedrock-cross-region-inference-in-multi-account-environments/) 