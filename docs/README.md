# Basic CI/CD Workshop ja のプロジェクトについて
[Spring PetClinic Sample Application](https://github.com/spring-projects/spring-petclinic) を題材に、GitLab CI/CD パイプラインの基礎を学習します。`.gitlab-ci.yml` を導入するところから実施します。
* 本ハンズオンワークショップは [e7c879e](https://github.com/spring-projects/spring-petclinic/commit/e7c879ed3abe10e446ff103887ad665ca6acf04e) のコミットで動作確認しています。

# GitLab Training Cloud でのハンズオン準備手順（約10分）
[GitLab Training Cloud](https://workshop.gitlabtraning.cloud) でハンズオンを開始するための手順です。
## 注意事項
GitLab Training Cloud のアカウントは、ワークショップ終了の1〜2日後の間に自動で削除されるため、ハンズオン用のプロジェクトを[エクスポート](https://docs.gitlab.com/ee/user/project/settings/import_export.html#export-a-project-and-its-data)することを推奨します。
## 前提条件
* GitLab Training Cloud のアカウントを取得し、サインイン済みであること。
* ユーザの言語設定が日本語になっていること。（Lab の手順は日本語 UI を前提としています。）

## ハンズオン用プロジェクトのインポート
### グループ [My Test Group - USERNAME] への移動
GitLab Training Cloud 用アカウントの作成で追加したブックマークからでも移動可能です。
1. ブラウザの新規のウィンドウまたはタブで、画面の左上で、 [Menu] > [Groups] > [所属グループ] の順にをクリックします。
1. [グループ] の画面で、[Training Users] > [Session INVITATION_CODE] の順にクリックでグループのツリーを展開し、 [My Test Group - USERNAME] をクリックします。（INVITATION_CODE、USERNAMEはランダムな英数字です。）
1. グループ [My Test Group - USERNAME] に遷移できることを確認します。

### プロジェクトテンプレートを利用した、グループ配下での新規プロジェクトの作成
1. グループ [My Test Group - USERNAME] へ移動した状態でGitLabプロジェクトを作成します。
1. [新規プロジェクト] ボタンをクリックします。
1. [Create new project] ページで、[テンプレートから作成] をクリックします。
1. [テンプレートから作成] ページで、[インスタンス] タブをクリクします。
1. [Basic CICD Workshop ja] の [テンプレートを使用] をクリックします。
1. [プロジェクト名] にプロジェクトの名前に "Basic CICD Workshop" を入力し、[プロジェクトを作成] をクリックします。
1. しばらく [インポート中です] と表示されたあと、プロジェクトのトップページが表示され、リポジトリのファイル構成を確認できます。
1. 新規作成したプロジェクト（Basic CICD Workshop）には、現在手順に従っているテンプレートプロジェクト（Basic CICD Workshop ja）と同じ内容がファイルがインポートできているため、以降の手順は新規作成したプロジェクト（ Basic CICD Workshop）の [docs/README.md] にしたがって、手順を進めます。
    * 手順用確認用のウィンドウと手順実施用のウィンドウを分けて進めることを推奨します。

<!---
### プロジェクトの設定
ハンズオン中にマージリクエストの承認ボタンを表示させるため、以下の事前設定を行います。

1. プロジェクト [Basic CICD Workshop] にいる状態で、左側メニューより [設定] > [一般] をブラウザの新規のウィンドウまたはタブで開きます。
1. プロジェクト [Basic CICD Workshop] にいる状態で、左側メニューより [設定] > [一般] をクリックします。
1. [全般設定] のページで、[Merge request (MR) approvals] の右側の [展開] ボタンをクリックします。
1. [Approval settings] > [Prevent MR approvals by the author.] のチェックを外します。
1. [変更を保存] をクリックし、設定を保存します。
-->

# Lab 一覧
ワークショップ時間中の完了目標は Lab5までです！
|#|内容|所要時間|
| :--- | :--- | :--- |
|[Lab1](lab1)|`.gitlab-ci.yml` とビルドジョブの追加|約30分|
|[Lab2](lab2)|アーティファクトの活用|約15分|
|[Lab3](lab3)|テストの実行|約15分|
|[Lab4](lab4)|パッケージレジストリへの登録|約15分|
|[Lab5](lab5)|ジョブの条件設定|約10分|
|[Lab6](lab6)|Tag とリリース|約20分|
|[Lab7](lab7)|品質とセキュリティスキャン|約15分|
|[Lab8](lab8)|Web アプリケーションのデプロイ|約15分|
|[Lab9](lab9)|時間が余った方へのおまけ|不明|

# [GitLab Training Cloud](https://workshop.gitlabtraning.cloud) 環境情報

## GitLab バージョン
 * GitLab Enterprise Edition
    * https://workshop.gitlabtraining.cloud/help

## GitLab プラン
* [Ultimate](https://about.gitlab.com/pricing/ultimate/) (Self-Managed)
    * 高度なセキュリティテスト、コンプライアンスを備えたDevSecOpsのプロセスのサポート
    * ポートフォリオ管理

## GitLab Runner
* インスタンスレベルで共有
* Executors のタイプ：[Docker Machine executor](https://docs.gitlab.com/runner/executors/#docker-machine-executor) 
    * オートスケールされた Docker エンジン上でジョブが実行されます。


## Kubernetes クラスタ（アプリのデプロイ環境）
* インスタンスレベルで GitLab インスタンスと連携
* Base domain：ci.workshop.gitlabtraining.cloud
* Kubernetes cluster name：workshop-gitlab-ci-cluster