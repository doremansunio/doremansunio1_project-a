# Lab6 - 品質とセキュリティスキャン
## .gitlab-ci.yml

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository -Dcheckstyle.skip=true"

stages:
  - pre build
  - build
  - test
  - post build
  - release

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
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH || $CI_COMMIT_TAG'

release:
  stage: release
  image: registry.gitlab.com/gitlab-org/release-cli:latest
  script:
    - echo running release_job for '$CI_COMMIT_TAG'
  release:
    name: 'Release $CI_COMMIT_TAG'
    description: 'Created using the release-cli for $CI_COMMIT_TAG'
    tag_name: '$CI_COMMIT_TAG'
    milestones:
      - $CI_COMMIT_TAG
    assets:
      links:
        - name: 'spring-petclinic jar'
          url: '$CI_API_V4_URL/projects/$CI_PROJECT_ID/packages/maven/org/springframework/samples/spring-petclinic/$CI_COMMIT_TAG/spring-petclinic-$CI_COMMIT_TAG.jar'
        - name: 'spring-petclinic pom'
          url: '$CI_API_V4_URL/projects/$CI_PROJECT_ID/packages/maven/org/springframework/samples/spring-petclinic/$CI_COMMIT_TAG/spring-petclinic-$CI_COMMIT_TAG.pom'
  rules:
    - if: $CI_COMMIT_TAG

include:
  - template: Jobs/Code-Quality.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Jobs/Code-Quality.gitlab-ci.yml
  - template: Security/Dependency-Scanning.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/Dependency-Scanning.gitlab-ci.yml
  - template: Security/License-Scanning.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/License-Scanning.gitlab-ci.yml
  - template: Security/SAST.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/SAST.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/Secret-Detection.gitlab-ci.yml
```


[Lab7](../lab7/README.md)