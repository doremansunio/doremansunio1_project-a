# Lab2 - アーティファクトの活用

## 目次
[[_TOC_]]

## 目的
* ジョブの成果物を後のステージに渡すために、キーワード `artifacts` の活用を理解します。
* ジョブ `download-m2cache` をジョブ `mvn-package` の前のステージに追加することで、前 Lab で時間のかかったビルドジョブの実行時間の短縮を行います。以降の Lab のパイプライン実行時間も改善できます。

## ハンズオンの課題
### 完成予定のパイプライン
```mermaid
graph LR
    subgraph "Pre build"
        pre-build[Stage]
        download-m2cache(download-m2cache job)
    end
    subgraph "Build"
        pre-build[Stage] --> build[Stage]
        download-m2cache(download-m2cache job) -.-> |Job Artifact|build[Stage]
        mvn-package(mvn-package job) 
    end
```

### イシューとマージリクエストの作成
イシューのタイトルを “Add download cache job” とし、
[Lab1 のイシューとマージリクエストの作成](../lab1/README.md#イシューとマージリクエストの作成) を参照して同様に操作します。

### キャッシュダウンロードのジョブの追加
ジョブ `mvn-package` よりも前のステージでキャッシュをダンロードし、アーティファクトとして保存するジョブ `download-m2cache` を追加します。保存したアーティファクトはジョブ `mvn-package` に受け渡されます。
1. マージリクエストのページで、[Code] > [Open in Web IDE] をクリックし、Web IDE のページを開きます。
1. Web IDE の左側のソースコードのツリーから [.gitlab-ci.yml] をクリックし、以下の定義対象の内容とテンプレートにしたがってのジョブ定義を追加します。
    * 定義対象
      |対象|値|解説|
      | :--- | :--- | :--- |
      |アーティファクトのPath指定|`$CI_PROJECT_DIR/.m2/`|cloneしたリポジトリのルート直下の `.m2` ディレクトリです。|
      |ジョブ `download-m2cache` の実行ステージ名|`pre build`|ステージ `build` のための準備ステージです。|
      |追加の実行スクリプト|任意の Linux コマンド + `\|\| true`|（任意実施）キーワード `script` にセットしたコマンドがちゃんとジョブに反映されているか確認するためにも、知っている Linux コマンドをいくつかスクリプトに加えてみましょう。本ジョブの実行環境である Alpine Linux の Docker イメージには入っていないコマンドである可能性もあるため、ジョブを失敗させないよう後ろに `\|\| true` をつけます。おすすめのコマンド：`curl wttr.in/Tokyo \|\| true`。|
    * テンプレート
      ```yaml
      download-m2cache:
        stage: pre build
        image: alpine:3.14.1
        script:
          - apk --no-cache add curl
          - 'curl -o $CI_PROJECT_DIR/m2.zip "https://gitlab.com/api/v4/projects/45304220/packages/generic/m2_local_cache/latest/m2.zip"'
          - if [ -e ./m2.zip ]; then unzip m2.zip; fi
        artifacts:
          <Code here>:
            - <Code here>
      ```
    * 周辺の解説（スキップ可）
        * キーワード `script` で事前に別プロジェクトでビルドしておいたアーティファクトを API でダウンロードし、`$CI_PROJECT_DIR/.m2/` 配下に解凍する処理をしています。
          * `CI_PROJECT_DIR`: リポジトリがクローンされ、ジョブが実行される場所のフルパスです。
        * 外部からダウンロードしたアーティファクトは、本ジョブのアーティファクトとして再度保存（アップロード）しています。
        * 本来は、キャッシュ的な用途なので適切なスコープを設定したキーワード `cache` の活用がより適切ですが、ジョブ間の成果物の受け渡しを理解するために、`artifacts`を利用します。
        * なるべくジョブが高速に動作させるために、軽量な Alpine Linux の Docker イメージを指定しています。

関連 GitLab Docs のページ
* https://docs.gitlab.com/ee/ci/yaml/#needs

### ステージの追加
ジョブの追加のともない、対応するステージも定義します。 
1. `pre build` というステージを `stages` の配列として `build` の前に追加します。
    * ヒント
      ```yaml
      stages:
        - <Code here> #Add this line
        - build 

      download-m2cache:
      ...
      mvn-package:
      ...
      ```
### ソースコードのコミット
[Lab1 の .gitlab-ci.yml のコミット](../lab1/README.md#ソースコードのコミット) を参照して同様に操作します。

### パイプライン実行結果の確認
1. マージリクエストのページで、[#] から始まるパイプラインのIDをクリックし、パイプラインの詳細を確認します。
1. パイプラインのページからそれぞれのジョブをクリックし、以下のアーティファクトのアップロード、ダウンロードのログがあることを確認します。
    * download-m2cacheでのアーティファクトのアップロード
      ```log
      Uploading artifacts...
      .m2: found 6421 matching files and directories     
      Uploading artifacts as "archive" to coordinator... ok  id=235227 responseStatus=201 Created token=2HG6Vv5P
      ```

    * mvn-packageでのアーティファクトのダウンロード
      ```log
      Downloading artifacts for download-m2cache (235227)...
      Downloading artifacts from coordinator... ok        id=235227 responseStatus=200 OK token=2HG6Vv5P
      ```
      * ジョブ `mvn-package` については、[Duration:] の値を確認し、前 Lab より高速化され、1分程度で完了していることを確認します。
1. マージリクエストのタイトルの右側の [︙] > [Mark as ready] をクリックします。
1. [Merge] ボタンをクリックし、マージリクエストをマージします。（本 Lab 以降は承認ボタンのクリックは任意とします。）


# ハンズオンの解答
* [Lab2 解答](../lab2/SOLUTION.md)



