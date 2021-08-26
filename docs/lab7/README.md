# Lab7 - 品質とセキュリティスキャン

## 目次
[[_TOC_]]

## 目的
* 外部の `.gitlab-ci.yml` を自プロジェクトのパイプラインに取り組むための `include` の使い方を学習します。
* GitLab が提供する `.gitlab-ci.yml` テンプレートを利用することで、[Code Quality](https://docs.gitlab.com/ee/user/project/merge_requests/code_quality.html) や [各種セキュリティ](https://docs.gitlab.com/ee/user/application_security/)、[コンプライアンススキャン](https://docs.gitlab.com/ee/user/compliance/license_compliance/index.html)が手軽に開始できることを理解します。


## ハンズオンの課題
### イシューとマージリクエストの作成
イシューのタイトルを “Add quality and security test"” とし、
[Lab1 のイシューとマージリクエストの作成](../lab1/README.md#イシューとマージリクエストの作成) を参照して同様に操作します。

### テンプレートの include
Code Quality、SAST、Secret Detection、依存関係スキャン、ライセンススキャンを既存のパイプラインに追加します。

1. マージリクエストのページで、[Web IDE で開く] をクリックします。
1. Web IDE の左側のソースコードのツリーから [.gitlab-ci.yml] をクリックし、以下のテンプレートを追記します。

* テンプレート

```yaml
include:
  - template: Jobs/Code-Quality.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Jobs/Code-Quality.gitlab-ci.yml
  - template: Security/Dependency-Scanning.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/Dependency-Scanning.gitlab-ci.yml
  - template: Security/License-Scanning.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/License-Scanning.gitlab-ci.yml
  - template: Security/SAST.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/SAST.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/Secret-Detection.gitlab-ci.yml
```

### ソースコードのコミット
[Lab1 の .gitlab-ci.yml のコミット](../lab1/README.md#ソースコードのコミット) を参照して同様に操作します。

### パイプライン実行結果の確認
1. マージリクエストのページで、[#] から始まるパイプラインのIDをクリックすると、パイプラインの詳細が確認できます。
1. マージリクエストのパイプラインの `test` ステージに各種スキャンジョブが追加されていることを確認します。
    * 言語やパッケージマネージャが自動判別されて、必要なスキャンジョブが追加されています。
1. （各種ジョブの実行に時間がかかるため、10分から15分後に再度確認します。）パイプラインページの [セキュリティ] タブから検出された脆弱性の一覧を確認します。
1. （各種ジョブの実行に時間がかかるため、10分から15分後に再度確認します。）パイプラインページの [ライセンス] タブから検出されたライセンスの一覧を確認します。
1. （各種ジョブの実行に時間がかかるため、10分から15分後に再度確認します。）パイプラインページの [Code Quality] タブから検出された品質の問題の一覧を確認します。
1. （各種ジョブの実行に時間がかかるため、10分から15分後に再度確認します。）[!] から開始するマージリクエストのリンクより、マージリクエストのページに戻り、 [承認] ボタンの下方あたりに、SAST と依存関係スキャンの脆弱性検出結果の概要が出力されていることを確認します。
1. [Mark as ready] ボタンをクリックします。
1. [パイプラインが成功したときにマージ] の右の矢印 :arrow_down_small: から [今すぐマージする] ボタンを選択し、クリックします。


# ハンズオンの解答
* [Lab7 解答](../lab7/SOLUTION.md)



