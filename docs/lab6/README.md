# Lab6 - Tag とリリース

## 目次
[[_TOC_]]

## 目的
* git の tag をトリガにそのコミットの断面でビルド、テスト、Maven Deploy をし、Maven リポジトリに登録したファイルを GitLab リリース機能で管理します。これにより、トレーサビリティを保ちつつ、一意的に正式版の成果物を管理します。
* キーワード `rules` を活用し、`git tag` をトリガとして動作するジョブの条件を学習します。
* キーワード `release` を活用し、`.gitlab-ci.yml` でリリースを作成する方法を学習します。


## ハンズオンの課題
### イシューとマージリクエストの作成
イシューのタイトルを “Add release job” とし、
[Lab1 のイシューとマージリクエストの作成](../lab1/README.md#イシューとマージリクエストの作成) を参照して同様に操作します。

### mvn-deployのジョブの条件変更
前の Lab で実施したデフォルトブランチの場合にジョブ実行する条件に加え、tag をつけられた際にもジョブ `mvn-deploy` を実行するようします。

1. マージリクエストのページで、[Web IDE で開く] をクリックします。
1. Web IDE の左側のソースコードのツリーから [.gitlab-ci.yml] をクリックし、以下の定義対象の指示とテンプレートにしたがってのジョブ定義を追加します。

* 定義対象

|対象|値|解説|
| :--- | :--- | :--- |
|Tag された時にも実行されるようにジョブの **OR 条件**の追加|`$CI_COMMIT_TAG`|コミットに tag がつけられていればタグ名が環境変数にセットされます。|

* テンプレート


```yaml
mvn-deploy:
  stage: post build
  image: maven:3.8.1-openjdk-17-slim
  before_script:
    - apt update && apt install git -y
  script:
    - ./mvnw deploy -s ci_settings.xml -Dmaven.test.skip=true
  rules:
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH' # この行を修正
```

### releaseのジョブの追加
`.gitlab-ci.yml` に ジョブ `release` を追加します。

* 定義対象

|対象|値|解説|
| :--- | :--- | :--- |
|リリースをするためのジョブキーワード|`release`||
|アセット（リリース物）へのリンク名|`'spring-petclinic jar'`|ハンズオンで利用している Web アプリの jar を示す表示名を付けます。|
|Tag された場合のみで実行される条件の設定|（条件文を考えましょう）|-|

* テンプレート


```yaml
release:
  stage: release
  image: registry.gitlab.com/gitlab-org/release-cli:latest
  script:
    - echo running release_job for '$CI_COMMIT_TAG'
  <Code here>:
    name: 'Release $CI_COMMIT_TAG'
    description: 'Created using the release-cli for $CI_COMMIT_TAG'
    tag_name: '$CI_COMMIT_TAG'
    milestones:
      - $CI_COMMIT_TAG
    assets:
      links:
        - name: <Code here>
          url: '$CI_API_V4_URL/projects/$CI_PROJECT_ID/packages/maven/org/springframework/samples/spring-petclinic/$CI_COMMIT_TAG/spring-petclinic-$CI_COMMIT_TAG.jar'
        - name: 'spring-petclinic pom'
          url: '$CI_API_V4_URL/projects/$CI_PROJECT_ID/packages/maven/org/springframework/samples/spring-petclinic/$CI_COMMIT_TAG/spring-petclinic-$CI_COMMIT_TAG.pom'
  rules:
    - if: <Code here>
```

### ステージの追加
テスト済みのソースコードのみをリモートリポジトリへ登録するようにするため、ステージ `test` の後にジョブが実行されるようにします。
1. `stages:` の定義が以下になるように、`release` というステージを `post build` の後に追加します。

```yaml
stages:
  - pre build
  - build
  - test
  - post build
  - release  #この行を追加
```

### pom.xml の修正
元々リモートリポジトリに登録する際の、jar ファイルバージョンはハードコーディングされていましたが、`$CI_COMMIT_REF_NAME` が渡るようにします。`$CI_COMMIT_REF_NAME` は、コミットに Tag があれば Tag 名がセットされ、そうでない場合はブランチ名（main など）がセットされるため、Tag とブランチの両方の場合にも値が入っているため便利です。
1. 8行目付近を以下のように変更します。

```xml
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.springframework.samples</groupId>
  <artifactId>spring-petclinic</artifactId>
  <version>${env.CI_COMMIT_REF_NAME}</version>  <!-- この行を変更 ->
```

### ソースコードのコミット
[Lab1 の .gitlab-ci.yml のコミット](../lab1/README.md#ソースコードのコミット) を参照して同様に操作します。

### マイルストーンの作成
リリースにマイルストーンを関連付けるために、事前に Tag 名と一致するマイルストーンを作成します。ここでは省略しますが、実際にはマイルストーンにイシューを関連付けさせて、イシュー、マイルストーン、リリース間のトレーサビリティを確保します。
1. 左側メニューの [イシュー] > [マイルストーン] をクリックします。
1. [新しいマイルストーン] ボタンをクリックします。
1. [タイトル] に "1.0" と入力します。
1. [Start Date] と [Due Date] は任意の日付を入力します。
1. [マイルストーンを作成] ボタンをクリックします。[1.0] のマイルストーンが作成されます。

### パイプライン実行結果の確認
変更内容の特性上、フィーチャーブランチ（マージリクエスト）でのパイプラインでは条件の正しさが検証できないため、マージ後のmain ブランチで検証を確認します。
1. マージリクエストのパイプラインが成功し、`mvn-deploy` と `release` のジョブ存在しないことを確認します。
1. [Mark as ready] ボタンをクリックします。
1. [マージ] ボタンをクリックし、マージリクエストをマージします。
1. 左側メニューの [リポジトリ] > [タグ] をクリックします。
1. [タグ一覧] のページで、[新しいタグ] ボタンをクリックします。
1. [Tag name]に "1.0" と入力し、[Create from] が "main" になっていることを確認し、[タグ作成] ボタンをクリックします。
1. 左側メニューの [CI/CD] > [パイプライン] をクリックします。
1. パイプライン一覧の最上部のパイプラインを確認します。
    * [コミット] の列に、"1.0" のTag名が付けられていることを確認します。
    * [#] から開始するパイプラインの番号のリンクをクリックし、`mvn-deploy` と `release` のジョブが実行されることを確認します。
1. パイプラインが完了したら、左側メニューの [Deployments] > [Release] をクリックし、CI ジョブにより作成されたリリースを確認します。
    * マイルストーン名
    * [Source code (zip)] のリンクからtagの断面のソースコードの一式がダウンロードできること。
    * [spring-petclic jar] のリンクが jar ファイルダウンロードできること。
    * Tag名

# ハンズオンの解答
* [Lab6 解答](../lab6/SOLUTION.md)



