# Lab4 - パッケージレジストリへの登録

## 目次
[[_TOC_]]

## 目的
* GitLab の CI ジョブからパッケージレジストリへ登録するための一例を体験します。
* CI ジョブの中で GitLab API を利用して GitLab パッケージレジストリへアクセスする際に、CI ジョブ実行中のみで有効な [GitLab CI/CD job token](https://docs.gitlab.com/ee/api/index.html#gitlab-cicd-job-token) を利用して認証できることを理解します。
* `.gitlab-ci.yml` 以外にもいくつかの設定ファイルも追加、修正する必要があるため、基本的には課題のコピペで進めてください。

## ハンズオンの課題
### イシューとマージリクエストの作成
イシューのタイトルを “Add maven deploy job” とし、
[Lab1 のイシューとマージリクエストの作成](../lab1/README.md#イシューとマージリクエストの作成) を参照して同様に操作します。

### Maven Deploy のジョブの追加
ステージ `test` の後に実行されるジョブ `mvn-deploy` を追加します。

1. マージリクエストのページで、[Web IDE で開く] をクリックします。
1. Web IDE の左側のソースコードのツリーから [.gitlab-ci.yml] をクリックし、以下を `.gitlab-ci.yml` に追記します。

* 追加ジョブの定義

```yaml
mvn-deploy:
  stage: post build
  image: maven:3.8.1-openjdk-17-slim
  before_script:
    - apt update && apt install git -y
  script:
    - ./mvnw deploy -s ci_settings.xml -Dmaven.test.skip=true
```

  * （スキップ可）周辺の解説
    * `./mvnw deploy` は Maven でビルドした jar ファイル等を Maven のリモートリポジトリへ登録するためのコマンドです。「デプロイ」よりも「デリバリー」や「リリース」の方が感覚的に違いかもしれません。
    * Maven Deploy の処理の中で git コマンドが必要なため、git コマンドをインストールしています。

### ステージの追加
テスト済みのソースコードのみをリモートリポジトリへ登録するようにするため、ステージ `test` の後にジョブが実行されるようにします。
1. `stages:` の定義が以下になるように、`post build` というステージを `test` の後に追加します。

```yaml
stages:
  - pre build
  - build
  - test
  - post build #この行を追加
```

### ci_settings.xml の追加
GitLab の Maven 用のパッケージレジストリ（Maven リポジトリ）へアクセスするために、GitLab CI/CD パイプラインのジョブで一時的に発行される [GitLab CI/CD job token](https://docs.gitlab.com/ee/api/index.html#gitlab-cicd-job-token) を使用し、GitLab APIの設定を実施します。

以下の内容で、新規に `ci_settings.xml` をリポジトリ直下のディレクトリに追加し、環境変数 `$CI_JOB_TOKEN` を Maven の設定ファイルで渡します。
1. 左側のソースコードのツリーの上部の [新規ファイル] のアイコン :bookmark_tabs: をクリックします。
1. [名前] に "ci_settings.xml" を入力し、[ファイルを作成] をクリックします。
1. 以下の xml の内容を貼り付けます。

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd">
  <servers>
    <server>
      <id>gitlab-maven</id>
      <configuration>
        <httpHeaders>
          <property>
            <name>Job-Token</name>
            <value>${env.CI_JOB_TOKEN}</value>
          </property>
        </httpHeaders>
      </configuration>
    </server>
  </servers>
</settings>
```
### pom.xml の修正
GitLab の Maven リポジトリをリモートリポジトリとして登録するための設定を追加します。
1. 左側のソースコードのツリーからリポジトリ直下のディレクトリの `pom.xml` をクリックし、以下のように修正します。

* Maven Deploy のエラーを回避する233行目付近に `<useNativeGit>true</useNativeGit>` を追加します。
```xml
...
        <configuration>
          <verbose>true</verbose>
          <dateFormat>yyyy-MM-dd'T'HH:mm:ssZ</dateFormat>
          <generateGitPropertiesFile>true</generateGitPropertiesFile>
          <generateGitPropertiesFilename>${project.build.outputDirectory}/git.properties
          </generateGitPropertiesFilename>
          <failOnNoGitDirectory>false</failOnNoGitDirectory>
          <failOnUnableToExtractRepoInfo>false</failOnUnableToExtractRepoInfo>
<!-- ここから -->
          <useNativeGit>true</useNativeGit>
<!-- ここまで -->
        </configuration>
...
```        
* 299行目付近の Maven リポジトリの設定について、以下のように設定を追記します。
```xml
...
    <repository>
      <id>spring-milestones</id>
      <name>Spring Milestones</name>
      <url>https://repo.spring.io/milestone</url>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
<!-- ここから -->
    <repository> 
        <id>gitlab-maven</id>
        <url>${env.CI_API_V4_URL}/projects/${env.CI_PROJECT_ID}/packages/maven</url>
    </repository>
<!-- ここまで -->
  </repositories>

<!-- ここから -->
  <distributionManagement>
  <repository>
      <id>gitlab-maven</id>
    <url>${CI_API_V4_URL}/projects/${env.CI_PROJECT_ID}/packages/maven</url>
  </repository>
  <snapshotRepository>
      <id>gitlab-maven</id>
      <url>${CI_API_V4_URL}/projects/${env.CI_PROJECT_ID}/packages/maven</url>
  </snapshotRepository>
  </distributionManagement>
<!-- ここまで -->
  <pluginRepositories>
    <pluginRepository>
...
```
### ソースコードのコミット
[Lab1 の .gitlab-ci.yml のコミット](../lab1/README.md#ソースコードのコミット) を参照して同様に操作します。

### パイプライン実行結果の確認
1. マージリクエストのページのパイプラインが成功したら、左側メニューの [パッケージとレジストリ] > [パッケージとレジストリ] をクリックします。
1. パッケージレジストリのページで、[org/springframework/samples/spring-petclinic] (2.4.5) が Maven リポジトリのビルドパッケージとして登録されていることを確認し、タイトルをクリックします。 
1. [org/springframework/samples/spring-petclinic] のページの下方より、`spring-petclinic-2.4.5.jar` がダウンロードできることを確認します。
1. マージリクエストのページへ戻ります。
1. [Mark as ready] ボタンをクリックします。
1. [マージ] ボタンをクリックし、マージリクエストをマージします。

# ハンズオンの解答
* [Lab4 解答](../lab4/SOLUTION.md)



