# Basic CICD Workshopのプロジェクトについて
[Spring PetClinic Sample Application](https://github.com/spring-projects/spring-petclinic) を題材に、GitLab CI/CD パイプラインの基礎を学習します。`.gitlab-ci.yml` を導入するところから実施します。
* 本ハンズオンワークショップは [e7c879e](https://github.com/spring-projects/spring-petclinic/commit/e7c879ed3abe10e446ff103887ad665ca6acf04e) のコミットに対して修正し、動作確認しています。

# GitLab.com でのハンズオン準備手順（約10分）
[GitLab.com](https://gitlab.com/explore) でハンズオンを開始するための手順です。本手順を参照しながらハンズオンを実施することになるため、本手順のページと実施用のページは、ウィンドウを分けて進めることを推奨します。

## GitLab.comアカウントの作成

会社メールのGitLab.comアカウントを持っていない方は https://gitlab.com/users/sign_up より会社メールを利用し、アカウントを作成します。アカウントを持っている方は、本セクションをスキップしてください。
* アカウント作成URL: https://gitlab.com/users/sign_up

## ハンズオン用グループへの参加
**自身のGitLab環境で実施する場合は、本セクションをスキップしてください。**
* アカウント作成 URL: https://gitlabdemo.com/invite
* Invitation Code: ハンズオン環境の事前準備メールで案内

ハンズオンは以下で示すアカウントごとに発行されるGitLab.com上のグループで行います。このグループの作成方法を紹介します。
* `https://gitlab.com/gitlab-learn-labs/environments/session-[SESSION_ID]/[RANDOM_CODE]` （例：`https://gitlab.com/gitlab-learn-labs/environments/session-26828cef/iu1x0th0`）

1. ハンズオン用グループへ招待するためのサイト、 https://gitlabdemo.com/invite へアクセスします。

2. [Invitation Code] に、GitLab社から連携されるInvitation Codeを入力し、[Provision Tranining Enviroment] をクリックします。
3. [GitLab.com Username] の入力項目が表示されるため、アカウント作成時に決めたUsernameを入力し、再度 [Provision Tranining Enviroment] をクリックします。

4. [Your GitLab Credentials] のページが表示されたら、自動的にハンズオン用のグループがGitLab.com上に作成されています。[GitLab URL] のURLをクリックし、作成されたグループへ移動します。

5. [My Test Group - …] のページが表示されたら、作成したグループへのアクセスは成功しています。ワークショップ当日にスムーズに本グループへ移動できるよう、本URLをブックマークしておくことを推奨します。  

### 注意事項
本ハンズオン用グループはハンズオン実施翌日に自動的に削除されるため、必要があればハンズオン実施後にグループやプロジェクトをエクスポート、または自身が管理するGitLab.comグループに移動しておくことを推奨します。
（ただし、Lab7、Lab9 の一部は Ultimate プランが必要です。Lab8 は Kubernetes クラスタとの連携が必要です。）

## ハンズオン用プロジェクトのインポート
### ハンズオン用グループへの移動
これからの操作は、[My Test Group - …] のグループで行うため、事前準備で保存したブックマークからグループへ移動します。

ブックマークが見当たらない方は、以下の方法で辿るのが早いです。
1. 左上の三本線のアイコンをクリックします。
2. [Groups] をクリックします。
3. 検索欄に `my test` と入力します。
4. 表示される [My Test Group - …] をクリックします。

### プロジェクトテンプレートを利用した、グループ配下での新規プロジェクトの作成
1. グループ [My Test Group - …] のページにて、[New project] のボタンをクリックします。
1. [Import project] をクリックします。
1. [Repository by URL] をクリックします。
1. プロジェクトインポートのページで [Git repository URL] に `https://gitlab.com/gitlab-learn-labs/sample-projects/basic-cicd-workshop/basic-cicd-workshop.git` を 入力します。
1. ページ下方の [Create project] のボタンをクリックします。
1. 作成されたプロジェクトのブックマージを保存します。



### プロジェクトの設定

ハンズオンの中盤でマージリクエストの承認を体験するため、マージリクエスト作成者が承認できる設定に変更します。

1. プロジェクト [Basic CICD Workshop] にて、左サイドバー [Settings] > [Merge request] をクリックします。
1. Merge requestsの設定ページの下方で、[Approval settings] > [Prevent approval by author] のチェックを外します。
1. ボタン [Save changes] をクリックします。


# Lab 一覧
各Labのページに従って順にハンズオンを進めます。
以降のハンズオンLabの内容は、上記の**インポートした各自のプロジェクト**で実施し、インポート元のテンプレートプロジェクトである https://gitlab.com/gitlab-learn-labs/sample-projects/basic-cicd-workshop/basic-cicd-workshop.git では実施しないよう注意ください。

## GitLab CI/CD 基礎
|#|内容|所要時間|解答|
| :--- | :--- | :--- | :--- |
|[Lab1](./lab1)|.gitlab-ci.yml とビルドジョブの追加|約30分|[解答](./lab1/SOLUTION.md)|
|[Lab2](./lab2)|アーティファクトの活用|約15分|[解答](./lab2/SOLUTION.md)|
|[Lab3](./lab3)|テストの実行|約15分|[解答](./lab3/SOLUTION.md)|
|[Lab4](./lab4)|ジョブの条件設定|約10分|[解答](./lab4/SOLUTION.md)|

## 時間が余った方へ
|#|内容|所要時間|解答|
| :--- | :--- | :--- | :--- |
|[Lab5](./lab5)|Tag とリリース|約20分|[解答](./lab5/SOLUTION.md)|
|[Lab6](./lab6)|品質とセキュリティスキャン|約15分|[解答](./lab6/SOLUTION.md)|
|[Lab7](./lab7)|Web アプリケーションのデプロイ|約15分|[解答](./lab7/SOLUTION.md)|
|[Lab8](./lab8)|もっと時間が余った方へのおまけ|不明|なし|

# GitLab パイプラインエディタの活用
[パイプラインエディタ](https://docs.gitlab.com/ee/ci/pipeline_editor/) では、`.gitlab-ci.yml` の構文やLintチェック、生成パイプラインのプレビュー、インクルードした外部 CI テンプレートを展開した結果を確認することができます。`.gitlab-ci.yml` で発生した構文エラーの原因を探るのに役に立ちます。

構文エラーの原因第一位は微妙なインデントのずれです。YAML ファイルはインデントに厳密であるため、注意しましょう。以下のようなインデントのずれは NG です。
```yaml
test-job:
  stage: test
   image: python:latest
  script:
```

プロジェクトの左側のメニューより、[CI/CD] > [Editor] をクリックし、ページ上部のプルダウンメニューで編集、検証対象のブランチを選択します。


# [GitLab Learn Labs](https://gitlab.com/gitlab-learn-labs) 環境情報

## GitLab バージョン
 * GitLab Enterprise Edition
    * https://gitlab.com/help

## GitLab プラン
* [Ultimate](https://about.gitlab.com/pricing/ultimate/) (Self-Managed）
    * 高度なセキュリティテスト、コンプライアンスを備えたDevSecOpsのプロセスのサポート
    * ポートフォリオ管理
    * Value Stream管理