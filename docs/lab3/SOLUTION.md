# Lab3 - テストの実行
## .gitlab-ci.yml

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository -Dcheckstyle.skip=true"

stages:
  - pre build
  - build
  - test

download-m2cache:
  stage: pre build
  image: alpine:3.14.1
  script:
    - apk --no-cache add curl
    - curl -L -o m2.zip https://workshop.gitlabtraining.cloud/api/v4/projects/688/jobs/artifacts/main/download?job=mvn%20package
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
```

[Lab4](../lab4/README.md)