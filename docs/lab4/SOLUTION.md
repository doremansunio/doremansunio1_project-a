# Lab4 - ジョブの条件設定
## .gitlab-ci.yml

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository -Dcheckstyle.skip=true"

stages:
  - pre build
  - build
  - test
  - post build

download-m2cache:
  stage: pre build
  image: alpine:3.14.1
  script:
    - apk --no-cache add curl
    - curl -L -o m2.zip "https://workshop.gitlabtraining.cloud/api/v4/projects/sample-project-templates%2flocal-cache-for-basic-cicd-workshop/jobs/artifacts/main/download?job=mvn-package"
    - if [ -e ./m2.zip ]; then unzip m2.zip; fi
  artifacts:
    paths:
      - ./.m2/

mvn-package:
  stage: build
  image: maven:3.8.1-openjdk-17-slim
  script:
    - ./mvnw package -Dmaven.test.skip=true

unit-test:
  stage: test
  image: maven:3.8.1-openjdk-17-slim
  script:
    - ./mvnw test
  artifacts:
    when: always
    paths:
      - target/surefire-reports/TEST-*.xml
    reports: 
        junit:
          - target/surefire-reports/TEST-*.xml
  needs: 
    - download-m2cache

mvn-deploy:
  stage: post build
  image: maven:3.8.1-openjdk-17-slim
  before_script:
    - apt update && apt install git -y
  script:
    - ./mvnw deploy -s ci_settings.xml -Dmaven.test.skip=true
  rules:
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
```

[Lab6](../lab5/README.md)