# Basic CICD Workshop ja のプロジェクトについて
[Spring PetClinic Sample Application](https://github.com/spring-projects/spring-petclinic) を題材に、GitLab CI/CD パイプラインの基礎を学習します。`.gitlab-ci.yml` を導入するところから実施します。
* 本ハンズオンワークショップは [e7c879e](https://github.com/spring-projects/spring-petclinic/commit/e7c879ed3abe10e446ff103887ad665ca6acf04e) のコミットに対して修正し、動作確認しています。

# GitLab Training Cloud でのハンズオン準備手順（約10分）
[GitLab Training Cloud](https://workshop.gitlabtraning.cloud) でハンズオンを開始するための手順です。本手順を参照しながらハンズオンを実施することになるため、本手順のページと実施用のページは、ウィンドウを分けて進めることを推奨します。


## GitLab Training Cloud用アカウントの作成
* アカウント作成URL: https://gitlabdemo.com/invite
* Invitation Code: ハンズオン環境の事前準備メールで案内

1. アカウント作成URLで [Invitation Code] を入力し、[Redeem and Create Account] をクリックします。
2. 次のページで、GitLab Training Cloudの [GitLab URL]（[https://workshop.gitlabtraining.cloud]）、
 [Username] 、[Password] が表示されます。
3. [Download Credentials] をクリックし、認証情報を保存し、再度 GitLab Training Cloud へサインインが必要な場合に利用します。
4. [My Group] をクリックし、GitLab Training Cloud のサインインページが表示されます。
5. GitLab Training Cloud のサインインページで、2. の [Username] 、[Password] を入力し、GitLab Training Cloud の自分専用のグループ（My Test Group - `USERNAME`）が表示されることを確認します。(このページを以後の作業やワークショップ当日のためにブックマークしておきます。`USERNAME` はランダムな英数字です。）
### 注意事項
GitLab Training Cloud のアカウントは、[ワークショップ終了の1〜2日後の間に自動で削除されます](https://about.gitlab.com/handbook/customer-success/demo-systems/#:~:text=Students%20have%20access%20to%20the%20Training%20Cloud%20until%2000%3A00%20UTC%20two%20days%20following%20the%20workshop%20(4%3A00pm%20PT%20on%20the%20day%20after%20the%20workshop).)。それ以降もハンズオンの演習を進めたい方は、一度ハンズオン用の GitLab プロジェクトを[エクスポート](https://docs.gitlab.com/ee/user/project/settings/import_export.html#export-a-project-and-its-data)し、[GitLab.com](https://gitlab.com/) 等に該当プロジェクトを[インポート](
https://docs.gitlab.com/ee/user/project/settings/import_export.html#import-the-project)することを推奨します。（Lab7、Lab9 の一部は Ultimate プランが必要です。Lab8 は Kubernetes クラスタとの連携が必要です。）


## ユーザ表示言語の日本語への変更
英語 UI のままで問題は動作上はありませんが、Lab の手順は日本語 UI を前提としています。
1. サインイン後のページの右上のユーザのアイコンをクリックし、[Preferences] をクリックします。
2. [Localization] > [Language] で [Japanese - 日本語] を選択し、[Save Changes] ボタンをクリックします。
3. ブラウザのページを更新し、表示言語が日本語に変更されることを確認します。


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
1. [プロジェクト名] に "Basic CICD Workshop" を入力し、[プロジェクトを作成] をクリックします。
1. しばらく [インポート中です] と表示されたあと、プロジェクトのトップページが表示され、リポジトリのファイル構成を確認できます。

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
## GitLab CI/CD 入門
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

# [GitLab Training Cloud](https://workshop.gitlabtraning.cloud) 環境情報

## GitLab バージョン
 * GitLab Enterprise Edition
    * https://workshop.gitlabtraining.cloud/help

## GitLab プラン
* [Ultimate](https://about.gitlab.com/pricing/ultimate/) (Self-Managed）
    * 高度なセキュリティテスト、コンプライアンスを備えたDevSecOpsのプロセスのサポート
    * ポートフォリオ管理

## GitLab Runner
* インスタンスレベルで共有
* Executor のタイプ：[Docker Machine executor](https://docs.gitlab.com/runner/executors/#docker-machine-executor) 
    * オートスケールされた Docker エンジン上でジョブが実行されます。


## Kubernetes クラスタ（アプリのデプロイ環境）
* インスタンスレベルで GitLab インスタンスと連携
* Base domain：ci.workshop.gitlabtraining.cloud
* Kubernetes cluster name：workshop-gitlab-ci-cluster