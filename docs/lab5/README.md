# Lab5 - ジョブの条件設定

## 目次
[[_TOC_]]

## 目的
* キーワード `rules` を活用し、main ブランチなどのデフォルトブランチのみでジョブが実行する方法を学習します。


## ハンズオンの課題
### イシューとマージリクエストの作成
イシューのタイトルを “Deploy jar only in main branch” とし、
[Lab1 のイシューとマージリクエストの作成](../lab1/README.md#イシューとマージリクエストの作成) を参照して同様に操作します。

### mvn-deployのジョブの条件追加
デフォルトブランチであるmainブランチの場合のみ、ジョブ `mvn-deploy` を実行するように条件を追加します。

1. マージリクエストのページで、[Web IDE で開く] をクリックします。
1. Web IDE の左側のソースコードのツリーから [.gitlab-ci.yml] をクリックし、以下の定義対象の指示とテンプレートにしたがってのジョブ定義を追加します。

* 定義対象

|対象|値|解説|
| :--- | :--- | :--- |
|条件|`'$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'`|ジョブの実行ブランチがデフォルトブランチ（通常は main）であれば `true` になる条件です。|


* テンプレート


```yaml
mvn-deploy:
  stage: post build
  image: maven:3.8.1-openjdk-17-slim
  before_script:
    - apt update && apt install git -y
  script:
    - ./mvnw deploy -s ci_settings.xml -Dmaven.test.skip=true
  <Code here>: #この行を追加し、修正
    - <Code here>: <Code here> #この行を追加し、修正
```

### ソースコードのコミット
[Lab1 の .gitlab-ci.yml のコミット](../lab1/README.md#ソースコードのコミット) を参照して同様に操作します。

### パイプライン実行結果の確認
変更内容の特性上、フィーチャーブランチ（マージリクエスト）でのパイプラインでは条件の正しさが完全に検証できないため、`mvn-deploy` のジョブが存在しないことを確認したらマージリクエストをマージします。
1. マージリクエストのパイプラインに `mvn-deploy` のジョブが存在しないことを確認します。
1. [Mark as ready] ボタンをクリックします。
1. [パイプラインが成功したときにマージ] の右の矢印 :arrow_down_small: から [今すぐマージする] ボタンを選択し、クリックします。
1. main ブランチ側のパイプラインには `mvn-deploy` のジョブが存在することを確認できたら、正しく定義できています。
1. main ブランチ側のパイプラインが完了したら、左側メニューの [パッケージとレジストリ] > [パッケージとレジストリ] をクリックします。
1. パッケージレジストリのページで、[org/springframework/samples/spring-petclinic] (2.4.5) の更新日時が直近になっていることを確認できれば、main ブランチのパイプラインによりパッケージが更新されたと理解できます。


# ハンズオンの解答
* [Lab5 解答](../lab5/SOLUTION.md)



