# Lab8 もっと時間が余った方へのおまけ
以下の課題に挑戦してみましょう。
## ユニットテストの失敗時の確認
Lab3 ではユニットテストは成功しますが、テストコードかアプリのコードを修正することで故意にジョブを失敗させ、失敗した場合のマージリクエストとパイプラインのテスト結果の表示を見てみましょう。
例えば、"src/main/java/org/springframework/samples/petclinic/model/Person.java#setLastName" の`lastName` を `firstName` と入れ替えたりするとテストが失敗します。

## セキュリティの脆弱性の解消
セキュリティの脆弱性の解消するフローを体験します。
1. 脆弱性レポートから [Improper Check for Unusual or Exceptional Conditions in net.minidev/json-smart] の脆弱性の詳細ページで内容を確認し、[イシューの作成] ボタンから関連イシューの作成をします（[This issue is confidential and should only be visible to team members with at least Reporter access.] のチェックは外します）。
2. さらに、イシューから関連するマージリクエストを作成します。
3. `pom.xml` の 13行目あたりの `<version>` を "2.5.0" に変更すると解消されます。（ただし、`json-smart` はテストフェーズで使用するモジュールであるため、悪用される可能性は低いと考えます。）

```xml
...
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.5.0</version>
  </parent>
...
```
## コンテナスキャンのジョブの追加
本ハンズオン Lab の `.gitlab-ci.yml` 直接にコンテナスキャンのジョブ定義をインクルードすると、ステージ `test`で実行されます。Docker イメージをビルドするジョブ `docker-build` が ステージ `test`のあとに設定されているため、コンテナスキャンは失敗します。コンテナスキャンのジョブのテンプレートを確認し、定義の一部をオーバーライドすることで、ジョブ `docker-build` のあとに実行されるように定義を変更しましょう。
```yaml
include:
  - template: Security/Container-Scanning.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/Container-Scanning.gitlab-ci.yml

```

